1. Go: Fibonacci Sequence Rate Limiter

A fibonacci with modulo sequence is defined for a given modulo,
mod, as follows:

    • fib[xJ = (fib[x - 1] + fib[x - 2)) % mod, x > 2
    • fib[1] = 1, fib[2) = 2

[1, 2, 3, 5, 8, 13, 21 ...J are the first few terms of the Fibonacci
sequence. In this task, you are required to write a function that runs
a server that generates a Fibonacci sequence.

It accepts boolean requests and returns the next number in the
sequence. It should have rate limiter which should make the server
send every response no earlier than 10 milliseconds after the
previous one. The server should be created with two arguments - a
boolean channel that accepts requests and a result int channel
through which every result will be returned. The main function will
also accept two arguments: the number of results that will be
skipped from the beginning, and the number of results that will be
printed to the console.

Note: Since the numbers can be large, the server must return the
number modulo 109. For example, for the 50th Fibonacci number -
12586269025, the server should return 586269025.

For example, using the arguments 2 and 5 results in [3, 5, 8, 13, 21].

Function Description
Complete the function ModuloFibonacciSequence in the editor
below. The function will return nothing as all its results will be
returned through a channel.

Modu/oFibonacciSequence has the following parameter(s):
requestChan: a channel of booleans (chan bool)
resultChan: a channel of integers (chan int)

Constraints
• 0 s skips 100
• 1 s totals 200

Input Format For Custom Testing

The first line contains an integer, skip, the number of elements
that should be skipped from the beginning of the Fibonacci
sequence.

The second line contains an integer, total, the number of elements that should be printed as a result of FibonacciServer working.

Sample Case O
Sample Input For Custom Testing
STDIN Function
0 skip = 0
6 total = 6

Sample Output
1
2
3
5
8
13

Explanation
0 means no elements will be skipped, so the sequence starts with
the first element, 1. The 6 elements in the sequence are [1, 2, 3, 5,
8, 13]. None of these is large enough for the modulo to change.

Sample Case 1
Sample Input For Custom Testing
50
4
Sample Output
316291173
267571272
583862445

Explanation
Skip the first 50 numbers in the sequence and print the modulo of the next 4 integers.

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "strconv"
    "strings"
    "time"
)


/*
 * Complete the 'ModuloFibonacciSequence' function below.
 *
 * The function accepts following parameters:
 *  1. chan bool requestChan
 *  2. chan int resultChan
 */

const modulo = 1000000000 // 10^9 as specified

func ModuloFibonacciSequence(requestChan chan bool, resultChan chan int) {
    var fib [2]int64 = [2]int64{1, 2} // Initial Fibonacci values fib[1] = 1, fib[2] = 2

    // Rate limit control
    rateLimit := time.Tick(10 * time.Millisecond)

    for {
        _, ok := <-requestChan
        if !ok { // If channel is closed, stop the goroutine
            close(resultChan)
            return
        }

        // Wait for rate limit tick before continuing
        <-rateLimit

        // Send the next Fibonacci number modulo 10^9
        resultChan <- int(fib[0] % modulo)

        // Calculate the next number in the sequence
        nextFib := (fib[0] + fib[1]) % modulo
        fib[0], fib[1] = fib[1], nextFib
    }
}
func main() {
    reader := bufio.NewReaderSize(os.Stdin, 16 * 1024 * 1024)

    skipTemp, err := strconv.ParseInt(strings.TrimSpace(readLine(reader)), 10, 64)
    checkError(err)
    skip := int32(skipTemp)

    totalTemp, err := strconv.ParseInt(strings.TrimSpace(readLine(reader)), 10, 64)
    checkError(err)
    total := int32(totalTemp)

    resultChan := make(chan int)
    requestChan := make(chan bool)
    go ModuloFibonacciSequence(requestChan, resultChan)
    for i := int32(0); i < skip + total; i++ {
        start := time.Now().UnixNano()
        requestChan <- true
        new := <-resultChan
        if i < skip {
            continue
        }
        end := time.Now().UnixNano()
        timeDiff := (end - start)/1000000
        if timeDiff < 3 {
            fmt.Println("Rate is too high")
            break
        }
        fmt.Println(new)
    }
}

func readLine(reader *bufio.Reader) string {
    str, _, err := reader.ReadLine()
    if err == io.EOF {
        return ""
    }

    return strings.TrimRight(string(str), "\r\n")
}

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}
```

2. Go: HTTP server

Implement an HTTP server that has three routes and maintains a
database of the world's largest lakes.
• The first route runs a handler postHand/erwhich accepts a POST request with JSON-encoded lake information and posts it to the database.

    {
    	"type": "post" ,
    	"payload": { "id": "id00", "name": "Name of the lake", "area": 452001}
    }

    • The second route runs a handler deleteHand/erwhich deletes the lake from the database by id. If the payload id is not present in the data set, the server returns a 404 response.
    {
    	"type": "delete" ,
    	"payload ": "id00"
    }
    • The third route runs a handler getHandlerwhich takes a lake from the database by id and returns it to the caller which prints the name and the area of the lake. If the id is not found in the database, the server returns a string message "404 Not Found". Otherwise, it returns the payload object corresponding to the id.
    {
    	"type": "get
    	"payload : "id00"
    }

Implement the server which will be queried by the program and
print the output to STDOUT.

Note:
The program uses these structs:
type Lake struct {
Name string
Area int32
}

    type Action struct {
    	Type string
    	Payload string
    }

The base URL is contained in the global variable address. Data is stored under the store variable which is the map[string] Lake.

Constraints
The total number of queries does not exceed 1000.
The name and id strings consist of no more than 100 lowercase and uppercase English characters only.
All the post ids are unique.

Input Format For Custom Testing
The first line contains an integer, n, the number of elements in
actions.

Each line i of the n subsequent lines (where 0 <= i < n) contains a string, action[i].

Sample Case O
Sample Input For Custom Testing
4
{type: 'post', payload: `{id: 'id000', name:'Malawi', area:2950}`}
{type: 'post', payload: `{id: 'id0001', name:'Great Bear Lake', area:31000}`}

{type: 'get', payload:'id0001'}
{type: 'get', payload: 'id0000'}
Sample Output
Great Bear Lake
31000
Malawi
29500

Explanation
The first two actions call postHandler and put two objects in the store. The 3rd and the 4th actions get items from the store by corresponding ids.

Sample Case 1
Sample Input For Custom Testing
5
{type: 'post', payload: `{id: 'id000', name:'Malawi', area:2950}`}
{type: 'post', payload: `{id: 'id0001', name:'Great Bear Lake', area:31000}`}
{type:'delete', payload:'id0000'}
{type: 'get', payload:'id0001'}
{type: 'get', payload: 'id0000'}
Sample Output
Great Bear Lake
31000
404 Not Found

Explanation
The first two actions call postHandler and put two objects in the store. The 3rd action deletes the object with id "id0000". The 4th
and the 5th actions get items from the store by corresponding ids, but since the object "id0000" was deleted, the 5th message leads to printing a "404 Not Found" error.

package main

import (
"bufio"
"encoding/json"
"fmt"
"io"
"net/http"
"os"
"strconv"
"strings"
"time"
)

/\*

- Complete the 'postHandler', 'deleteHandler' and 'getHandler' functions below.
-
- All functions are expected to be void.
- All functions accept http.ResponseWriter w and _http.Request req as parameters.
  _/

func postHandler(w http.ResponseWriter, req \*http.Request) {
if req.Method != http.MethodPost {
http.Error(w, "Method is not supported.", http.StatusNotFound)
return
}

    var lake Lake
    err := json.NewDecoder(req.Body).Decode(&lake)
    if err != nil {
        http.Error(w, err.Error(), http.StatusBadRequest)
        return
    }

    store[lake.Id] = lake
    w.WriteHeader(http.StatusOK)

}

func deleteHandler(w http.ResponseWriter, req \*http.Request) {
if req.Method != http.MethodDelete {
http.Error(w, "Method is not supported.", http.StatusNotFound)
return
}

    id := req.URL.Query().Get("id")
    if id == "" {
        http.Error(w, "ID is required", http.StatusBadRequest)
        return
    }

    if _, ok := store[id]; !ok {
        http.Error(w, "Not Found", http.StatusNotFound)
        return
    }

    delete(store, id)
    w.WriteHeader(http.StatusOK)

}

func getHandler(w http.ResponseWriter, req \*http.Request) {
if req.Method != http.MethodGet {
http.Error(w, "Method is not supported.", http.StatusNotFound)
return
}

    id := req.URL.Query().Get("id")
    if id == "" {
        http.Error(w, "ID is required", http.StatusBadRequest)
        return
    }

    lake, ok := store[id]
    if !ok {
        http.Error(w, "Not Found", http.StatusNotFound)
        return
    }

    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(lake)

}

func main() {
reader := bufio.NewReaderSize(os.Stdin, 16 _ 1024 _ 1024)

    stdout, err := os.Create(os.Getenv("OUTPUT_PATH"))
    checkError(err)

    defer stdout.Close()

    writer := bufio.NewWriterSize(stdout, 16 * 1024 * 1024)

    actionsCount, err := strconv.ParseInt(strings.TrimSpace(readLine(reader)), 10, 64)
    checkError(err)

    http.HandleFunc("/get", getHandler)
    http.HandleFunc("/post", postHandler)
    http.HandleFunc("/delete", deleteHandler)
    go http.ListenAndServe(portSuffix, nil)
    time.Sleep(100 * time.Millisecond)

    var actions []string

    for i := 0; i < int(actionsCount); i++ {
        actionsItem := readLine(reader)
        actions = append(actions, actionsItem)
    }

    for _, actionStr := range actions {
        var action Action
        err := json.Unmarshal([]byte(actionStr), &action)
        checkError(err)
        switch action.Type {
        case "post":
            _, err := http.Post(address + "/post", "application/json", strings.NewReader(action.Payload))
            checkError(err)
        case "delete":
            client := &http.Client{}
            req, err := http.NewRequest("DELETE", address + "/delete?id=" + action.Payload, nil)
            checkError(err)
            resp, err := client.Do(req)
            checkError(err)
            if resp.StatusCode != 200 {
                fmt.Fprintf(writer, "%s\n", resp.Status)
                continue
            }
        case "get":
            resp, err := http.Get(address + "/get?id=" + action.Payload)
            checkError(err)
            if resp.StatusCode != 200 {
                fmt.Fprintf(writer, "%s\n", resp.Status)
                continue
            }
            var lake Lake
            err = json.NewDecoder(resp.Body).Decode(&lake)
            checkError(err)
            fmt.Fprintf(writer, "%s\n", lake.Name)
            fmt.Fprintf(writer, "%d\n", lake.Area)
        }
    }

    fmt.Fprintf(writer, "\n")

    writer.Flush()

}

const portSuffix = ":3333"
var address = "http://127.0.0.1" + portSuffix

type Lake struct {
Id string `json:"id"`
Name string `json:"name"`
Area int32 `json:"area"`
}

type Action struct {
Type string
Payload string
}

var store = map[string]Lake{}

func readLine(reader \*bufio.Reader) string {
str, \_, err := reader.ReadLine()
if err == io.EOF {
return ""
}

    return strings.TrimRight(string(str), "\r\n")

}

func checkError(err error) {
if err != nil {
panic(err)
}
}

3. Go: Custom String Sorting
   Sort a set of strings based on the following factors: 1. An odd length string should precede an even length string. 2. If both strings have odd lengths, the shorter of the two should precede. 3. If both strings have even lengths, the longer of the two should precede. 4. If the two strings have equal lengths, they should be in alphabetical order.

Example
strArr 'ab', 'abcde', 'a', 'abcd']

The result is ['a', 'abc', 'abcde', 'abcd', 'ab']. Odd length words are sorted in ascending order of length followed by even length words
in descending order of length. There are no matching length words,
so no alphabetical sorting is required.

Function Description
Complete the function customSortingin the editor below.

customSorting has the following parameter:
string strArr[nj: an array of strings

Returns
string[n]: the sorted array

Constraints
• 1 <= length of strArrs <=1000
• 1 <= length of strArr[i] <= 100
• Each strArr[i] contains uppercase and lowercase English letters only.

Input Format For Custom Testing
The first line contains an integer, n, the number of elements in
strArr.
Each line i of the n subsequent lines (where Osi < n) contains a
string describing strArr[i].
Sample Case O
Sample Input For Custom Testing
STDIN Function
5 -> strArr[] size n =5
abc -> strArr = ['abc', 'ab', 'abcde', 'a','abcs']

ab
abcde
a
abcd

Sample Output
a
abc
abcde
abcd
ab

Explanation
The odd length strings come first, sorted ascending by length.
Even length words come last in descending length order.

Sample Case 1
SSample'npGt For Custom Testing
8
Mercury
Venus
Earth
Mars
Jupiter
Saturn
Uranus
Neptune

Sample Output
Earth
Venus
Jupiter
Mercury
Neptune
Saturn
Uranus
Mars

Explanation
The odd length strings come first, sorted ascending by length.
Even length words come last in descending length order. Multiple
words have similar lengths and they are. sorted alphabetically,
'Jupiter' 'Mercury' and 'Neptune' for example.

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "strconv"
    "strings"
)


/*
 * Complete the 'customSorting' function below.
 *
 * The function is expected to return a STRING_ARRAY.
 * The function accepts STRING_ARRAY strArr as parameter.
 */

func customSorting(strArr []string) []string {
    for i := 1; i < len(strArr); i++ {
        key := strArr[i]
        j := i - 1

        // Insert strArr[i] into the sorted sequence strArr[0..i-1]
        for j >= 0 && shouldSwap(strArr[j], key) {
            strArr[j+1] = strArr[j]
            j = j - 1
        }
        strArr[j+1] = key
    }
    return strArr
}

// shouldSwap returns true if a should be placed before b according to the custom rules
func shouldSwap(a, b string) bool {
    lenA, lenB := len(a), len(b)
    oddA, oddB := lenA%2 != 0, lenB%2 != 0

    // If one is odd and the other is even, the odd one should come first
    if oddA && !oddB {
        return false
    } else if !oddA && oddB {
        return true
    }

    // If both are odd or both are even
    if oddA && oddB {
        // If both have odd lengths, the shorter should come first
        if lenA != lenB {
            return lenA > lenB
        }
        // If both have the same length, sort alphabetically
        return a > b
    } else {
        // If both have even lengths, the longer should come first
        if lenA != lenB {
            return lenA < lenB
        }
        // If both have the same length, sort alphabetically
        return a > b
    }
}
func main() {
    reader := bufio.NewReaderSize(os.Stdin, 16 * 1024 * 1024)

    stdout, err := os.Create(os.Getenv("OUTPUT_PATH"))
    checkError(err)

    defer stdout.Close()

    writer := bufio.NewWriterSize(stdout, 16 * 1024 * 1024)

    strArrCount, err := strconv.ParseInt(strings.TrimSpace(readLine(reader)), 10, 64)
    checkError(err)

    var strArr []string

    for i := 0; i < int(strArrCount); i++ {
        strArrItem := readLine(reader)
        strArr = append(strArr, strArrItem)
    }

    result := customSorting(strArr)

    for i, resultItem := range result {
        fmt.Fprintf(writer, "%s", resultItem)

        if i != len(result) - 1 {
            fmt.Fprintf(writer, "\n")
        }
    }

    fmt.Fprintf(writer, "\n")

    writer.Flush()
}

func readLine(reader *bufio.Reader) string {
    str, _, err := reader.ReadLine()
    if err == io.EOF {
        return ""
    }

    return strings.TrimRight(string(str), "\r\n")
}

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

```
