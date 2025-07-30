# Struktura folderów

W folderze źródłowym, mamy następujące foldery:
```
ebin/
include/
priv/
src/
    - app_sup.erl
    - ...
test/
```

Folder `/ebin` przechowuje skompilowane pliki, `include/` pliki nagłówkowe `.hrl`, `/priv` pliki wykonywalne i inne, a `src/` pliki źródłowe. 

# Emakefile 

Aby uprościć pracę z wieloma plikami, można dodać plik `Emakefile`, który informuje kompilator, w jaki sposób kompilować i gdzie umieszczać pliki.

```erlang title="Emakefile"
{'src/*', [
    debug_info,
    {i, "src"},
    {i, "include"},
    {outdir, "ebin"}
]}.
```

Kompilator będzie szukać plików w katalogu `src/` i `include/`, a umieści je w `ebin/`. 

```erlang
erl -make 
erl -pa ebin/

erl
    make:all([load])
```

# Plik aplikacji 

Trzeba też dodać plik aplikacji. Umieszcza się w katalogu i nazywa: 
- katalog `ebin/`, nazwa `<yourapp>.app`
- katalog `src/`, nazwa `<myapp>.app.src` 

Struktura pliku jest następująca:
```erlang
{application, ppool, [
    {vsn, "1.0.0"},
    {modules, [ppool, ppool_serv, ppool_sup, ppool_supersup, ppool_worker_sup]},
    {registered, [ppool]},
    {mod, {ppool, []}},
    {env, [{Key, Val}]}
]}.
```