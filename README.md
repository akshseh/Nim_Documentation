# Nim_Documentation

Nim (formerly Nimrod) is a statically typed, imperative programming language that gives the programmer power without compromises on runtime efficiency.
Nim is efficient, expressive, and elegant.
We start the tour with a modified "hello world" program:
```nim
# This is a comment
echo "What's your name? "
var name: string = readLine(stdin)
echo "Hi, ", name, "!"
```
Save the file as xyz.nim and compile using:

```nim
nim compile --run xyz.nim
```

With the --run switch Nim executes the file automatically after compilation. You can give your program command line arguments by appending them after the filename:

```nim
nim compile --run greetings.nim arg1 arg2
```
Indentation is Nim's way of grouping statements. Indentation is done with spaces only, tabulators are not allowed.

String literals are enclosed in double quotes. The var statement declares a new variable named name of type  string with the value that is returned by the readLine procedure. Since the compiler knows that readLine returns a string, you can leave out the type in the declaration (this is called local type inference). So this will work too:
```
var name = readLine(stdin)
```
#### Numbers

Numerical literals are written as in most other languages. As a special twist, underscores are allowed for better readability: 1_000_000 (one million). A number that contains a dot (or 'e' or 'E') is a floating point literal:  1.0e9 (one billion). Hexadecimal literals are prefixed with 0x, binary literals with 0b and octal literals with 0o. A leading zero alone does not produce an octal

#### Var statement
The var statement declares a new local or global variable:
```nim
var                     # Declare (and assign) variables,
  letter: char = 'n'    # with or without type annotations
  lang = "N" & "im"
  nLength : int = len(lang)
  boat: float
  a, b, c: string
  truth: bool = false
```
#### Assignment operator

= is the assignment operator. The assignment operator cannot be overloaded, overwritten or forbidden, but this might change in a future version of Nim. You can declare multiple variables with a single assignment statement and all the variables will have the same value:

```nim
var x, y = 3  # assigns 3 to the variables `x` and `y`
echo "x ", x  # outputs "x 3"
echo "y ", y  # outputs "y 3"
x = 42        # changes `x` to 42 without changing `y`
echo "x ", x  # outputs "x 42"
echo "y ", y  # outputs "y 3"
```
Note that declaring multiple variables with a single assignment which calls a procedure can have unexpected results: the compiler will unroll the assignments and end up calling the procedure several times. If the result of the procedure depends on side effects, your variables may end up having different values! For safety use only constant values


#### Let
The let statement works like the var statement but the declared symbols are single assignment variables: After the initialization their value cannot change:

```nim
let            # Use let to declare and bind variables *once*.
  legs = 400   # legs is immutable.
  arms = 2_000 # _ are ignored and are useful for long numbers.
  aboutPi = 3.15
  x = "abc" # introduces a new variable `x` and binds a value to it

x = "xyz"     # Illegal: assignment to `x`
```

#### Constants
Constants are symbols which are bound to a value. The constant's value cannot change. The compiler must be able to evaluate the expression in a constant declaration at compile time.
Indentation can be used after the const keyword to list a whole section of constants:
```nim
const            # Constants are computed at compile time. This provides
  debug = true   # performance and is useful in compile time expressions.
  compileBadCode = false
```

### Control flow statements

The greetings program consists of 3 statements that are executed sequentially. Only the most primitive programs can get away with that: branching and looping are needed too.

### If statement

The if statement is one way to branch the control flow:
```nim
let name = readLine(stdin)
if name == "":
  echo "Poor soul, you lost your name?"
elif name == "name":
  echo "Very funny, your name is name."
else:
  echo "Hi, ", name, "!"
```

There can be zero or more elif parts, and the else part is optional. The keyword elif is short for else if, and is useful to avoid excessive indentation. (The "" is the empty string. It contains no characters.)

### Case statement

Another way to branch is provided by the case statement. A case statement is a multi-branch:
```nim
let name = readLine(stdin)
case name
of "":
  echo "Poor soul, you lost your name?"
of "name":
  echo "Very funny, your name is name."
of "Dave", "Frank":
  echo "Cool name!"
else:
  echo "Hi, ", name, "!"
```

As it can be seen, for an of branch a comma separated list of values is also allowed.

The case statement can deal with integers, other ordinal types and strings. (What an ordinal type is will be explained soon.) For integers or other ordinal types value ranges are also possible:

```
# this statement will be explained later:
from strutils import parseInt

echo "A number please: "
let n = parseInt(readLine(stdin))
case n
of 0..2, 4..7: echo "The number is in the set: {0, 1, 2, 4, 5, 6, 7}"
of 3, 8: echo "The number is 3 or 8"
```
However, the above code does not compile: the reason is that you have to cover every value that n may contain, but the code only handles the values 0..8. Since it is not very practical to list every other possible integer (though it is possible thanks to the range notation), we fix this by telling the compiler that for every other value nothing should be done:
```
...
case n
of 0..2, 4..7: echo "The number is in the set: {0, 1, 2, 4, 5, 6, 7}"
of 3, 8: echo "The number is 3 or 8"
else: discard
```

The empty discard statement is a do nothing statement. The compiler knows that a case statement with an else part cannot fail and thus the error disappears. Note that it is impossible to cover all possible string values: that is why string cases always need an else branch.

In general the case statement is used for subrange types or enumerations where it is of great help that the compiler checks that you covered any possible value.

#### While statement

The while statement is a simple looping construct:
```
echo "What's your name? "
var name = readLine(stdin)
while name == "":
  echo "Please tell me your name: "
  name = readLine(stdin)
  # no ``var``, because we do not declare a new variable here
```
The example uses a while loop to keep asking the users for their name, as long as the user types in nothing (only presses RETURN).

#### For statement

The for statement is a construct to loop over any element an iterator provides. The example uses the built-in countup iterator:
```
echo "Counting to ten: "
for i in countup(1, 10):
  echo i
# --> Outputs 1 2 3 4 5 6 7 8 9 10 on different lines
```
The built-in $ operator turns an integer (int) and many other types into a string. The variable i is implicitly declared by the for loop and has the type int, because that is what countup returns. i runs through the values 1, 2, .., 10. Each value is echo-ed. This code does the same:
```
echo "Counting to 10: "
var i = 1
while i <= 10:
  echo i
  inc(i) # increment i by 1
# --> Outputs 1 2 3 4 5 6 7 8 9 10 on different lines
```
Counting down can be achieved as easily (but is less often needed):
```
echo "Counting down from 10 to 1: "
for i in countdown(10, 1):
  echo i
# --> Outputs 10 9 8 7 6 5 4 3 2 1 on different lines
```

Since counting up occurs so often in programs, Nim also has a .. iterator that does the same:
```
for i in 1..10:
  ...
```

Zero-indexed counting have two shortcuts ..< and ..^ to simplify counting to one less than the higher index:
```
for i in 0..<10:
  ...  # 0..9
```
or
```
var s = "some string"
for i in 0..<s.len:
  ...
```
Other useful iterators for collections (like arrays and sequences) are
items and mitems, which provides immutable and mutable elements respectively, and
pairs and mpairs which provides the element and an index number (immutable and mutable respectively)
```nim
for index, item in ["a","b"].pairs:
  echo item, " at index ", index
# => a at index 0
# => b at index 1
```

#### Scopes and the block statement

Control flow statements have a feature not covered yet: they open a new scope. This means that in the following example, x is not accessible outside the loop:
```nim
while false:
  var x = "hi"
echo x # does not work
```
A while (for) statement introduces an implicit block. Identifiers are only visible within the block they have been declared. The block statement can be used to open a new block explicitly:
```nim
block myblock:
  var x = "hi"
echo x # does not work either
```
The block's label (myblock in the example) is optional

#### Break statement

A block can be left prematurely with a break statement. The break statement can leave a while, for, or a  block statement. It leaves the innermost construct, unless a label of a block is given:
```nim
block myblock:
  echo "entering block"
  while true:
    echo "looping"
    break # leaves the loop, but not the block
  echo "still in block
```
### Continue statement

Like in many other programming languages, a continue statement starts the next iteration immediately:
```nim
while true:
  let x = readLine(stdin)
  if x == "": continue
  echo x
```

### When statement
Example:
```nim
when system.hostOS == "windows":
  echo "running on Windows!"
elif system.hostOS == "linux":
  echo "running on Linux!"
elif system.hostOS == "macosx":
  echo "running on Mac OS X!"
else:
  echo "unknown operating system"
````
The when statement is almost identical to the if statement, but with these differences:

- Each condition must be a constant expression since it is evaluated by the compiler.
- The statements within a branch do not open a new scope.
- The compiler checks the semantics and produces code only for the statements that belong to the first condition that evaluates to true.
- The when statement is useful for writing platform specific code, similar to the #ifdef construct in the C programming language.

**Note:** To comment out a large piece of code, it is often better to use a when false: statement than to use real comments. This way nesting is possible.

### Procedures

To define new commands like echo and readLine in the examples, the concept of a procedure is needed. (Some languages call them methods or functions.) In Nim new procedures are defined with the proc keyword:
```nim
proc yes(question: string): bool =
  echo question, " (y/n)"
  while true:
    case readLine(stdin)
    of "y", "Y", "yes", "Yes": return true
    of "n", "N", "no", "No": return false
    else: echo "Please be clear: yes or no"

if yes("Should I delete all your important files?"):
  echo "I'm sorry Dave, I'm afraid I can't do that."
else:
  echo "I think you know what the problem is just as well as I do."
```

This example shows a procedure named yes that asks the user a question and returns true if they answered "yes" (or something similar) and returns false if they answered "no" (or something similar). A return statement leaves the procedure (and therefore the while loop) immediately. The (question: string): bool syntax describes that the procedure expects a parameter named question of type string and returns a value of type bool. The bool type is built-in: the only valid values for bool are true and false. The conditions in if or while statements must be of type bool.

Some terminology: in the example question is called a (formal) parameter, "Should I..." is called an argument that is passed to this parameter

#### Special proc
```
proc binom(n, k: int): int {..} #Computes the binomial coefficient
proc isPowerOfTwo(x: int): bool {..} #Returns true, if x is a power of two, false otherwise. #Zero and negative numbers are not a power of two.
proc sqrt(x: float64): float64 {..} #Computes the square root of x.
proc floor(x: float64): float64 {..} #Computes the floor function (i.e., the largest integer not #greater than x)
drinks.add("Milk")

if "Milk" in drinks:
  echo "We have Milk and ", drinks.len - 1, " other drinks"

let myDrink = drinks[2]

#
# Defining Types
#

# Defining your own types puts the compiler to work for you. It's what makes
# static typing powerful and useful.

type
  Name = string # A type alias gives you a new type that is interchangable
  Age = int     # with the old type but is more descriptive.
  Person = tuple[name: Name, age: Age] # Define data structures too.
  AnotherSyntax = tuple
    fieldOne: string
    secondField: int

var
  john: Person = (name: "John B.", age: 17)
  newage: int = 18 # It would be better to use Age than int

john.age = newage # But still works because int and Age are synonyms

type
  Cash = distinct int    # `distinct` makes a new type incompatible with its
  Desc = distinct string # base type.

var
  money: Cash = 100.Cash # `.Cash` converts the int to our type
  description: Desc  = "Interesting".Desc

when compileBadCode:
  john.age  = money        # Error! age is of type int and money is Cash
  john.name = description  # Compiler says: "No way!"

#
# More Types and Data Structures
#

# Enumerations allow a type to have one of a limited number of values

type
  Color = enum cRed, cBlue, cGreen
  Direction = enum # Alternative formating
    dNorth
    dWest
    dEast
    dSouth
var
  orient = dNorth # `orient` is of type Direction, with the value `dNorth`
  pixel = cGreen # `pixel` is of type Color, with the value `cGreen`

discard dNorth > dEast # Enums are usually an "ordinal" type

# Subranges specify a limited valid range

type
  DieFaces = range[1..20] # Only an int from 1 to 20 is a valid value
var
  my_roll: DieFaces = 13

when compileBadCode:
  my_roll = 23 # Error!

# Arrays

type
  RollCounter = array[DieFaces, int]  # Array's are fixed length and
  DirNames = array[Direction, string] # indexed by any ordinal type.
  Truths = array[42..44, bool]
var
  counter: RollCounter
  directions: DirNames
  possible: Truths

possible = [false, false, false] # Literal arrays are created with [V1,..,Vn]
possible[42] = true

directions[dNorth] = "Ahh. The Great White North!"
directions[dWest] = "No, don't go there."

my_roll = 13
counter[my_roll] += 1
counter[my_roll] += 1

var anotherArray = ["Default index", "starts at", "0"]

# More data structures are available, including tables, sets, lists, queues,
# and crit bit trees.
# http://nim-lang.org/docs/lib.html#collections-and-algorithms

#
# IO and Control Flow
#

# `case`, `readLine()`

echo "Read any good books lately?"
case readLine(stdin)
of "no", "No":
  echo "Go to your local library."
of "yes", "Yes":
  echo "Carry on, then."
else:
  echo "That's great; I assume."

# `while`, `if`, `continue`, `break`

import strutils as str # http://nim-lang.org/docs/strutils.html
echo "I'm thinking of a number between 41 and 43. Guess which!"
let number: int = 42
var
  raw_guess: string
  guess: int
while guess != number:
  raw_guess = readLine(stdin)
  if raw_guess == "": continue # Skip this iteration
  guess = str.parseInt(raw_guess)
  if guess == 1001:
    echo("AAAAAAGGG!")
    break
  elif guess > number:
    echo("Nope. Too high.")
  elif guess < number:
    echo(guess, " is too low")
  else:
    echo("Yeeeeeehaw!")

#
# Iteration
#

for i, elem in ["Yes", "No", "Maybe so"]: # Or just `for elem in`
  echo(elem, " is at index: ", i)

for k, v in items(@[(person: "You", power: 100), (person: "Me", power: 9000)]):
  echo v

let myString = """
an <example>
`string` to
play with
""" # Multiline raw string

for line in splitLines(myString):
  echo(line)

for i, c in myString:       # Index and letter. Or `for j in` for just letter
  if i mod 2 == 0: continue # Compact `if` form
  elif c == 'X': break
  else: echo(c)

#
# Procedures
#

type Answer = enum aYes, aNo

proc ask(question: string): Answer =
  echo(question, " (y/n)")
  while true:
    case readLine(stdin)
    of "y", "Y", "yes", "Yes":
      return Answer.aYes  # Enums can be qualified
    of "n", "N", "no", "No":
      return Answer.aNo
    else: echo("Please be clear: yes or no")

proc addSugar(amount: int = 2) = # Default amount is 2, returns nothing
  assert(amount > 0 and amount < 9000, "Crazy Sugar")
  for a in 1..amount:
    echo(a, " sugar...")

case ask("Would you like sugar in your tea?")
of aYes:
  addSugar(3)
of aNo:
  echo "Oh do take a little!"
  addSugar()
# No need for an `else` here. Only `yes` and `no` are possible.

#
# FFI
#

# Because Nim compiles to C, FFI is easy:

proc strcmp(a, b: cstring): cint {.importc: "strcmp", nodecl.}

let cmp = strcmp("C?", "Easy!")
```
