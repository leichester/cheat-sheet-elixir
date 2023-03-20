# Elixir Cheat Sheet

```elixir
elixir -v
1.14.2
```

Elixir is a dynamic functional compiled language that runs over an Erlang Virtual Machine called BEAM.

Erlang and its BEAM is well known for running low-lattency, distributed and fault-tolerant applications.

Elixir was designed to take all that advantages in a modern coding language.

Elixir data types are immutable. The most complex nested list, the database record—these things behave just like the simplest integer. Their values are all immutable. Once a variable references a list such as `[1,2,3]`, you know it will always reference those same values (until you rebind the variable). Most compound data structures in most other programming languages are mutable—you can change all or part of their content.

In Elixir a function is usually described with its arity (number of arguments), such as: `is_boolean/1`.

## File Types

- `.exs` => Elixir script file
- `.ex` => Elixir regular file
- `.beam` => Compiled Elixir file

## Compile and Run Elixir code

- `elixir <script_file>.exs` => run a script file
- `elixirc <file>.ex` => compile a file to `Elixir.<File>.beam`

## Elixir Conventions

- `foo function return tuple` => the result of a `foo` function is usually `{:ok, result}` or `{:error, reason}`
- `foo! function may raise an error` => the result of a `foo!` is not wrapped in a tuple and it may raises an exception
- Exceptions/Errors are **not used** for controlling flow
- Elixir uses **fail fast** idea and the supervision trees to control process health and possible restart processes.
- Elixir identifiers must start with a letter or underscore, optionally followed by letters, digits, and underscores. The identifiers may end with a question mark or an exclamation mark. Module, record, protocol, and behavior names start with an uppercase letter and are BumpyCase. All other identifiers start with a lowercase letter or an underscore, and by convention use underscores between words.

## Comments

- `#` => single line comment

There are no multi-line comment

## Code Documentation

- `@moduledoc` => module documentation
- `@doc` => function documentation
- `@spec` => function arguments/return specification
- `@typedoc` => type documentation
- `@type` => type definition
- `@typep` => private type definition

```elixir
defmodule Math do
  @moduledoc """
  Provides math-related functions.

  ## Examples

      iex> Math.sum(1, 2)
      3
  """

  @spec sum(number, number) :: number
  @doc """
  Calculates the sum of two numbers.
  """
  def sum(a, b), do: a + b   # `, do: inline_code` or `, do: ()` is the shortcut for `do ... end`. 
                             # Except `cond do` where no `,` 
end

h Math
h Math.sum
```

## Elixir Special Unbound Variable

- `_` => unbound variable

## Elixir/Erlang Virtual Machine inspection

- `:observer.start` => start a gui tool for inspection
- `:erlang.memory` => inspect memory
- `:c.memory` => inspect memory

```elixir
:c.memory
# [
#   total: 19262624,
#   processes: 4932168,
#   processes_used: 4931184,
#   system: 14330456,
#   atom: 256337,
#   atom_used: 235038,
#   binary: 43592,
#   code: 5691514,
#   ets: 358016
# ]
```

## Interactive Elixir

- `iex` => open Interactive Elixir
- `iex <file>` => open Interactive Elixir loading a file
- `<Ctrl>c + <Ctrl>c` => close iex

Inside iex
- `i/1` => prints information about the given term
- `h/0` => prints help message
- `h/1` => prints help for the given module, function or macro
- `r/1` => recompiles the given module's source file
- `c/1` => compiles a `.ex` file into the current directory
- `recompile/0` => recompiles the current project
- `v/1` => retrieves the nth value from the history
- `#iex:break` => cancel multiple line command
## Basic Types

### Integer

Arbitrary-sized 
- `1` => integer
- `1_000` => integers can use `_` to make it easy to read
- `0b1010` => binary integer notation 10
- `0o777` => octadecimal integer notation 511
- `0x1F` => hexadecimal integer notation 31

### Float

There must be at least one digit before and after the decimal point. Floats are IEEE 754 double precision.
- `-1.0` => float
- `5.7e-2` => float exponent notation 0.057

### Atom

We write atom using a leading colon (:), which can be followed by an atom word or an Elixir operator. An atom word is a sequence of UTF-8 letters (including combining marks), digits, underscores, and at signs (@). It may end with an exclamation point or a question mark. You can also create atoms containing arbitrary characters by enclosing the characters following the colon in double quotes.

- `:atom` => atom / symbol
-	`​:fred`​  
- `​:is_binary?`​  
- `​:var@2`​  
- `​:<>`​  
- `​:===`​  
- `​:'func/3'​`
​- `​:"long john silver"`​  
- `​:эликсир​ `  
- `​:mötley_crüe`
- `true` => boolean (atom)

Atoms are not garbage collected. Once an atom is created, it is never reclaimed.
<!-- ### BitString

- `<<97::size(2)>>` => bit string
- `<<97,98>>` => binary
- `"elixir"` => string -->

### Tuple

A tuple is an ordered collection of values.
- `{1, 2, 3, :ok}` => tuple

### List

A list is effectively a linked data structure.
- `[1, 2, 3]` => list
- `'elixir'` => char list

- `[a: 5, b: 3]` => keyword list short notation
- `[{:a, 5}, {:b, 3}]` => keyword list long notation

### Map

A map is a collection of key/value pairs.
- `%{name: "Mary", age: 29}` => map short notation (keys must be atoms)
- `%{:name => "Mary", :age => 29}` => map long notation

### Date

The Date type holds a year, a month, a day, and a reference to the ruling calendar
- `{​:ok​, d1} = Date.new(2018, 12, 25) # {:ok, ~D[2018-12-25]}`
- `d2 = ​~​D[2018-12-25]` => `~D` sigil syntax
- `Date.day_of_week(d1) # 2`
- `Date.add(d1, 7) # 	~D[2019-01-01]`

Elixir can also represent a range of dates:
- `first_half = Date.range(~D[2018-01-01], ~D[2018-06-30])  #DateRange<~D[2018-01-01], ~D[2018-06-30]>`
- `~​D[2018-03-15] ​in​ first_half # true`

### Time

The Time type contains an hour, a minute, a second, and fractions of a second. The fraction is stored as a tuple containing microseconds and the number of significant digits. (The fact that time values track the number of significant digits in the seconds field means that `~T[12:34:56.0]` is not equal to `~T[12:34:56.00]`.)
- `{​:ok​, t1} = Time.new(12, 34, 56)`
- `​ t2 = ​~​T[12​:34:56​.78]` => `~T` sigil syntax
- `Time.add(~T[00:00:00.000], 86_399_999, :millisecond)  # ~T[23:59:59.999]`

There are two date/time types: `DateTime` and `NaiveDateTime`. The naive version contains just a date and a time; `DateTime` adds the ability to associate a time zone. The `~N[...]` sigil constructs `NaiveDateTime` structs.

### PID

A PID is a reference to a local or remote process.
- `self() #=> #PID<0.80.0>` => current Process id

### Port

A port is a reference to a resource (typically external to the application) that you’ll be reading or writing.

### Function

- `fn -> :hello end` => anonymous function

### Reference

- `make_ref() #=> #Reference<0.0.8.133>` => create a new reference

### Port

- `hd Port.list() #=> #Port<0.0>` => get first port

## Type Testing

- `is_nil/1`
- `is_integer 1`
- `is_float 4.6`
- `is_number 7.8`
- `is_atom :foo`
- `is_boolean false`
- `is_list/1`
- `is_tuple/1`
- `is_map/1`
- `is_pid self()`
- `is_function(fn a, b -> a + b end)` => function
- `is_function(fn a, b -> a + b end, 2)` => function with arity
- `is_port hd Port.list()`
- `is_reference make_ref()`
- `Range.range?(1..3)`

## Converting Types

- `Map.to_list(%{:a => 1, 2 => :b})` => convert a map to list of tuples or keyword list

## Number Operators

- `10 / 2 => 5.0` => division always return a float
- `div(10, 2) => 5` => integer division
- `rem 10, 3 => 1` => integer remain of a division
- `round(3.58) => 4` => float round
- `trunc(3.58) => 3` => float trunc

## Boolean Operators

Falsy in Elixir is `false` and `nil`, otherwise will be truthy.

- `==` => equals (`1 == 1.0` is true)
- `!=` => different
- `===` => strict equal (integer with float)
- `!==` => strict different (integer with float `1 !== 1.0` is true)
- `<` => less
- `<=` => less or equal
- `>` => greater
- `>=` => greater or equal
- `&&` => truthy and
- `||` => truthy or
- `!` => truthy not
- `and` => boolean and
- `or` => boolean or
- `not` => boolean not

Boolean and Relaxed Boolean operators
- `a ​or​ b     ​# true if a is true; otherwise b​`
- `a ​and​ b    ​# false if a is false; otherwise b​`
- `​not​ a      ​# false if a is true; true otherwise`
- `a || b    ​# a if a is truthy; otherwise b​`
- `a && b    ​# b if a is truthy; otherwise a`​
- `​!​a        ​# false if a is truthy; otherwise true`

If the types are the same or are compatible (for example, 3 > 2 or 3.0 < 5), the comparison uses natural ordering. Otherwise comparison is based on type according to this rule: `number < atom < reference < functions < port < pid < tuple < map < list < bit string`.

## Join Operators

```elixir  
  binary1 <> binary2  ​# concatenates two binaries (Later we'll​
                      ​# see that binaries include strings.)​
  list1 ++ list2      ​# concatenates two lists​
  list1 -- list2      ​# removes elements of list 2 from a copy of list 1
```
## In operator

```elixir  
  a ​in​ enum       ​# tests if a is included in enum (for example,​
                  ​# a list, a range, or a map). For maps, a should​
                  ​# be a {key, value} tuple.
```
## Pipe Operator

- `|>` => pipe operator

The return of a function will be passed as the first argument to the following.

```elixir
1..100 |>
  Stream.map(&(&1 * 3)) |>
  Stream.filter(&(rem(&1, 2) != 0)) |>
  Enum.sum
#=> 7500
```

## Pattern Matching

In Elixir `=` sign is not just an assign operator, but a **Match Operator**. It’s like an assertion. It succeeds if Elixir can find a way of making the left-hand side equal the right-hand side.

This means that you assign variables from right side to the left based on patterns defined by the left one. If a pattern does not match a `MatchError` is raised. A pattern (the left side) is matched if the values (the right side) have the same structure and if each term in the pattern can be matched to the corresponding term in the values.

This powerful tool is also used to decompose complex objects like tuples, lists, etc into smaller ones:

```elixir
x = 1 #=> assign 1 to x
2 = x #=> ** (MatchError)
1 = x #=> match and does not assign anything

"world" <> x = "hello" #=> ** (MatchError)
"he" <> x = "hello"

{x, y, z} = {1, 2} #=> ** (MatchError)
{} = {1, 2} #=> ** (MatchError)
{:a, :b} = {:b, :a} #=> ** (MatchError)
{x, y} = {1, 2}

first..last = 1..5

[x, 4] = [:a, 5] #=> ** (MatchError)
[] = [:a, 5] #=> ** (MatchError)
[:a, :b] = [:b, :a] #=> ** (MatchError)
[x, 4] = [:a, 4]

[x | y] = [] #=> ** (MatchError)
[x | y] = [1]
[x | y] = [1, 2, 3]

[a: x] = [b: 9] #=> ** (MatchError)
[a: x] = [a: 5]
[{:a, x}] = [a: 5]

%{a: x} = %{b: 5} #=> ** (MatchError)
%{} = %{a: 5} # empty map matches any map
%{a: x, b: 5} = %{b: 5, a: 7, c: 9}

defmodule User do
  defstruct name: "John", age: 27
end
john = %User{age: 29}
%User{name: name} = john
name #=> "John"
```

So in other words:

- non variables on the left side will be used to **restrict a pattern to match**
- variables using the pin operator on the left side will have its value to be used to **restrict a pattern to match**
- variables on the left side will be **assigned** with right side values

So **variables** and **non variables** behave differently with the match operator.

In order to assert an **empty map** you have to use a guard instead of pattern match, just like:

```elixir
(
  fn m when map_size(m) == 0 ->
    "empty map"
  end
).(%{}) #=> "empty map"
```
Elixir tries to match arguments as right value to the parameters as left pattern.

### Pin Operator

The Pin Operator `^` is used to treat variables the same way non variables with the match operator. In other words, the Pin Operator will evaluate the variable and use its value to **restrict a pattern**, preserving its original value. It force Elixir to use the existing value of the variable in the pattern.

```elixir
x = 1 #=> assign 1 to x
^x = 1 #=> match x value with right side 1
^x = 2 #=> ** (MatchError)
```

### Match Operator Limitation

You cannot make function calls on the left side of a match.

- `length([1, [2], 3]) = 3 #=> ** (CompileError) illegal pattern`

## Custom Operators

You can customize an Elixir Operator like the following example:

```elixir
1 + 2 #=> 3
defmodule WrongMath do
  def a + b do
    {a, b}
  end
end
import WrongMath
import Kernel, except: [+: 2]
1 + 2 #=> {1, 2}
```

## Sigils

Available delimiters for `Sigil`: `/`, `|`, `"`, `'`, `(`, `[`, `{`, `<`.

- `~r` => regular expression (modifiers: `i`)
- `~r/hello/i` => `i` modifies to case insensitive
- `~w` => list of string words (modifiers: )
- `~w[foo bar]c` => `c` modifies to list of char lists
- `~w[foo bar]a` => `c` modifies to list of atoms

```elixir
~w(one two three) #=> ["one", "two", "three"]
~w(one two three)c #=> ['one', 'two', 'three']
~w(one two three)a #=> [:one, :two, :three]
```

<!-- ## Bit Strings

- `<<97::4>>` => short notation with 4 bits
- `<<97::size(4)>>` => long notation with 4 bits
- `byte_size(<<5::4>>)` => bit string byte size

### Performance for Bit Strings:

#### cheap functions:

- `byte_size(<<97::4>>)`

#### expensive functions:

## Binaries

Binaries are 8 bits multiple Bit Strings. Binaries are 8 bits by default.

- `<<97>>` => short notation with 8 bits
- `<<97::size(8)>>` => long notation with 8 bits
- `<>` => concatenate binaries/strings

### Performance for Binaries:

#### cheap functions:

- `byte_size(<<97>>)`

#### expensive functions: -->

## Strings

String is a Binary of code points where all elements are valid characters. Strings are surrounded by double-quotes and are encoded in `UTF-8` by default.

- `"hello"` => string
- `"hello #{:world}"` => string interpolation
- `"\n"` => new line
- `String.length("hello") #=> 5` => get the length of a string
- `String.upcase("hello") #=> "HELLO"` => upcase a string
- `"""` => multi-line string begin/end

### Performance for Strings:

#### cheap functions:

- `byte_size("hello")`

#### expensive functions:

- `String.length("Hello")`

## Tuples

Tuple is stored contiguously in memory.

- `{:ok, "hello"}`
- `tuple_size({:ok, "hello"})` => tuple size
- `elem({:ok, "hello"}, 0)` => get tuple element by index
- `put_elem({:ok, "hello"}, 1, "world")`

### Performance for Tuples:

#### cheap functions:

- `tuple_size({:ok, "hello"})`
- `elem({:ok, "hello"}, 1)`

#### expensive functions:

- `put_elem({:ok, "hello"}, 1, "world")`

## Lists

**Lists** implements `Enumerable` protocol.

List is a linked list structure where each element points to the next one in memory. When subtraction just the first ocurrence will be removed. A list may either be empty or consist of a head and a tail. The head contains a value and the tail is itself a list.

- `[:ok, "hello"]`
- `[97 | [1, 6, 9]]` => prepend
- `[1, 5] ++ [3, 9] # [1, 5, 3, 9]` => concatenation
- `[1, 5] -- [9, 5] # [1]` => subtraction first occurrences
- `hd([1, 5, 7])` => head
- `tl([1, 5, 7])` => tail
- `:foo in [:foo, :bar] #=> true` => in operator

### Performance for Lists:

#### cheap functions:

- `[97 | [1, 6, 9]]` => prepend
- `hd([1, 5, 7])` => head
- `tl([1, 5, 7])` => tail

#### expensive functions:

- `[1, 5] ++ [3, 9] # [1, 5, 3, 9]` => concatenation
- `[1, 5] -- [9, 5] # [1]` => subtraction first occurrences
- `length([1, 4])`
- `:foo in [:foo, :bar] #=> true` => in operator

<!-- ## Char List

A Char List is a List of code points where all elements are valid characters. Char Lists are surrounded by single-quote and are usually used as arguments to some old Erlang code.

- `'ab'` => char list
- `[97, 98]` => `'ab'`
- `[?a, ?b]` => `'ab'`
- `?x` => code points (ASCII code) for letter `x`
- `'hello' ++ 'world' # 'helloworld'` => concatenation
- `'hello' -- 'world' # 'hel'` => subtraction first occurrences
- `?l in 'hello' #=> true` => in operator

### Performance for Char Lists:

#### cheap functions:

- `[?H | 'ello']` => prepend

#### expensive functions:

- `'hello' ++ 'world' # 'helloworld'` => concatenation
- `'hello' -- 'world' # 'hel'` => subtraction first occurrences
- `length('Hello')`
- `?l in 'hello' #=> true` => in operator -->

## Keyword Lists

Keyword list is a list of key/value pairs. It is a list of tuples where first elements are atoms. When fetching by key the first match will return. If a keyword list is the last argument of a function the square brackets `[` are optional.

- `[a: 5, b: 3]` => keyword list short notation. 
- `[{:a, 5}, {:b, 3}]` => keyword list long notation (a list of two-value tuples)
- `[{:a, 6} | [b: 7]] # [a: 6, b: 7]` => prepend
- `[a: 5] ++ [a: 7] # [a: 5, a: 7]` => concatenation
- `[a: 5, b: 7] -- [a: 5] # [b: 7]` => subtraction first ocurrences
- `([a: 5, a: 7])[:a] # 5` => fetch by key
- `length(a: 5, b: 7)` => optional squared brackets `[`

### Performance for Keyword Lists:

#### cheap functions:

- `[{:a, 6} | [b: 7]] # [a: 6, b: 7]` => prepend

#### expensive functions:

- `[a: 5] ++ [a: 7] # [a: 5, a: 7]` => concatenation
- `[a: 5, b: 7] -- [a: 5] # [b: 7]` => subtraction first ocurrences
- `([a: 5, a: 7])[:a] # 5` => fetch by key
- `length(a: 5, b: 7)` => optional squared brackets `[`

## Maps

**Maps** implements `Enumerable` protocol.

Map holds a key value collection. Why do we have both maps and keyword lists? Maps allow only one entry for a particular key, whereas keyword lists allow the key to be repeated. Maps are efficient (particularly as they grow), and they can be used in Elixir’s pattern matching. In general, use keyword lists for things such as command-line parameters and passing around options, and use maps when you want an associative array.

- `%{name: "Mary", age: 29}` => map short notation (keys must be atoms)
- `%{:name => "Mary", :age => 29}` => map long notation
- `%{name: "Mary", age: 29}[:name]` => fetch `:name` hash notation
- `%{name: "Mary", age: 29}[:born]` => returns nil when do not find in the hash notation
- `%{name: "Mary", age: 29}.name` => fetch `:name` short notation (this notation is only for key that are atom)
- `%{name: "Mary", age: 29}.born # ** (KeyError)` => blows an error when key does not exists
- `%{%{name: "Mary", age: 29} | age: 31}` => update value for existing key
- `%{%{name: "Mary", age: 29} | born: 1990} # ** (KeyError)` => blows an error when updating non existing key
- `map_size(%{name: "Mary"}) #=> 1` => map size

### Performance for Maps:

#### cheap functions:

- `%{name: "Mary", age: 29}[:name]` => fetch `:name`
- `%{name: "Mary", age: 29}.name` => fetch `:name` short notation
- `%{%{name: "Mary", age: 29} | age: 31}` => update value for existing key
- `map_size(%{name: "Mary"}) #=> 1` => map size

#### expensive functions:

## Structs

Structs are built in top of Map.

- `defstruct` => define a struct

```elixir
defmodule User do
  defstruct name: "John", age: 27
end
john = %User{} #=> %User{age: 27, name: "John"}
mary = %User{name: "Mary", age: 25} #=> %User{age: 25, name: "Mary"}
meg = %{john | name: "Meg"} #=> %User{age: 27, name: "Meg"}
bill = Map.merge(john, %User{name: "Bill", age: 23})

john.name #=> John
john[:name] #=> ** (UndefinedFunctionError) undefined function: User.fetch/2
is_map john #=> true
john.__struct__ #=> User
Map.keys(john) #=> [:__struct__, :age, :name]
```

## Ranges

Ranges are `Struct`. Ranges are represented as start..end, where start and end are integers.

- `range = 1..10` => range definition
- `Enum.reduce(1..3, 0, fn i, acc -> i + acc end) #=> 6` => range used in a reduce function to sum them up
- `Enum.count(range) #=> 10`
- `Enum.member?(range, 11) #=> false`

## Protocols
Protocols allow us to extend the original behaviour for as many data types as we need. 
- `defprotocol Foo` => define protocol `Foo`
- `defimpl Foo, for Integer` => implement that protocol for `Integer`

Here are all native data types that you can use: `Atom`, `BitString`, `Float`, `Function`, `Integer`, `List`, `Map`, `PID`, `Port`, `Reference`, `Tuple`.

```elixir
defprotocol Blank do
  @doc "Returns true if data is considered blank/empty"
  def blank?(data)
end

defimpl Blank, for: Integer do
  def blank?(_), do: false
end

defimpl Blank, for: List do
  def blank?([]), do: true
  def blank?(_),  do: false
end

defimpl Blank, for: Map do
  def blank?(map), do: map_size(map) == 0
end

defimpl Blank, for: Atom do
  def blank?(false), do: true
  def blank?(nil),   do: true
  def blank?(_),     do: false
end

Blank.blank?(0) #=> false
Blank.blank?([]) #=> true
Blank.blank?([1, 2, 3]) #=> false
Blank.blank?("hello") #=> ** (Protocol.UndefinedError)
```

`Structs` do not share `Protocol` implementations with `Map`.

```elixir
defimpl Blank, for: User do
  def blank?(_), do: false
end
```

You can also implement a `Protocol` for `Any`. And in this case you can derive any `Struct`.

```elixir
defimpl Blank, for: Any do
  def blank?(_), do: false
end

defmodule DeriveUser do
  @derive Blank
  defstruct name: "john", age: 27
end
```

Elixir built-in most common used protocols: `Enumerable`, `String.Chars`, `Inspect`.

## Nested data Structures

- `put_in/2`
- `update_in/2`
- `get_and_update_in/2`

```elixir
users = [
  john: %{name: "John", age: 27, languages: ["Erlang", "Ruby", "Elixir"]},
  mary: %{name: "Mary", age: 29, languages: ["Elixir", "F#", "Clojure"]}
]
users[:john].age #=> 27

users = put_in users[:john].age, 31
users = update_in users[:mary].languages, &List.delete(&1, "Clojure")
```

## Enums and Streams

**Lists** and **Maps** are Enumerables.

`Enum` module perform **eager** operations, meanwhile `Stream` module perform **lazy** operations.

```elixir
# eager Enum
1..100 |> Enum.map(&(&1 * 3)) |> Enum.sum #=> 15150

# lazy Stream
1..100 |> Stream.map(&(&1 * 3)) |> Enum.sum #=> 15150
```

## do/end Keyword List and Block Syntax

In Elixir you can use either **Keyword List** syntax or  **do/end Block** syntax:

```elixir
sky = :gray

if sky == :blue do
  :sunny
else
  :cloudy
end

if sky == :blue, do: :sunny, else: :cloudy

if sky == :blue, do: (
  :sunny
), else: (
  :cloudy
)
```

## Conditional Flows (if/else/case/cond)

### if / else

```elixir
sky = :gray
if sky == :blue, do: :sunny, else: :cloudy
```

### unless / else

```elixir
sky = :gray
unless sky != :blue, do: :sunny, else: :cloudy
```

### case / when

```elixir
sky = {:gray, 75}
case sky, do: (
  {:blue, _}         -> :sunny
  {_, t} when t > 80 -> :hot
  _                  -> :check_wheather_channel
)
```

On **when guards** short-circuiting operators `&&`, `||` and `!` are **not** allowed.

### cond

`cond` is equivalent as `if/else-if/else` statements.

```elixir
sky = :gray
cond do: (
  sky == :blue -> :sunny
  true         -> :cloudy
)
```

## The `with` macro

The `with` expression serves double duty. First, it allows you to define a local scope for variables. If you need a couple of temporary variables when calculating something, and you don’t want those variables to leak out into the wider scope, use with. Second, it gives you some control over pattern-matching failures using `->`. 

- `with` => macro to combine multiple match clauses
- `<-` => a matching clause, on the left. IF match fails, it returns the value on right that couldn’t be matched
- `=` => bare expression is allowed. If the match had failed, a `MatchError` exception would be raised
- `else` => if some matching clause fails

```elixir
height = 100 
opts = %{width: 10, height: 20}
with {:ok, width} <- Map.fetch(opts, :width),
     {:ok, height} <- Map.fetch(opts, :height) do    
      {:ok, width * height}
    else
      :error ->
        {:error, :wrong_data}
    end
#=> {:ok, 200} 
#The inner variable height is local to the with, and does not affect the variable in the outer scope. 
```

## Recursion

There is traditional **no for loop** in Elixir, due to Elixir immutability There is a macro `for` that it's also called as `Comprehension` but it works differently from a traditional for loop. If you want a simple loop iteration you'll need to use **recursion**:

```elixir
defmodule Logger do
  def log(msg, n) when n <= 0, do: ()
  def log(msg, n) do
    IO.puts msg
    log(msg, n - 1)
  end
end
Logger.log("Hello World!", 3)
# Hello World!
# Hello World!
# Hello World!
```

In functional programming languages **map** and **reduce** are two major algorithm concepts. They can be implemented with **recursion** or using the `Enum` module.

**reduce** will reduces the array into a single element:

```elixir
defmodule Math do
  def sum_list(list, sum \\ 0)
  def sum_list([], sum), do: sum
  def sum_list([head | tail], sum) do
    sum_list(tail, head + sum)
  end
end
Math.sum_list([1, 2, 3]) #=> 6

Enum.reduce([1, 2, 3], 0, &+/2) #=> 6
```

**map** modifies an existing array (new array with new modified values):

```elixir
defmodule Math do
  def double([]), do: []
  def double([head | tail]) do
    [head * 2 | double(tail)]
  end
end
Math.double([1, 2, 3]) #=> [2, 4, 6]

Enum.map([1, 2, 3], &(&1 * 2)) #=> [2, 4, 6]
```

## Comprehension -> the for loop

`Comprehension` is a syntax sugar for the very powerful `for special form`. You can have **generators** and **filters** in that.

- `for` => `Comprehension`
- `<-` => **generators**
- `:into` => change return to another `Collectable` type

You can iterate over `Enumerable` what makes so close to a regular `for` loop on other languages:

```elixir
for n <- [1, 2, 3, 4], do: n * n
#=> [1, 4, 9, 16]
```

You can also iterate over multiple `Enumerable` and then create a combination between them:

```elixir
for i <- [:a, :b, :c], j <- [1, 2], do:  {i, j}
#=> [a: 1, a: 2, b: 1, b: 2, c: 1, c: 2]
```

You can pattern match each element:

```elixir
values = [good: 1, good: 2, bad: 3, good: 4]
for {:good, n} <- values, do: n * n
#=> [1, 4, 16]
```

Generators use `<-` and they have on the right an `Enumerable` and on the left a **pattern matchable** element variable.

You can have **filters** to filter **truthy** elements:

```elixir
for dir  <- [".", "/"],
    file <- File.ls!(dir),
    path = Path.join(dir, file),
    File.regular?(path) do
  "dir=#{dir}, file=#{file}, path=#{path}"
end
#=> ["dir=., file=README.md, path=./README.md", "dir=/, file=.DS_Store, path=/.DS_Store"]
```

You can use `:into` to change the return type:

```elixir
for k <- [:foo, :bar], v <- 1..5, into: %{}, do: {k, v}
#=> %{bar: 5, foo: 5}
for k <- [:foo, :bar], v <- 1..5, into: [], do: {k, v}
#=> [foo: 1, foo: 2, foo: 3, foo: 4, foo: 5, bar: 1, bar: 2, bar: 3, bar: 4, bar: 5]
```

## Anonymous Functions

- `fn...end` => define anonymous functions, like the quotes that surround a string literal.
- `->` => one line function definition
- `.(arguments)` => call an anonymous function
- `when` => guards

```elixir
add = fn a, b -> a + b end
add.(4, 5) #=> 9
```

We can have multiple clauses and guards inside functions.

```elixir
calc = fn
  x, y when x < 0 -> x + y
  x, y -> x * y
end
calc.(-1, 6) #=> 5
calc.(9, 5) #=> 45
```
You can define different implementations, depending on the type and contents of the arguments passed. (You cannot select based on the number of arguments—each clause in the anonymous function definition must have the same number of parameters.)

Elixir automatically carry with them the bindings of variables in the scope in which they are defined. This is a closure—the scope encloses the bindings of its variables, packaging them into something that can be saved and used later.

```elixir
  greeter = ​fn​ name -> (​fn​ -> ​'​​Hello ​​#{​name​}​​'​ ​end​) ​end
  dave_greeter = greeter.(​'​​Dave'​)
  dave_greeter.()  #'Hello Dave'

  ​defmodule​ Greeter ​do​
    ​def​ for(name, greeting) ​do​
    ​  fn​
        (^name) -> ​'​​#{​greeting​}​​ ​​#{​name​}​​'​
        (_)     -> ​'​​I don't know you'​
    ​  end​
    ​end​
  ​end​
mr_valim = Greeter.for(​'​​José'​, ​'​​Oi!'​)
IO.puts mr_valim.(​'​​José'​)    ​# => Oi! José​
IO.puts mr_valim.(​'​​Dave'​)    ​# => I don't know you
```

## Modules And Named Functions

- `defmodule` => define Modules
- `def`  => define functions inside Modules
- `defp`  => define private functions inside Modules
- `when` => guards
- `@` => module attributes
- `__info__(:functions)` => list functions inside a module
- `defdelegate` => delegate functions

```eilxir
defmodule Math do
  def sum(a, b) when is_integer(a) and is_integer(b), do: a + b
end

Math.sum(1, 2) #=> 3

Math.__info__(:functions) #=> [sum: 2]
```

Module attribute works as constants, evaluated at compilation time:

```eilxir
defmodule Math do
  @foo :bar
  def print, do: @foo
end

Math.print #=> :bar
```

Special Module attributes:

- `@vsn`
- `@moduledoc`
- `@doc`
- `@behaviour`
- `@before_compile`

## Default Argument

- `\\` => default argument

```elixir
defmodule Concat do
  def join(a, b, sep \\ " ") do
    a <> sep <> b
  end
end

IO.puts Concat.join("Hello", "world")      #=> Hello world
IO.puts Concat.join("Hello", "world", "_") #=> Hello_world
```

Default values are **evaluated runtime**.

```elixir
defmodule DefaultTest do
  def dowork(x \\ IO.puts "hello") do
    x
  end
end
DefaultTest.dowork #=> :ok
# hello
DefaultTest.dowork 123 #=> 123
DefaultTest.dowork #=> :ok
# hello
```

Function with multiple clauses and a default value requires a **function head** where default values are set:

```elixir
defmodule Concat do
  def join(a, b \\ nil, sep \\ " ") # head function

  def join(a, b, _sep) when is_nil(b) do
    a
  end

  def join(a, b, sep) do
    a <> sep <> b
  end
end

IO.puts Concat.join("Hello")               #=> Hello
IO.puts Concat.join("Hello", "world")      #=> Hello world
IO.puts Concat.join("Hello", "world", "_") #=> Hello_world
```

## Function Capturing

- `&` => function capturing
- `&1` => 1st argument

`&` could be used with a module function.

When capturing you can use the function/operator with its arity.

```elixir
a = &(&1 * &2)
a.(3, 4) #=> 12
(&(&1 * &2)).(3, 4) #=> 12
(&*/2).(3, 4) #=> 12

(&Kernel.is_atom(&1)).(:foo) #=> true
(&Kernel.is_atom/1).(:foo) #=> true
(&{:ok, [&1]}).(:foo) #=> {:ok, [:foo, :bar]}
(&[&1, &2]).(:foo, :bar) #=> [:foo, :bar]
(&[&1 | [&2]]).(:foo, :bar) #=> [:foo, :bar]
```

## Behaviours

Behaviour modules defines functions

- `@callback` => defines a function to be implemented by other modules
- `::` => separates the function definition to its return type

```elixir
defmodule Parser do
  @callback parse(String.t) :: {:ok, term} | {:error, String.t}
  @callback extensions() :: [String.t]
end

defmodule JSONParser do
  @behaviour Parser
  
  @impl Parser
  def parse(str), do: {:ok, "some json " <> str} # ... parse JSON

  @impl Parser
  def extensions, do: ["json"]
end
```

## Exceptions/Errors => raise/try/rescue

**Exceptions/Errors** in Elixir are `Structs`.

- `raise "oops" #=> ** (RuntimeError) oops` => raises runtime error
- `raise ArgumentError #=> ** (ArgumentError) argument error` => raises an error by module
- `raise ArgumentError, message: "oops" #=> ** (ArgumentError) oops` => raises an error by module with message
- `defexception` => define an exception
- `try/rescue` => catches an error
- `throw/try/catch` => can be used as circuit breaking, but should be avoided
- `exit("my reason")` => exiting current process
- `after` => ensures some resource is cleaned up even if an exception was raised
- `else` => match on the results of the try block whenever the try block finishes without a throw or an error

```elixir
defmodule MyError do
  defexception message: "default message"
end

is_map %MyError{} #=> true
Map.keys %MyError{} #=> [:__exception__, :__struct__, :message]

raise MyError #=> ** (MyError) default message
raise MyError, message: "custom message" #=> ** (MyError) custom message
```

You can rescue an error with:

```elixir
try do
  raise "oops"
rescue
  e in RuntimeError -> e
else 
  IO.puts "can't be reached in this ex."
after
  IO.puts "I can do some clean up here"
end
#=> I can do some clean up here
#=> %RuntimeError{message: "oops"}
```

`throw/catch` sometime is used for circuit breaking/controlling the flow, but you can usually use another better way:

```elixir
try do
  Enum.each -50..50, fn(x) ->
    if rem(x, 13) == 0, do: throw(x)
  end
  "Got nothing"
catch
  x -> "Got #{x}"
end
#=> "Got -39"

Enum.find -50..50, &(rem(&1, 13) == 0)
#=> -39
```

When a process dies of “natural causes” (e.g., unhandled exceptions), it sends an `exit` signal. A process can also die by explicitly sending an `exit` signal: `spawn_link(fn -> exit(1) end)`

`exit` can be caught but this is rare in Elixir:

```elixir
try do
  exit "I am exiting"
catch
  :exit, _ -> "not really"
end
#=> "not really"
```

<!-- You can ommit `try` inside functions and use `rescue`, `catch`, `after` directly:

```elixir
def without_even_trying do
  raise "oops"
after
  IO.puts "cleaning up!"
end
``` -->
Variables defined inside `try/catch/rescue/after` blocks do not leak to the outer context.
## IO

- `IO.puts/1 "Hello"` => prints to stdout
- `IO.puts :stderr, "Hello"` => prints to stderr
- `IO.gets "yes/no: "` => reads an user input

## StringIO

- `{:ok, pid} = StringIO.open("my-file.md")` => open a file
- `IO.read(pid, 2) #=> "he"` => read first 2 lines

## File

- `{:ok, file} = File.open "hello", [:write]` => open file for reading
- `IO.binwrite file, "world"` => write into file
- `File.close file` => close file
- `File.read "my-file.md"` => reads a file
- `File.stream!("my-file.md") |> Enum.take(10)` => read the first 10 lines

```elixir
{:ok, file} = File.open "my-file.md", [:write]
IO.binwrite file, "hello world"
File.close file
File.read "my-file.md" #=> {:ok, "hello world"}
```

## Path

- `Path.join` => joins
- `Path.expand("~/hello")` => expands to full path

## Processes, Tasks

Process in Elixir has the same concept as threads in a lot of other languages, but extremely lightweight in terms of memory and CPU. They are isolated from each other and communicate via message passing.

- `spawn/1` => fork a process
- `self()` => current process
- `Process.alive?(pid)` => check if process is still running
- `send/2` => send message to another process
- `receive/1` => receive message from another process
- `after` => receive option to work with timeout
- `flush()` => prints out and clean all messages received
- `Process.monitor(item)` => Starts monitoring the given item from the calling process.

```elixir
parent = self()
pid = spawn(fn -> send(parent, {:hello, self()}) end)
Process.alive?(pid)
#=> false    #The spawned process will execute the given function and exit after the function is done
receive do
  {:hello, pid} -> "Got hello from #{inspect pid}"
after 
  1_000 -> "nothing after 1s, time out"
end
```
We spawn processes as linked processes. 
- `spawn_link/1` => forks a process and link them, so failures are propagated
- `Task.start/1` => starts a task
- `Task.start_link/1` => starts a task and link it to the current process

Tasks build on top of the spawn functions to provide better error reports and introspection. Tasks can be used in supervision trees.

```elixir
parent = self()

spawn_link(fn -> send(parent, {:hello, self()}) end)
receive do: ({msg, pid} -> "#{inspect pid} => #{msg}"), after: (1_000 -> "nothing after 1s")

Task.start_link(fn -> send(parent, {:hello, self()}) end)
receive do: ({msg, pid} -> "#{inspect pid} => #{msg}"), after: (1_000 -> "nothing after 1s")

flush()
```
## State

State can be stored in processes. Manual implementation of a storage using Elixir processes that loop infinitely, maintain state, and send and receive messages.:

```elixir
defmodule KV do
  def start_link do
    Task.start_link(fn -> loop(%{}) end)
  end

  defp loop(map) do
    receive do
      {:get, key, caller} ->
        send caller, Map.get(map, key)
        loop(map)
      {:put, key, value} ->
        loop(Map.put(map, key, value))
    end
  end
end
{:ok, pid} = KV.start_link

send pid, {:put, :hello, :world}
send pid, {:get, :hello, self()}
flush() #=> :world
```
It is also possible to register the pid, giving it a name, and allowing everyone that knows the name to send it messages:
- `Process.register(pid, :foo)` => register a name(:foo) for a process

```elixir
Process.register(pid, :kv)
send(:kv, {:get, :hello, self()})
flush()
```
## Agent

Elixir provides agents, which are simple abstractions around state.
Implementation of a storage using `Agent`:

```elixir
{:ok, pid} = Agent.start_link(fn -> %{} end)
Agent.update(pid, fn map -> Map.put(map, :hello, :world) end)
Agent.get(pid, fn map -> Map.get(map, :hello) end)
Agent.stop(pid)
```
- `Agent.start_link/1` => receives an anonymous function that returns the Agent’s initial state.
- `Agent.update/3` => second argument is a function that takes the agent’s current state as input and returns its desired new state.
- `Agent.get/3` => second argument is a function that takes the state as input and returns the value that `Agent.get/3` itself will return.
- `Agent.stop/3` => terminate the agent process.
- `Agent.get_and_update/2` => get a value and update the agent state in one function

## GenServer

A behaviour module for implementing the server of a client-server relation.

A GenServer is a process like any other Elixir process and it can be used to keep state, execute code asynchronously and so on. The advantage of using a generic server process (GenServer) implemented using this module is that it will have a standard set of interface functions and include functionality for tracing and error reporting. It will also fit into a supervision tree.

There are two types of requests you can send to a GenServer: calls and casts. Calls are synchronous. Casts are asynchronous. 

Callbacks(server side):
- `init(init_arg)` => Invoked when the server is started. Returning `{:ok, state}` will cause `start_link/3` to return `{:ok, pid}`
- `handle_call(request, from, state)` => Invoked to handle synchronous `call/3` messages. `request` is the request message sent by a `call/3`, `request` is often specified as tuples. `from` is a 2-tuple containing the callers PID and a term that uniquely identifies the call, and `state` is the current state of the GenServer.
- `handle_cast(request, state)` => Invoked to handle asynchronous `cast/2` messages. `request` is the request message sent by a `cast/2` and `state` is the current state of the GenServer. Returning `{:noreply, new_state}` continues the loop with new state new_state.
- `handle_info(msg, state)` => Invoked to handle all other messages. `msg` is the message and `state` is the current state of the GenServer. Return values are the same as `handle_cast/2`.  

functions(client side): 
- `start_link(module, init_arg, options \\ [])` => Starts a GenServer process linked to the current process. This is often used to start the GenServer as part of a supervision tree. Once the server is started, the `init/1` function of the given module is called with `init_arg` as its argument to initialize the server. To ensure a synchronized start-up procedure, this function does not return until `init/1` has returned.
- `call(server, request, timeout \\ 5000)` => Makes a synchronous call to the server and waits for its reply. `handle_call/3` will be called on the server to handle the request.
- `cast(server, request)` => Sends an asynchronous request to the server.This function always returns `:ok` regardless of whether the destination server (or node) exists.

Add @impl true before each callback

```elixir
defmodule KV.Registry do
  use GenServer

  ## Client API 

  def start_link(opts) do
    GenServer.start_link(__MODULE__, :ok, opts)
  end

  @doc """
  Looks up the bucket pid for `name` stored in `server`.
  Returns `{:ok, pid}` if the bucket exists, `:error` otherwise.
  """
  def lookup(server, name) do
    GenServer.call(server, {:lookup, name})
  end

  @doc """
  Ensures there is a bucket associated with the given `name` in `server`.
  """
  def create(server, name) do
    GenServer.cast(server, {:create, name})
  end

  ## Defining GenServer Callbacks

  @impl true
  def init(:ok) do
    {:ok, %{}}
  end

  @impl true
  def handle_call({:lookup, name}, _from, names) do
    {:reply, Map.fetch(names, name), names}
  end

  @impl true
  def handle_cast({:create, name}, names) do
    if Map.has_key?(names, name) do
      {:noreply, names}
    else
      {:ok, bucket} = KV.Bucket.start_link([])
      {:noreply, Map.put(names, name, bucket)}
    end
  end
end
```
cheat sheet: https://elixir-lang.org/downloads/cheatsheets/gen-server.pdf

## Supervisor

A Supervisor is a process that supervises other processes and restarts them whenever they crash. To do so, Supervisors manage the whole life-cycle of any supervised processes, including startup and shutdown.

```elixir
defmodule KV.Supervisor do
  use Supervisor

  def start_link(opts) do
    Supervisor.start_link(__MODULE__, :ok, opts)
  end

  @impl true
  def init(:ok) do
    children = [
      {KV.Registry, name: KV.Registry}
    ]

    Supervisor.init(children, strategy: :one_for_one)
  end
end
```

Callback:
- `init(init_arg)` => Callback invoked to start the supervisor and during hot code upgrades. Developers typically invoke `Supervisor.init/2` at the end of their init callback to return the proper supervision flags.
function:
- `init(children, options)` => This is typically invoked at the end of the `init/1` callback of module-based supervisors.
- `which_children(supervisor)` => Returns a list with information about all children of the given supervisor.

Once the supervisor starts, it will traverse the list of children and it will invoke the `child_spec/1` function on each module. The `child_spec/1` function is automatically defined when we `use Agent`, `use GenServer`, `use Supervisor`. After the supervisor retrieves all child specifications, it proceeds to start its children one by one, in the order they were defined. Supervisor automatically starts a broken child.

## Application

Applications are the idiomatic way to package software in Erlang/OTP. To get the idea, they are similar to the "library" concept common in other programming languages, but with some additional characteristics.

An application is a component implementing some specific functionality, with a standardized directory structure, configuration, and life cycle. Applications are loaded, started, and stopped. Each application also has its own environment, which provides a unified API for configuring each application.

We define an application callback to customize what happens when our application starts. The `:mod` option in `mix.exs` `application` function specifies the “application callback module”, followed by the arguments to be passed on application start.

## Mix

Mix is a build tool that ships with Elixir that provides tasks for creating, compiling, testing your application, managing its dependencies and much more

- `mix new` => create project 
- `mix compile` => compile project
- `mix test` => run test
- `mix format` => format project using `.formatter.exs` file
- `mix help` => help

Once the project is compiled, start an iex session inside the project by `iex -S mix`
recompile the project from within iex with the `recompile()` helper

## ExUnit
Test-unit based framework that ships with Elixir

- The test file is an Elixir script file (`.exs`)
- `use ExUnit.Case` to inject the testing API
- A setup callback with the help of the `setup/1` macro. The `setup/1` macro defines a callback that is run before every test, in the same process as the test itself.
- When we return a map from the callback, ExUnit will merge this map into the test context. Since the test context is a map itself, we can pattern match in `test/2`'s second argument
- Running `mix test`, Mix has compiled the source files and generated the application manifest once again.
- `mix test test/kv_test.exs:5` Mix will load and run just that particular test

## Environments

- `:dev` - the one in which Mix tasks (like compile) run by default
- `:test` - used by mix test
- `:prod` - the one you will use to run your project in production

`Mix.env()` returns the current environment as an atom, can be used in .ex/.exs file 
Mix will default to the `:dev` environment, except for the test task that will default to the `:test` environment. The environment can be changed via the MIX_ENV environment variable: `MIX_ENV=prod mix compile` in command line

## alias, require, import and use

In order to facilitate code reuse Elixir has: `alias`, `require`, `import` (directives) and `use` (macro).

- `alias Foo.Bar, as: Bar` => alias module, so Bar can be called instead of Foo.Bar
- `alias Foo.Bar` => `as` is optional on alias
- `require Foo` => ensure the module is compiled and available (usually for macros)
- `import Foo` => requires and import functions from Foo so they can be called without the `Foo.` prefix
- `import List, only: [duplicate: 2]` => only option
- `import List, expect: [duplicate: 2]` => except option
- `import List, only: :macros` => import only macros
- `import List, only: :functions` => import only functions
- `use Foo` => invokes the custom code defined in Foo as an extension point
- `alias MyApp.{Foo, Bar, Baz}` => multiple alias
- `require MyApp.{Foo, Bar, Baz}` => multiple require
- `import MyApp.{Foo, Bar, Baz}` => multiple import

All modules are defines inside `Elixir` namespace but it can be omitted for convenience.

`alias`, `require` and `import` are lexically scoped, which means that it will be valid just inside the scope it was defined. This is **not a global scope**.

`require` is usually used to require Elixir macro code:

```elixir
Integer.is_odd(3) #=> ** (CompileError): you must require Integer before invoking the macro Integer.is_odd/1
require Integer
Integer.is_odd(3) #=> true
```

`use` call `__using__` when the module is being used:

```elixir
defmodule Fruit do
  defmacro __using__(option: option) do
    IO.puts "options=#{inspect option}"
    quote do: IO.puts "Using Fruit module"
  end
end

defmodule Meal do
  use Fruit, option: :hello
end
#=> options=:hello
#=> Using Fruit module
```
`import Module` brings all the Functions and Macros of Module un-namespaced into your module.

`require Module` allows you to use macros of Module but does not import them. (Functions of Module are always available namespaced.)

`use Module` first `requires module` and then calls the `__using__` macro on Module.

See example in: https://stackoverflow.com/questions/28491306/elixir-use-vs-import
## Meta Programming

- `quote` => shows AST (Abstract Syntax Tree)

```elixir
quote do: 2 * 2 == 4
#=> {
#=>   :==,
#=>   [context: Elixir, import: Kernel],
#=>   [
#=>     {
#=>       :*,
#=>       [context: Elixir, import: Kernel],
#=>       [2, 2]
#=>     },
#=>     4
#=>   ]
#=> }
```

## Erlang libraries

Elixir provider some Erlang modules as atoms.

- `:crypto` => crypto functions like `:crypto.hash/2`
- `:io` => io functions like `:io.format/2`
- `:digraph` => deal with digraphs
- `:ets` => large data structure in memory
- `:dets` => large data structure on disk
- `:math` => math functions like `:math.pi/0`
- `:queue` => first-in first-out structure
- `:rand` => rand functions like `:rand.uniform/0`
- `:zip` => handle zip files
- `:zlib` => handle gzip files
