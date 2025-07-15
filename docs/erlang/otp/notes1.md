## Sygnały, przechwytywanie zakończenia

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
    Jeśli funkcja `terminate/2` zostanie wywołana z innym błędem niż `normal`, `shutdown`, `{shutdown, Term}`, framework OTP uzna to za porażkę i będzie printować troszkę więcej logów/opisów błędu. 




