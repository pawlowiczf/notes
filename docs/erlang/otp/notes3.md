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

```title = "Emakefile"
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