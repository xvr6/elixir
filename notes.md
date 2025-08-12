# Notes

- `iex \n h`

## Types

- `1_000_000` -> still `1000000`
  - Math on ints always returns ints, division natively returns floats.
  - Call div(x,y) to get int back
- String concatenation - need to use `"`
  - `"hello" <> "world"`
  - `"${var1} #{var2}"`
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
> [1,2,3,4]
list2
> [1,2,3]
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

## Enum

- A list is an Enumerable