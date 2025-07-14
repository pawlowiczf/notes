# Opis problemu
Tablica ETS jest niszczona, gdy jej właściciel (proces) zakończy się. Można tego uniknąć dodając, za wczasu, potomka tej tablicy (opcja `heir`) lub po prostu oddać prawo do własności za pomocą funkcji `ets:give_away/3`. 

Chciałem zrobić drzewo nadzorcy z procesem, który wykorzystuje tabelę i zasymulować błąd - wtedy tabela zostanie przesłana do stabilnego, drugiego procesu, który będzie ją tylko przechowywać. Gdy proces pierwszy zostanie zrestartowany, poprosi o tablicę, i dostanie ponownie prawa do własności.

Przykładowa funkcja do symulowania błędu to `ets_worker:crash` - po jej wywołaniu proces się ładnie zatrzymuje, wszystko działa super. Problem jest gdy pojawi się błąd, który nagle, natychmiast crashuje cały proces. Wtedy już supervisor, restarty, przekazywanie własności nie działa. 

# Kod 

```erlang title="ets_worker.erl"
-module(ets_worker).
-compile(export_all).

-behaviour(gen_server).

start_link() ->
    gen_server:start_link({local, ?MODULE}, ?MODULE, [], []).

crash() ->
    gen_server:cast(?MODULE, crash).

insert_data() ->
    gen_server:cast(?MODULE, {add, {1, "Filip", "Kowalski", 14, 191}}),
    gen_server:cast(?MODULE, {add, {2, "Andrzej", "Mariakcki", 16, 167}}),
    gen_server:cast(?MODULE, {add, {3, "Karol", "Nowak", 21, 205}}),
    ok.

add_person(Person) ->
    gen_server:cast(?MODULE, {add, Person}).

delete_person(Id) when is_integer(Id) ->
    gen_server:cast(?MODULE, {delete, Id}).

lookup_person(Id) ->
    gen_server:call(?MODULE, {lookup, Id}).

%% GEN_SERVER handlers:

init([]) ->
    process_flag(trap_exit, true),

    io:format("Starting ets_worker...~n", []),
    {ok, undefined, {continue, claim_ets}}.


handle_continue(claim_ets, _State) ->
    EtsTabNameResult = case ets_heir:claim_ets() of 
        {error, no_table} ->
            ets:new(
                db, 
                [named_table, set]
            ),
            ets:setopts(db, {heir, whereis(ets_heir), none}),
            db;

        {ok, EtsTabName} ->
            EtsTabName
    end,

    {noreply, EtsTabNameResult}.

handle_cast({add, Person}, EtsTabName) ->
    ets:insert(EtsTabName, Person),
    {noreply, EtsTabName};

handle_cast({delete, Id}, EtsTabName) ->
    ets:delete(EtsTabName, Id),
    {noreply, EtsTabName};

handle_cast(crash, EtsTabName) ->
    {stop, normal, EtsTabName}.

handle_call({lookup, Id}, _From, EtsTabName) when is_integer(Id) ->
    ResultSet = ets:lookup(EtsTabName, Id),
    {reply, ResultSet, EtsTabName}.

handle_info({'ETS-TRANSFER', EtsTabName, _FromPid, claim}, EtsTabName) ->
    io:format("Received ETS-TRANSFER from ets_heir ~n", []),
    {noreply, EtsTabName}.

% terminate(shutdown, EtsTabName) ->
%     io:format("Terminating ets_worker... from supervisor command ~n", []),
%     ets:setopts(EtsTabName, [{heir, none}]);

terminate(Reason, _EtsTabName) ->
    io:format("Terminating ets_worker. Reason: ~p ~n", [Reason]).
```

```erlang title="ets_heir.erl"
-module(ets_heir).
-compile(export_all).

-behaviour(gen_server).

%% State - map containing two fields:
%% status - one of: undefined, claimed
%% etsTabName - ets table name  

claim_ets() ->
    gen_server:call(?MODULE, claim).


start_link() ->
    gen_server:start_link({local, ?MODULE}, ?MODULE, [], []).

init([]) ->
    process_flag(trap_exit, true),

    io:format("Starting ets_heir...~n", []),
    {ok, #{status => undefined}}.

handle_call(claim, _From, #{status := undefined} = State) ->
    {reply, {error, no_table}, State};

handle_call(claim, _From, #{etsTabName := EtsTabName, status := claimed} = State) ->
    ets:give_away(EtsTabName, whereis(ets_worker), claim),
    {reply, {ok, EtsTabName}, State#{status => undefined, etsTabName => undefined}}.

handle_info({'ETS-TRANSFER', EtsTabName, _FromPid, none}, State) ->
    io:format("Received ETS-TRANSFER from ets_worker ~n", []),
    {noreply, State#{etsTabName => EtsTabName, status => claimed}}.

terminate(Reason, #{etsTabName := EtsTabName, status := claimed} = _State) ->
    io:format("Terminating ets_heir. EtsTabName: ~p. Reason: ~p ~n", [EtsTabName, Reason]);

terminate(_Reason, #{status := undefined} = _State) ->
    io:format("Terminating ets_heir. No ets table", []);

terminate(Reason, _State) ->
    io:format("Terminating ets_heir. Reason: ~p ~n", [Reason]).
``` 

```erlang title="ets_sv.erl"
-module(ets_sv).
-compile(export_all).

-behaviour(supervisor).

init(_) ->
    SupFlags = #{
        strategy => one_for_one,
        intensity => 3,
        period => 10
    },

    ChildSpec1 = #{
        id => 1,
        start => {ets_heir, start_link, []},
        modules => [ets_heir],
        restart => permanent, 
        type => worker,
        shutdown => 3000
    },

    ChildSpec2 = #{
        id => 2,
        start => {ets_worker, start_link, []},
        modules => [ets_worker],
        restart => permanent, 
        type => worker,
        shutdown => 3000
    },

    {ok, {SupFlags, [ChildSpec1, ChildSpec2]}}.

start_link() ->
    supervisor:start_link({local, ?MODULE}, ?MODULE, []).
```