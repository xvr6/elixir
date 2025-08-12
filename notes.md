# Notes

- `iex \n h`

## Types

- `1_000_000` -> still `1000000`
  - Math on ints always returns ints, division natively returns floats.
  - Call div(x,y) to get int back
- String concatenation - need to use `"`
  - `"hello" <> "world"`
  - `"#{var1} #{var2}"`
- Atoms <- basically a named constant
  - Should be used when a string is reused - more memory efficient
  - :atom
  - :"atom with space"
  - :"atom_with_space"
- Booleans:
  - true, false, nil
  - under the hood they are just atoms
  - `true == :true`, `false == :false`, `nil == :nil`
  - therefor `is_atom(true)` is true  
- Short circut operators (does work as expected, but took notes just in case)
  - || && !
  - falsy values:
    - false, nil
    - `nil || false || "hi"` returns "hi"
      - returns first truthy value
  - all other values are truthy
    - `true && 9.0 && false` returns `false`
    - `true && 9.0 && nil` returns `nil`
    - `true && 9.0 && :hi` returns `:hi`
    - returns first falsy value

## Functional Programming and Immutability

- in elixir we have basic datatypes and transformations of that datatype
- functions must be within a module (`Module.function()`)
  - Note: when runing `iex> h (Module)` it will return `<function>[!/?]/#`; ex: `chmod!/2`
    - `#` indicates expected params
    - if `!` is present, the function will raise an error if it occurs. If not it will usually return a touple in the format of `{:error, reason}`
    - if `?` is present, it will return a boolean
  - One module is always imported - `Kernel`
  - `String.upcase("hi")`
  - for javascript, types/primitives are treated as Objects. This is not the case in elixir
    - js `"hi".toUpperCase()`
    - ex `"hi" |> String.upcase()` or `String.upcase("hi")`
- In traditional OOP languages, one can never be sure if something is mutating the og variable or creating a new one; causes confusion.
  - traditional oop mutability:
    - js `const name = "xavier"` <- `name.toUpperCase()` produces a copy that is upper case, no change to og name variable
    - js `const arr = [1,2,3]`   <- `arr.push(4)` changes arr to now be `[1,2,3,4]`
  - in elixir mutability does not exist:
    - ex `list = [1,2,3]`        <- `list ++ [4, 4_000]` returns a new list with the new entires appended to it, the first one being unmodified
  - if one wants to change the original variable, you must reassign:
    - ex `list = list ++ [70]` returns `[1,2,3,70]`
  - no mutability means no issues with deep/shallow copy (by value or by copy)
  - you cannot copy by reference, only by value.

```elixir
list = [1,2,3]
list2 = list 
list ++ [4]

list
"""
> [1,2,3,4]
"""
list2
"""
> [1,2,3]
"""
```

## Lists

- Lists are singly linked lists
- `list = [1,2,3]` is stored in memory as:
  - `[1, addr2] -> [2, addr3] -> [3, <no address saved>]`
  - not saved sequentially in memory
  - you cannot get lists by element
  - `list[2]` does not work; must traverse entire list to get the value at index 2
- To append to the beginning:
  - `[0] ++ list` - **remember this makes a new list, does not mutate the first**
  - cheap, simply gets first memory addr
- To append to end:
  - `list ++ [4]`
  - expensive operation, must traverse the entire length to append
- To remove entries:
  - `list -- [2]` removes 2 from list
- Get the Head of a list
  - `hd list` or `hd(list)` - part of Kernel, so technically `Kernel.hd(list)`
- Tail of a list - the rest of a list once the head is removed
  - `tl list` or `tl(list)`
  - To get the explicit last element, you can do `List.last(list)`
    - This is computationally expensive

## Enum module

- Called to deal with any enumerables, including lists!
- `Enum.concat(list1, list2)` works the same as `list1 ++ list2`

## Tuple Module

- Dataset used to save a fixed number of elmeents
- no operations can be done to a tuple
- They are saved sequentially in memory
  - `elem({"xavier","xvr"},1)` returns `"xvr"`; elem is part of Kernel module.
- Tuples are traditionall returned for functions
  - `File.read "test.txt"` returns `{:ok, <content>}`
  - if file is unable to be read, returns `{:error, <some sort of status code or error message>}`
  - if `File.read! "test.txt"` is called and test.txt doesn't exist, an error will be raised

## Keyword Lists

- A way of passing optional parameters to a fcn.
- `String.split("1 2 3", " ")` returns `["1", "2", "3"]`
- Say we have a string with a lot of extra spaces:
  - `String.split("1               2 3", " ")` returns `["1", "", <...>, "", "2", "3"]
  - instead, we use a keyworded list as a pram: `String.split("1               2 3", " ", trim: true)`. This returns `["1", "2", "3"]`
- A keyword list is a tuple where the first element must be an atom
  - `[{:trim, true}, {:param2, true}]` <- basically an object
  - can be passed into a fcn this way as well:
    - `String.split("1               2 3", " ", [{:trim, true}])` or `String.split("1               2 3", " ", [trim: true])`
  - if the last element of a function is a keyword list, you can just remove it as shown in the og example
    - `String.split("1               2 3", " ", trim: true)`
- Example, DB querying
  - `query = from(user in User, where: user.age > 18)`
  - again, can be `[where: user.age > 18]` or `[{:where, user.age > 18}]`
- This can be used for functions, typically the final variable and named `opts`
  - Can then use `Keyword.validate()` to ensure the keyword list is valid; assign defaults with this as well.
  - things can be retrieved by calling `opts[:key]`
  - Also can be retrieved with `Keyword.get(:key)`

## Map Module

```elixir
"""
ugly syntax
"""
map = %{"name" => "xavier", :age => 22, 1 => false}
```

- values can be retrieved by `map[<key>]`

```elixir
# better syntax
map = %{:name => "xavier", :age => 22}
#> map = %{name: "xavier", age: 22}

# output is defaulted to the better syntax if all keys are atoms.
map = %{name: "xavier", age: 22}
#> map = %{name: "xavier", age: 22}
```

- if a key is an atom, can be accessed by: `map.<key>` like a regular js object
  - This raises an error if the key is invalid
  - A way around this is with `Map.get(map, <key>)` which returns either the value at that key or `nil`
  - for defaults, simply add a third param: `Map.get(map, <key>, <default> \\ nil)` 
    - `\\` implies optional. Anything after the `\\` is the default value for that param.
- To add values to a map:
  - `Map.put(map, <new_key>, <value>)`; again this does not modify the og map value and a new variable must be reassigned.
- To edit a value in a map:
  - `&{map | age: 100}`
