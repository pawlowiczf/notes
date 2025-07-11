## Exits

W `gen-server` ustawienie opcji `process_flag(trap_exit, true)` powoduje, że sygnały `Exit` z procesu rodzica (np. supervisora), konwertowane są na wiadomości, które następnie należy obsłużyć w funkcji `handle_info`, np. 
```erlang
handle_info({'EXIT', FromPid, Reason}, State) ->
    io:format("Otrzymano sygnał wyjścia od ~p z powodu: ~p~n", [FromPid, Reason]),
    io:format("Podejmuję akcję, np. restartuję proces...~n"),
    {noreply, State};
```


terminate/2:     
If the gen_server process is part of a supervision tree and is ordered by its supervisor to terminate, this function is called with Reason=shutdown if the following conditions apply:

* The gen_server process has been set to trap exit signals.
* The shutdown strategy as defined in the child specification of the supervisor is an integer time-out value, not brutal_kill.

Even if the gen_server process is not part of a supervision tree, this function is called if it receives an 'EXIT' message from its parent. Reason is the same as in the 'EXIT' message.

!!! info 
    if any reason other than `normal`, `shutdown` or `{shutdown, Term}` is used when `terminate/2` is called, the OTP framework will see this as a failure and start logging a bunch of stuff here and there for you.