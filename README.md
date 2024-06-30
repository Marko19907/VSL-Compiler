# Very Simple Language Compiler

![Linux](https://img.shields.io/github/actions/workflow/status/Marko19907/VSL-Compiler/.github%2Fworkflows%2Flinux-build.yml?label=Linux%20CI)
![macOS](https://img.shields.io/github/actions/workflow/status/Marko19907/VSL-Compiler/.github%2Fworkflows%2Fmacos-build.yml?label=macOS%20CI)

This repository contains the source code for the Very Simple Language (VSL) compiler, a 64-bit version of the language from the book "Introduction to Compiling Techniques" by Jeremy P. Bennett. 

The compiler is a semester project for the Compiler Construction (TDT4205) course at NTNU.


## Usage

Using GitHub Codespaces, checking out the compiler is straightforward. It provides an environment with all necessary tools installed, and the code ready to be built and run. 

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/Marko19907/VSL-Compiler)

If you prefer to build the compiler locally, follow the instructions below.

### Prerequisites

To build the compiler locally, you will need:

 - CMake v. 3.21
 - Python v. 3.10
 - gcc
 - flex v. 2.6
 - bison v. 3.5
 - GNU make and Ninja
 
### Building

Create the build files with Ninja. This step is required only once.

``` sh
cmake -B build -GNinja
```

Then, compile the code. Run this command whenever you make changes to the code.

``` sh
cmake --build build
```

The tests can be run with the following command from the `tests/` directory:

``` sh
make check-all
```

### Running
The final binary can be found at `build/vslc`. Use the `--help` option for more details on available commands.

Input is passed to `stdin`, output is printed to `stdout`.

To pass a file to the compiler, for example:
``` sh
build/vslc -c < tests/codegen/sieve.vsl
```

To write the output to a file and execute it:
``` sh
build/vslc -c < tests/codegen/sieve.vsl > sieve.s
gcc -o sieve sieve.s
./sieve 100
```

Note that `100` is the argument for the sieve program, representing the upper limit for finding prime numbers. `gcc` is used to compile the generated assembly code into an executable binary.


## VSL Language Features

The compiler supports the following features:

* Variables and arrays
* Global and local variables
* Arithmetic and logical expressions
* if-then, if-then-else, and if-then-else-if-else statements
* While loops and break statements
* Nested if, while, and break statements
* print statements with variables and strings
* Functions with parameters and return values

## VSL syntax

VSL (Very Simple Language) is an imperative language with a straightforward syntax. Below is a detailed description of its key components with concise examples.

### Variables and Arrays

#### Declaring Variables and Arrays
* **Variables**: Use the var keyword.
* **Arrays**: Declare arrays with a specified size.

```vsl
var x, y, z
var array[10]
```

#### Assignment (Declaration vs. Binding)
* **Declaration**: Introducing a variable or array without assigning it a value.
* **Binding**: Assigning a value to a declared variable using the `:=` operator.
* A variable must be declared before it can be bound to a value.

```vsl
var x      // Declaration
x := 10    // Binding
```

#### Global and Local Variables
* **Global Variables**: Declared outside of any function.
* **Local Variables**: Declared within functions or blocks.

```vsl
var global_var, global_array[5]

func example() begin
    var local_var, local_array[3]
end
```

### Operators

#### Supported Operators
* **Arithmetic Operators**: `+`, `-`, `*`, `/`
* **Bitwise Operators**: `<<` (left shift), `>>` (right shift)

```vsl
func main() begin
    var x, y, z
    x := y + z
    y := 5 + z * 3
    z := z * z - 3
    x := (x + y) / z
    x := y << 1
    y := x >> 1
end
```

#### Assignments and Arithmetic
* **Assignment**: Use the `:=` operator to bind values to variables. Reassigning a value is allowed.
* **Arithmetic**: Standard arithmetic operations are supported.

```vsl
x := y + z
y := 5 + z * 3
z := z * z - 3
x := (x + y) / z
```

### Print Statements

* Use the `print` statement to output variables and strings to `stdout`.
* Separate variables and strings with commas.

```vsl
print "Hello, World!"
print x, y, z
print "Value of x is: ", x
```

### Comments
* Single-line comments start with `//`.

```vsl
// This is a comment
var x // This is an inline comment
```

### Control Flow

#### If-Then Statements

```vsl
if condition then begin
    // code
end
```

#### If-Then-Else Statements

```vsl
if condition then
    // code
else
    // code
```

#### If-Then-Else-If-Else Statements

```vsl
if condition1 then begin
    // code
    // some more code
end
else if condition2 then
    // code
else
    // code
```

Note: Scope is defined by the `begin` and `end` keywords. If an `if` statement has only one line of code, the `begin` and `end` keywords are optional.

#### While Loops

* Use the `while` keyword to create a loop. Break statements can be used to exit the loop. 

```vsl
while condition do begin
    // code
    if another_condition then
        break
end
```

### Functions

#### Function Declaration and Return

* **Functions**: Defined using the func keyword.
* **Parameters**: Specified in parentheses. Any number of parameters can be passed.
* **Body**: Enclosed in begin and end blocks.
* **Return**: Functions can return values using the return statement.

```vsl
func add(a, b) begin
    return a + b
end

func main() begin
    print add(40, 2)
end
```

### Example Programs

Examples of VSL code can be found in the [tests/codegen](tests/codegen) folder.

## Structure

The compiler consists of three main parts: the front-end, middle-end, and back-end.

### Front-end
The front-end comprises two components:

* **Scanner**: Converts a stream of characters into tokens (implemented using Flex)
* **Parser**: Converts tokens into a parse tree (implemented using Bison)

### Middle-end
This part applies optimizations to the syntax tree. Constant folding and peephole optimization are implemented.

### Back-end
The backend generates x86-64 assembly code from the syntax tree.


## Limitations

This compiler is a school semester project and as such has several significant limitations:

* Only integers are supported; floating-point numbers are not.
* Types are not supported; all variables are integers, arrays of integers, or strings.
* No liveness analysis or register allocation is performed, resulting in inefficient generated code.
* Primarily supports Linux; experimental macOS support is available but tested only with GitHub Actions CI on Apple Silicon runners. Windows is not supported.

Despite these limitations, the compiler can compile and run various simple programs. Please do not use it for critical tasks, as no warranty is provided or implied. 
