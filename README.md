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
```nim
var name = readLine(stdin)
```
### Numbers

Numerical literals are written as in most other languages. As a special twist, underscores are allowed for better readability: 1_000_000 (one million). A number that contains a dot (or 'e' or 'E') is a floating point literal:  1.0e9 (one billion). Hexadecimal literals are prefixed with 0x, binary literals with 0b and octal literals with 0o. A leading zero alone does not produce an octal

### Var statement
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
### Assignment operator

= is the assignment operator. The assignment operator cannot be overloaded, overwritten or forbidden, but this might change in a future version of Nim. You can declare multiple variables with a single assignment statement and all the variables will have the same value:

```nim
var x, y = 3  # assigns 3 to the variables `x` and `y`
echo "x ", x  # outputs "x 3"
x = 42        # changes `x` to 42 without changing `y`
echo "x ", x  # outputs "x 42"
```
Note that declaring multiple variables with a single assignment which calls a procedure can have unexpected results: the compiler will unroll the assignments and end up calling the procedure several times. If the result of the procedure depends on side effects, your variables may end up having different values! For safety use only constant values

### Let
The let statement works like the var statement but the declared symbols are single assignment variables: After the initialization their value cannot change:

```nim
let            # Use let to declare and bind variables *once*.
  legs = 400   # legs is immutable.
  arms = 2_000 # _ are ignored and are useful for long numbers.
  aboutPi = 3.15
  x = "abc" # introduces a new variable `x` and binds a value to it

x = "xyz"     # Illegal: assignment to `x`
```

### Constants
Constants are symbols which are bound to a value. The constant's value cannot change. The compiler must be able to evaluate the expression in a constant declaration at compile time.
Indentation can be used after the const keyword to list a whole section of constants:
```nim
const            # Constants are computed at compile time. This provides
  debug = true   # performance and is useful in compile time expressions.
  compileBadCode = false
```

## Control flow statements

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

```nim
from strutils import parseInt  #header file

echo "A number please: "
let n = parseInt(readLine(stdin))
case n
of 0..2, 4..7: echo "The number is in the set: {0, 1, 2, 4, 5, 6, 7}"
of 3, 8: echo "The number is 3 or 8"
```
However, the above code does not compile: the reason is that you have to cover every value that n may contain, but the code only handles the values 0..8. Since it is not very practical to list every other possible integer (though it is possible thanks to the range notation), we fix this by telling the compiler that for every other value nothing should be done:
```nim
...
case n
of 0..2, 4..7: echo "The number is in the set: {0, 1, 2, 4, 5, 6, 7}"
of 3, 8: echo "The number is 3 or 8"
else: discard
```

The empty discard statement is a do nothing statement. The compiler knows that a case statement with an else part cannot fail and thus the error disappears. Note that it is impossible to cover all possible string values: that is why string cases always need an else branch.

In general the case statement is used for subrange types or enumerations where it is of great help that the compiler checks that you covered any possible value.

### While statement

The while statement is a simple looping construct:
```nim
echo "What's your name? "
var name = readLine(stdin)
var x : int
x = 0
while x < 3:
  echo "Please tell me your friend's name: "
  name = readLine(stdin)
  inc(x)
  # no ``var``, because we do not declare a new variable here
```
The example uses a while loop to keep asking the users for their name, as long as the user types in nothing (only presses RETURN).

### For statement

The for statement is a construct to loop over any element an iterator provides. The example uses the built-in countup iterator:
```nim
echo "Counting to ten: "
for i in countup(1, 10):
  echo i
# --> Outputs 1 2 3 4 5 6 7 8 9 10 on different lines
```
The built-in $ operator turns an integer (int) and many other types into a string. The variable i is implicitly declared by the for loop and has the type int, because that is what countup returns. i runs through the values 1, 2, .., 10. Each value is echo-ed. This code does the same:
```nim
echo "Counting to 10: "
var i = 1
while i <= 10:
  echo i
  inc(i) # increment i by 1
# --> Outputs 1 2 3 4 5 6 7 8 9 10 on different lines
```
Counting down can be achieved as easily (but is less often needed):
```nim
echo "Counting down from 10 to 1: "
for i in countdown(10, 1):
  echo i
# --> Outputs 10 9 8 7 6 5 4 3 2 1 on different lines
```

Since counting up occurs so often in programs, Nim also has a .. iterator that does the same:
```nim
for i in 1..10:
  ...
```

Zero-indexed counting have two shortcuts ..< and ..^ to simplify counting to one less than the higher index:
```nim
for i in 0..<10:
  ...  # 0..9
```
or
```nim
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

### Scopes and the block statement

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

### Break statement

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
else:
  echo "unknown operating system"
```
The when statement is almost identical to the if statement, but with these differences:

- Each condition must be a constant expression since it is evaluated by the compiler.
- The statements within a branch do not open a new scope.
- The compiler checks the semantics and produces code only for the statements that belong to the first condition that evaluates to true.
- The when statement is useful for writing platform specific code, similar to the #ifdef construct in the C programming language.

**Note:** To comment out a large piece of code, it is often better to use a when false: statement than to use real comments. This way nesting is possible.

## Procedures

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

### Special proc
```nim

import math #include this header file

proc isPowerOfTwo(x: int): bool {..} #Returns true, if x is a power of two, false otherwise. 
#Zero and negative numbers are not a power of two.
proc sqrt(x: float64): float64 {..} #Computes the square root of x.
proc floor(x: float64): float64 {..} #Computes the floor function
proc ln(x: float64): float64 {..} #Computes the natural log of x
proc exp(x: float64): float64 {..} #Computes the exponential function of x (pow(E, x)) 
proc fmod(x, y: float64): float64 {..} #Computes the remainder of x divided by y
     # eg: echo fmod(-2.5, 0.3) ## -0.1
proc `mod`[T: float32 | float64](x, y: T): T #Computes the modulo operation for float operators.
# Equivalent to x - y * floor(x/y). Note that the remainder will always have the same sign as the divisor.
     # eg: echo (4.0 mod -3.1) # -2.2
proc `^`[T](x, y: T): T #Computes x to the power y`. ``x must be non-negative, 
#use pow <#pow,float,float> for negative #exponents.
proc lcm[T](x, y: T): T #Computes the least common multiple of x and y

```

### Result variable

A procedure that returns a value has an implicit result variable declared that represents the return value. A  return statement with no expression is a shorthand for return result. The result value is always returned automatically at the end of a procedure if there is no return statement at the exit.
```nim
proc sumTillNegative(x: varargs[int]): int =
  for i in x:
    if i < 0:
      return
    result = result + i

echo sumTillNegative() # echos 0
echo sumTillNegative(3, 4, 5) # echos 12
echo sumTillNegative(3, 4 , -1 , 6) # echos 7
```
The result variable is already implicitly declared at the start of the function, so declaring it again with 'var result', for example, would shadow it with a normal variable of the same name. The result variable is also already initialised with the type's default value. Note that referential data types will be nil at the start of the procedure, and thus may require manual initialisation.

### Parameters

Parameters are constant in the procedure body. By default, their value cannot be changed because this allows the compiler to implement parameter passing in the most efficient way. If a mutable variable is needed inside the procedure, it has to be declared with var in the procedure body. Shadowing the parameter name is possible, and actually an idiom:
```nim
proc printSeq(s: seq, nprinted: int = -1) =
  var nprinted = if nprinted == -1: s.len else: min(nprinted, s.len)
  for i in 0 .. <nprinted:
    echo s[i]
```
If the procedure needs to modify the argument for the caller, a var parameter can be used:

```nim
proc divmod(a, b: int; res, remainder: var int) =
  res = a div b        # integer division
  remainder = a mod b  # integer modulo operation
var
  x, y: int
divmod(8, 5, x, y) # modifies x and y
echo x
echo y
```

In the example, res and remainder are var parameters. Var parameters can be modified by the procedure and the changes are visible to the caller. Note that the above example would better make use of a tuple as a return value instead of using var parameters.

### Operators

The Nim library makes heavy use of overloading - one reason for this is that each operator like + is just an overloaded proc. The parser lets you use operators in infix notation (a + b) or prefix notation (+ a). An infix operator always receives two arguments, a prefix operator always one. (Postfix operators are not possible, because this would be ambiguous: does a @ @ b mean (a) @ (@b) or (a@) @ (b)? It always means  (a) @ (@b), because there are no postfix operators in Nim.)

Apart from a few built-in keyword operators such as and, or, not, operators always consist of these characters: + - * \ / < > = @ $ ~ & % ! ? ^ . |

User defined operators are allowed. Nothing stops you from defining your own @!?+~ operator, but doing so may reduce readability.

The operator's precedence is determined by its first character. The details can be found in the manual.

To define a new operator enclose the operator in backticks "``":
```nim
proc `$` (x: myDataType): string = ...
# now the $ operator also works with myDataType, overloading resolution
# ensures that $ works for built-in types just like before
```
The "``" notation can also be used to call an operator just like any other procedure:
```nim
if `==`( `+`(3, 4), 7): echo "True"
```

# Iterators

Let's return to the simple counting example:
```nim
echo "Counting to ten: "
for i in countup(1, 10):
  echo i
```
Can a countup proc be written that supports this loop? Lets try:
```nim
proc countup(a, b: int): int =
  var res = a
  while res <= b:
    return res
    inc(res)
```

However, this does not work. The problem is that the procedure should not only return, but return and continue after an iteration has finished. This return and continue is called a yield statement. Now the only thing left to do is to replace the proc keyword by iterator and here it is - our first iterator:
```nim
iterator countup(a, b: int): int =
  var res = a
  while res <= b:
    yield res
    inc(res)
```

Iterators look very similar to procedures, but there are several important differences:
It is common practice to wrap iterators in procs of the same name which accumulate the result of the iterator and return it as a sequence, like split from the strutils module.

## Basic types

This section deals with the basic built-in types and the operations that are available for them in detail.Defining your own types puts the compiler to work for you. It's what makes static typing powerful and useful.

### Booleans

Nim's boolean type is called bool and consists of the two pre-defined values true and false. Conditions in while, if, elif, and when statements must be of type bool.

The operators not, and, or, xor, <, <=, >, >=, !=, == are defined for the bool type. The and and  or operators perform short-circuit evaluation. For example:
```nim
while p != nil and p.name != "xyz":
  # p.name is not evaluated if p == nil
  p = p.next
``` 
### Characters

The character type is called char. Its size is always one byte, so it cannot represent most UTF-8 characters; but it can represent one of the bytes that makes up a multi-byte UTF-8 character. The reason for this is efficiency: for the overwhelming majority of use-cases, the resulting programs will still handle UTF-8 properly as UTF-8 was specially designed for this. Character literals are enclosed in single quotes.

Chars can be compared with the ==, <, <=, >, >= operators. The $ operator converts a char to a string. Chars cannot be mixed with integers; to get the ordinal value of a char use the ord proc. Converting from an integer to a char is done with the chr proc.

### Strings

```nim
if s[i] == 'a' and s[i+1] == 'b':
  # no need to check whether ``i < len(s)``!
  ...
```
The assignment operator for strings copies the string. You can use the & operator to concatenate strings and  add to append to a string.

### Integers

The default integer type is int. Integer literals can have a type suffix to specify a non-default integer type:
```nim
let
  x = 0     # x is of type ``int``
  y = 0'i8  # y is of type ``int8``
  z = 0'i64 # z is of type ``int64``
  u = 0'u   # u is of type ``uint``
```
Most often integers are used for counting objects that reside in memory, so int has the same size as a pointer.

The common operators + - * div mod < <= == != > >= are defined for integers. The and or xor not operators are also defined for integers, and provide bitwise operations. Left bit shifting is done with the shl, right shifting with the shr operator. Bit shifting operators always treat their arguments as unsigned. For arithmetic bit shifts ordinary multiplication or division can be used.

Unsigned operations all wrap around; they cannot lead to over- or under-flow errors.

Lossless Automatic type conversion is performed in expressions where different kinds of integer types are used. However, if the type conversion would cause loss of information, the EOutOfRange exception is raised (if the error cannot be detected at compile time).

### Floats

Nim has these floating point types built-in: float float32 float64. The default float type is float. In the current implementation, float is always 64-bits. Float literals can have a type suffix to specify a non-default float type:
```nim
var
  x = 0.0      # x is of type ``float``
  y = 0.0'f32  # y is of type ``float32``
  z = 0.0'f64  # z is of type ``float64``
```
The common operators + - * / < <= == != > >= are defined for floats.
Automatic type conversion in expressions with different kinds of floating point types is performed: the smaller type is converted to the larger. Integer types are not converted to floating point types automatically, nor vice versa. Use the toInt and toFloat procs for these conversions.

## Type Conversion

Conversion between basic types is performed by using the type as a function:
```nim
var
  x: int32 = 1.int32   # same as calling int32(1)
  y: int8  = int8('a') # 'a' == 97'i8
  z: float = 2.5       # int(2.5) rounds down to 2
  sum: int = int(x) + int(y) + int(z) # sum == 100

```

### Defining Types

```nim
type
  DieFaces = range[1..20] # Only an int from 1 to 20 is a valid value
var
  my_roll: DieFaces = 13
  
when compileBadCode:
  my_roll = 23 # Error!
```

### Arrays
An array is a simple fixed length container. Each element in an array has the same type. The array's index type can be any ordinal type. They are like classic C arrays, their size is specified at compile-time and cannot be given or changed at runtime.

Arrays can be constructed using []

```nim
type
  IntArray = array[0..5, int] # an array that is indexed with 0..5
var
  x: IntArray
x = [1, 2, 3, 4, 5, 6]
for i in low(x)..high(x):
  echo x[i]
```
The notation x[i] is used to access the i-th element of x. Array access is always bounds checked (at compile-time or at runtime). These checks can be disabled via pragmas or invoking the compiler with the  --bound_checks:off command line switch.

Arrays are value types, like any other Nim type. The assignment operator copies the whole array contents.

The built-in len proc returns the array's length. low(a) returns the lowest valid index for the array a and high(a) the highest valid index.
```nim
type
  Direction = enum
    north, east, south, west
  BlinkLights = enum
    off, on, slowBlink, mediumBlink, fastBlink
  LevelSetting = array[north..west, BlinkLights]
var
  level: LevelSetting
level[north] = on
level[south] = slowBlink
level[east] = fastBlink
echo repr(level)  # --> [on, fastBlink, slowBlink, off]
echo low(level)   # --> north
echo len(level)   # --> 4
echo high(level)  # --> west
```
The syntax for nested arrays (multidimensional) in other languages is a matter of appending more brackets because usually each dimension is restricted to the same index type as the others. In Nim you can have different dimensions with different index types, so the nesting syntax is slightly different. Building on the previous example where a level is defined as an array of enums indexed by yet another enum, we can add the following lines to add a light tower type subdivided in height levels accessed through their integer index:
```nim
type
  LightTower = array[1..10, LevelSetting]
var
  tower: LightTower
tower[1][north] = slowBlink
tower[1][east] = mediumBlink
echo len(tower)     # --> 10
echo len(tower[1])  # --> 4
echo repr(tower)    # --> [[slowBlink, mediumBlink, ...more output..
# The following lines don't compile due to type mismatch errors
#tower[north][east] = on
#tower[0][1] = on
```
Note how the built-in len proc returns only the array's first dimension length. Another way of defining the  LightTower to better illustrate its nested nature would be to omit the previous definition of the  LevelSetting type and instead write it embedded directly as the type of the first dimension:
```nim
type
  LightTower = array[1..10, array[north..west, BlinkLights]]
```
It is quite common to have arrays start at zero, so there's a shortcut syntax to specify a range from zero to the specified index minus one:
```nim
type
  IntArray = array[0..5, int] # an array that is indexed with 0..5
  QuickArray = array[6, array[2,int]]  # a 5*2 matrix
var
  x: IntArray
  y: QuickArray
x = [1, 2, 3, 4, 5, 6]
for i in low(x)..high(x):
  echo x[i]
for i in 0..5:
  echo y[i][0]," ",y[i][1]
```

### Sort

Default Nim sort (an implementation of merge sort). The sorting is guaranteed to be stable and the worst case is guaranteed to be O(n log n). The current implementation uses an iterative mergesort to achieve this. It uses a temporary sequence of length a.len div 2. Currently Nim does not support a sensible default argument for cmp, so you have to provide one of your own. However, the system.cmp procs can be used:
```nim
include algorithm  #header file for sort function

sort(myIntArray, system.cmp[int])
# do not use cmp[string] here as we want to use the specialized
# overload:
sort(myStrArray, system.cmp)
```

# Sequences

Sequences are similar to arrays but of dynamic length which may change during runtime (like strings). Since sequences are resizable they are always allocated on the heap and garbage collected.Sequences are always indexed with an int starting at position 0. The len, low and high operations are available for sequences too. The notation x[i] can be used to access the i-th element of x. Sequence variables are initialized with nil.
```nim
var
  x: seq[int] # a reference to a sequence of integers
x = @[1, 2, 3, 4, 5, 6] # the @ turns the array into a sequence allocated on the heap
```

### From statement

We have already seen the simple import statement that just imports all exported symbols. An alternative that only imports listed symbols is the from import statement:
```nim
from mymodule import x, y, z
```
The from statement can also force namespace qualification on symbols, thereby making symbols available, but needing to be qualified to be used.

```nim
from mymodule import x, y, z
x()           # use x without any qualificationfrom mymodule import nil
mymodule.x()  # must qualify x with the module name as prefix
x()           # using x here without qualification is a compile error
```
Since module names are generally long to be descriptive, you can also define a shorter alias to use when qualifying symbols.
```nim
from mymodule as m import nil
m.x()         # m is aliasing mymodule
```
### Include statement

The include statement does something fundamentally different than importing a module: it merely includes the contents of a file. The include statement is useful to split up a large module into several files:
```nim
include fileA, fileB, fileC
```

## Macros

Macros enable advanced compile-time code transformations, but they cannot change Nim's syntax. However, this is no real restriction because Nim's syntax is flexible enough anyway. Macros have to be implemented in pure Nim code if the foreign function interface (FFI) is not enabled in the compiler, but other than that restriction (which at some point in the future will go away) you can write any kind of Nim code and the compiler will run it at compile time.

### Building your first macro

To give a footstart to writing macros we will show now how to turn your typical dynamic code into something that compiles statically. For the exercise we will use the following snippet of code as the starting point:

```nim
import strutils 
import tables

proc readCfgAtRuntime(cfgFilename: string): Table[string, string] =
  let
    inputString = readFile(cfgFilename)
  var
    source = ""
    a,b : int
  
  result = initTable[string, string]()
  for line in inputString.splitLines:
    # Ignore empty lines
    if line.len < 1: continue
    var chunks = split(line, ',')
    if chunks.len != 2:
      quit("Input needs comma split values, got: " & line)
    result[chunks[0]] = chunks[1]
    var chunkInt = split(readLine(stdin),' ')
    a = parseInt(chunkInt[0])
    b = parseInt(chunkInt[1]) 
  if result.len < 1: quit("Input file empty!")

```  
