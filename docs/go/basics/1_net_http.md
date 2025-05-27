# Net/HTTP library

## Funkcje handlujące
Aby odpowiadać na zapytania, musimy stworzyć funkcje handlującą i przypisać ją do ścieżki. Używamy `http.HandleFunc`, która przyjmuje ścieżkę oraz funkcję o specjalnej sygnaturze. 
```go
func d(res http.ResponseWriter, req *http.Request) {
    io.WriteString(res, "dog dog dog")
}

http.HandleFunc("/dog", d)
http.ListenAndServe(":8080", nil)
```

Możemy też użyć funkcji *http.Handle*, podobnej do `http.HandleFunc`. Ta funkcja przyjmuje także ścieżkę oraz obiekt typu **Handler**, który realizuje specjalny interface. Można użyć funkcji `http.HandlerFunc`, która jako argument przyjmuje funkcję o specjalnej sygnaturze i zwraca obiekt typu `Handler`. 
```go
func c(res http.ResponseWriter, req *http.Request) {
	io.WriteString(res, "cat cat cat")
}

func main() {
	http.Handle("/dog", http.HandlerFunc(d))
	http.ListenAndServe(":8080", nil)
}    
```

## Wysyłanie zapytania
Wysłanie zapytania składa się z części:
1. Utworzenie nowego zapytania:
```go
url := "https://api.example.com/data"
req, err := http.NewRequest("GET", url, nil)
req.Header.Set("Content-Type", "application/x-www-form-urlencoded")
```
2. Utworzenie klienta i wysłanie zapytania:
```go
client := &http.Client{}
resp, err := client.Do(req)
```
Powyższy sposób jest ogólny. Możemy tak wysłać dowolne zapytanie. Jeśli jednak chcemy ograniczyć się tylko do GET lub POST, możemy to zrobić tak:
```go
func (c *Client) Get(url string) (resp *Response, err error
func (c *Client) Post(url, contentType string, body io.Reader) (resp *Response, err error)
```

## Odbieranie zapytania POST
Odbierając zapytanie mamy dostęp do wskaźnika do struktury `*http.Request`. Należy wtedy wykonać odpowiednie funkcje: 
<div style="text-align: center;">
  <code>req.ParseForm()</code> – parsuje dane formularza przesłane daną metodą (POST) <br>
  <code>req.PostForm.Get("name")</code>
</div>

!!! danger "Uwaga"
    Zawsze należy wykonać komendę:
    ```go
    defer resp.Body.Close()
    ```

## ServeMux, method based routing

Można tworzyć drzewa multiplekserów, które będą handlować zapytaniami. 
```go
router := http.NewServeMux() 
subrouter := http.NewServeMux() 
subrouter.HandleFunc(...)
```
Teraz, aby dodać rodzinę adresów dla podroutera dla danego prefiksu wywołujemy na glównym routerze:
`router.Handle("/api/", http.StripPrefix("/api", subrouter))`
Zauwazmy, ze `http.ServeMux` implementuje interfejs `Handler`. 

!!! note info "Informacja"
    Od wersji 1.22 możemy definiować funkcje handlujące na danego rodzaju zapytania:
    ```go
    router.HandleFunc("POST /api", ...) 
    ```