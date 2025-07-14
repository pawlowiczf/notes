## Exits

Sygnały EXIT, od procesów innych niż rodzic, można obsługiwać w funkcji `handle-info`.
```erlang
handle_info({'EXIT', FromPid, Reason}, State) ->
    io:format("...", []),
    {noreply, State};
```

Jeśli `gen_server` jest częścią drzewa nadzorcy i dostał instrukcję zakończenia, funkcja `terminate/2` jest wywołana z powodem `Reason=shutdown`, gdy spełnione są następujące warunki:

- `gen_server` ustawił opcję `process_flag(trap_exit, true)`
- `shutdown` w specyfikacji dziecka jest dodanią liczbą całkowitą, a nie `brutal_kill` 

Nawet jeśli `gen_server` nie jest częścią drzewa, to funkcja `terminate/2` jest wywołana, jeśli otrzyma wiadomość `EXIT` od swojego rodzica. Powód jest wtedy taki sam, jak ten w wiadomości `EXIT`. 

!!! note danger ""  
    if any reason other than `normal`, `shutdown` or `{shutdown, Term}` is used when `terminate/2` is called, the OTP framework will see this as a failure and start logging a bunch of stuff here and there for you.

Przykładowy supervisor:
```erlang
-behaviour(supervisor).

init(_) ->
    SupFlags = #{
        strategy => one_for_one,
        intensity => 2
    },

    ChildSpec = #{
        id => 1,
        start => {people_db, start_link, []},
        modules => [people_db],
        restart => permanent, 
        type => worker,
        shutdown => 3000
    },
    {ok, {SupFlags, [ChildSpec]}}.

start_link() ->
    supervisor:start_link({local, ?MODULE}, ?MODULE, []).
```

