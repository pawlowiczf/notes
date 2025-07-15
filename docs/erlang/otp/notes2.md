
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