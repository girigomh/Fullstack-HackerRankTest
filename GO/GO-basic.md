1. Go: Remainder Sorting
   Implement a function that receives an array of strings and sorts
   them based on the following heuristics:

• The primary sort is by increasing remainder of the strings' lengths,
modulo 3.
• If multiple strings have the same remainder, they should be sorted in
alphabetical order.

Example
strArr = ["Colorado", "Utah", "Wisconsin", "Oregon"]
Their lengths are [8, 4, 9, 6]. The remainders, modulo 3, are [2, 1, 0,
0]. Oregon and Wisconsin have the same remainder and are further
sorted alphabetically. Here is the sorted array showing (string,
length, length modulo 3): [('Oregon', 6, 0), ('Wisconsin', 9, 0), ('Utah',4, 1), ('Colorado', 8, 2)]. Return the sorted array ["Oregon", Wisconsin", "Utah", "Colorado"].

Function Description
Complete the function RemainderSortingin the editor below. The
function return an array of strings.
RemainderSorting has the following parameter(s):
strArr Injstring: an array of strings

Constraints
• 1 of strArr 700
• 1 of each string 100

Input Format For Custom Testing:
The first line contains an integer, n, denoting the number of
elements in strArr.

Each line i of the n subsequent lines (where Osi < n) contains a
string describing

Sample Case O
Sample Input For Custom Testing
STDIN Function
4 -> strArr[] size n = 4
a -> strArr = [ 'a', 'ab,', 'bc', 'abc']
ab
bc
abc

Sample Output
abc
a
ab
bc

Sample Case 1
Sample Input For Custom Testing
STDIN Function
6 -> strArr[] size n = 6
Colorado -> strArr = ['Colorado', 'Utah' ,'Wisconsin', 'Oregon' ,'Maine']
Utah
Montana
Wi sconsin
Oregon
Maine

Sample Output
Oregon
Wi sconsin
Montana
Utah
Colorado
Mai ne

Explanation
The following sorted array is in the form (string, length, length
modulo 3): [('Oregon', 6, 0), ('Wisconsin', 9, 0), ('Montana', 7, 1),
('Utah', 4, 1), ('Colorado', 8, 2), ('Maine', 5, 2)).

```GO
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "sort"
    "strconv"
    "strings"
)


/*
 * Complete the 'RemainderSorting' function below (and other code for sorting if needed).
 *
 * The function is expected to return a STRING_ARRAY.
 * The function accepts STRING_ARRAY strArr as parameter.
 */

func RemainderSorting(strArr []string) []string {
    sort.SliceStable(strArr, func(i, j int) bool {
        // Primary sort: sort by remainder of lengths when divided by 3
        modI := len(strArr[i]) % 3
        modJ := len(strArr[j]) % 3
        if modI != modJ {
            return modI < modJ
        }
        // Secondary sort: sort alphabetically if remainders are equal
        return strArr[i] < strArr[j]
    })
    return strArr
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

    result := RemainderSorting(strArr)

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

2. Go: String Operations

Implement a function that takes in a string and encrypts it using the
following algorithm:

1. Trim all spaces at the start and end of the string.
2. Remove all the digits from O to 9.
3. Reverse the string.

Note that the function should work with international symbols as
well.

Example

str = "de75s1 rev2er "

Strip the leading and trailing spaces, remove digits and reverse the
string to return "reversed".

Function Description
Complete the function ModifyString in the editor below.

ModifyString has the following parameter(s):
str: a string

Returns:
string: the processed string

Constraints
• 1 s length ofstrs310

Input Format For Custom Testing

The only line contains a string, str.
Sample Case O

Sample Input For Custom Testing
011123eH56
Sample Output
Hello

Explanation
Strip the leading and trailing spaces, remove digits and reverse
the string.

Sample Case 1
Sample Input For Custom Testing
another
Sample Output
rehtona

Explanation
There are no digits, so we just remove trailing spaces and reverse
the string

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "strings"
)


/*
 * Complete the 'ModifyString' function below and add imports if needed.
 *
 * The function is expected to return a STRING.
 * The function accepts STRING str as parameter.
 */

func ModifyString(str string) string {
    // Trim spaces
    str = strings.TrimSpace(str)

    // Remove digits
    str = removeDigits(str)

    // Reverse string
    str = reverseString(str)

    return str
}

// removeDigits removes all digits from a string.
func removeDigits(str string) string {
    var builder strings.Builder
    for _, char := range str {
        if char < '0' || char > '9' {
            builder.WriteRune(char)
        }
    }
    return builder.String()
}

// reverseString reverses a string.
func reverseString(s string) string {
    runes := []rune(s)
    for i, j := 0, len(runes)-1; i < j; i, j = i+1, j-1 {
        runes[i], runes[j] = runes[j], runes[i]
    }
    return string(runes)
}
func main() {
    reader := bufio.NewReaderSize(os.Stdin, 16 * 1024 * 1024)

    stdout, err := os.Create(os.Getenv("OUTPUT_PATH"))
    checkError(err)

    defer stdout.Close()

    writer := bufio.NewWriterSize(stdout, 16 * 1024 * 1024)

    str := readLine(reader)

    result := ModifyString(str)

    fmt.Fprintf(writer, "%s\n", result)

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
