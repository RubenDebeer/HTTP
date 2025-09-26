# HTTP Clients
A HTTP client is just something that makes HTTP requests.

## What 
## Why 
## How

# JSON
(JavaScript Object Notation), is a standard for representing structured data based on JavaScript's object syntax. It is commonly used to transmit data in web apps via HTTP.

JSON supports the following primitive data types:
- Strings, e.g. "Hello, World!"
- Numbers, e.g. 42 or 3.14
- Booleans, e.g. true
- Null, e.g. null

And the following collection types:

Arrays, e.g. [1, 2, 3]
Object literals, e.g. {"key": "value"}

JSON is similar to JavaScript objects and Python dictionaries. Keys are always strings, and the values can be any data type, including other objects.

This is valid JSON:

``` json
{
    "movies": [
        {
            "id": 1,
            "title": "Iron Man",
            "director": "Jon Favreau",
            "favorite": true
        },
        {
            "id": 2,
            "title": "The Avengers",
            "director": "Joss Whedon",
            "favorite": false
        }
    ]
}
```
### Decoding JSON IN Golang
When you receive JSON data in the body of an HTTP response, it comes as a stream of bytes. We can just convert the bytes to a string... but in Go there's a better way. It's typically best to decode the JSON data into a struct.
```json
[
  {
    "id": "001-a",
    "title": "Unspaghettify code",
    "estimate": 9001
  }
]
```
To decode this JSON into a **slice** of **Issue** **structs**, we need to know the JSON fields and their types. The standard `encoding/json` package uses tags to map JSON fields to struct fields.

```go
type Issue struct {
    ID       string `json:"id"`
    Title    string `json:"title"`********
    Estimate int    `json:"estimate"`
}
```
after we recieve the resonse we can decode it into the slice of `issues` using the "address of" operator `&`
```go

// res is a successful `http.Response`
****
var issues []Issue
decoder, err := json.NewDecoder(res.Body)
if err := decoder.Decode(&issues); err != nil {
    fmt.Println("error decoding response body")
    return
}
```

if all goes well and we don't have errors we can just slice the values from the decoder
```go 
for _, issue := range issues {
    fmt.Printf("Issue – id: %v, title: %v, estimate: %v\n", issue.Id, issue.Title, issue.Estimate)
    // Issue – id: 001-a, title: Unspaghettify code, estimate: 9001
}
```

### Reviewing JSON
JSON is a stringified representation of a JavaScript object, which makes it perfect for saving to a file or sending in an HTTP request. Remember, an actual JavaScript object is something that exists only within your program's variables. If we want to send an object outside our program, for example, across the internet in an HTTP request, we need to convert it to JSON first.

### Serializzation & De-Serialization (Marchaling & Demarshaling)
We can decode JSON bytes (or strings) into a Go struct using json.Unmarshal or a json.Decoder.

The Decode method of `json.Decoder` streams data from an `io.Reader` into a Go struct, while `json.Unmarshal` works with data that's already in` []byte` format. Using a `json.Decoder` can be more memory-efficient because it doesn't load all the data into memory at once. json. Unmarshal is ideal for small JSON data you already have in memory. When dealing with HTTP requests and responses, you will likely use `json.Decoder` since it works directly with an `io.Reader`.

JSON --> Struct
```go 
// res is an http.Response
    defer res.Body.Close()

    data, err := io.ReadAll(res.Body)
    if err != nil {
        return nil, err
    }

    var issues []Issue
    if err := json.Unmarshal(data, &issues); err != nil {
        return nil, err
    }
```

Cool this is Unmarshaling now how about marshaling or Serializing the data. We can achive this by running the `json.Marshal` function, this function converts the Go struct into a slice of bytes representing JSON data.

Struct --> JSON
```go
    type Board struct {
        Id       int    `json:"id"`
        Name     string `json:"name"`
        TeamId   int    `json:"team"`
        TeamName string `json:"team_name"`
    }

    board := Board{
        Id:       1,
        Name:     "API",
        TeamId:   9001,
        TeamName: "Backend",
    }

    data, err := json.Marshal(board)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println(string(data))
    // {"id":1,"name":"API","team":9001,"team_name":"Backend"}

```
Ok awe what about if you don't know the response type of the HTTP request ? Like you don't know how to model it into structs ?
In this case `map[string]interface{}` is a map where the key is a string and the value can be any Go type.

```go 
    var data map[string]interface{}
    jsonString := `{"name": "Alice", "age": 30, "address": {"city": "Wonderland"}}`
    json.Unmarshal([]byte(jsonString), &data)
    fmt.Println(data["name"])
    fmt.Println(data["address"].(map[string]interface{})["city"])
```
# URI's
A URI, or Uniform Resource Identifier, is a unique character sequence that identifies a resource that is (almost always) accessed via the internet.

Just like Golang has syntax rules, so do URIs. These rules help ensure uniformity so that any program can interpret the meaning of the URI in the same way.

URI's come in two ain types : 
- URLs
- URNs
- 
## URL Construction 
TODO: Add Image
There are 8 main parts of a URL: 

| Part | Required |
| ----- | ----- |
| Protocol | Yes |
| Username | No |
| Password | No |
| Domain | Yes |
| Port | No (defaults to 80 or 443\) |
| Path | No (defaults to /) |
| Query | No |
| Fragment | No |

### Protocol 
TODO: Image 
### Port
TODO: Explain Why ports are needed and add Image 
### Paths
TODO: explain Why it's needed and add image
### Query

# Headers 
# Methods
# Paths
# HTTPS
# Errors