## Get Programming with Haskell

By Will Kurt

### Unit 2 - Introducing types

- Types determine the kinds of computation that can be done on certain data.
- In any programming language, you need to know the types you're working with so you can either throw an error when types don’t match or do some sort of automatic conversion using type transformation rules.
- Dynamically typed languages (Python, JS etc.): Flexible type systems, checked at runtime. This leads to errors at runtime which can be dangerous in production if you ship something that will run (and break) 6 months in the future. To address this issue, we write unit tests for everything.
- Statically typed languages (Java, C# etc.): If type errors occur, compilation won't occur. This prevents bugs from creeping into running programs. The downside here is that programmers need to explictly declare types for everything.
- Haskell is statically typed but uses heavy type inference which allows programmers to leave out types in declarations.

Bonus:

- Dynamically typed languages get bad rep by people who don't understand why they came into existence. They exist to solve a particular problem.
- Back in the day when memory was limited, the `loading` and `linking` processes of the compile process used to take a really long time to complete. This meant that testing software took time, deploying took even longer.
- This was one of the main reasons why dynamically typed languages came into existence. Moving process like type-checking to runtime meant that build times and deploy times became much faster.
- The point is that there are trade-offs to each decision. Dynamically typed languages get a bad rep for no clear reason these days. People just don't know what problem these languages are meant to solve.

#### Lesson 11: Type basics

- Haskell's type system is what distinguishes it from other programming languages. It is very robust.
- Types in Haskell all start with uppercase letters to distinguish them from functions in Haskell. eg. `Int`
- Some types in Haskell are bounded ie. they can hold values only in a particular range. For example, `Int` is bounded while its unbounded counterpart `Integer` isn't.
- A list of characters `[Char]` is the same as a `String`. Haskell allows us to use `String` as a type synonym for `[Char]`.
- `half n = n/2` is incorrect code because we cannot divide a whole number into two parts. We instead have to do some type conversion.
- Haskell does not perform implicit type conversion like Python or Ruby. In Python or Ruby, 5/2 is 2 which is not the case in Haskell.
- In Haskell, literal numbers are polymorphic ie. their type is determined by where they're being used at compile time. So, in Haskell 5/2 is 2.5 by default.
- If you can't figure out the types in your code then Haskell's type inference will not be able to either.
- In functions with multiple arguments, the last type is always the return type.

```haskell

makeAddress :: Int -> String -> String -> (Int, String, String)
makeAddress number street town = (number, street, town)

-- is the same as

makeAddressLambda = (\number ->
                     (\street ->
                      (\town -> (number, street, town)))

```

- Haskell has type variables to allow for wildcard types. In `simple :: a -> a`, `a` can become the type the of the variable that is passed to `simple`. In this way, `simple :: a -> a` can become either `simple :: Int -> Int` or `simple :: Char -> Char` using only one base definition.

#### Lesson 12: Creating your own types

- Creating types in Haskell is as important as creating classes in OOP.
- Types can be assigned synonyms in Haskell. This lets us do incredibly cool things like converting `areaOfCircle :: Double -> Double` to `areaOfCircle :: Diameter -> Area` where `Diameter` and `Area` are type synonyms.
- New types in Haskell can be created using the `data` keyword. The synax for this is:

```haskell

-- Syntax
data TYPE_CONSTRUCTOR = DATA_CONSTRUCTOR <ARG1> <ARG2> ...

-- Examples:

data Sex = Male | Female
-- here the entire expression is the DATA_CONSTRUCTOR and there are no args

type FirstName = String
type LastName = String
data Name = Name FirstName LastName
-- here Name is the DATA_CONSTRUCTOR and there are two args: FirstName and LastName

```

#### Lesson 13: Type classes

- Type classes are a way to group together types that behave in the same way. They make it easy to define one function to a wide variety of types.
- `:t` or `:type` in GHCi can be used to inspect the type of a function in the wild.
- `:info` can be used in GHCi to get information about types and type classes.
- `Num a` can be understood as "there is a type a of class Num". `Num` is a type class generalising the idea of a number. All things of class `Num` must have for instance a function `(+)` defined on them.
- Type classes allow us to define functions on an abstraction. For example, suppose we define a function `addThenDouble` on the `Num` type class. This would mean that this function will be valid for any function which implements the `Num` type class. In a way, Type classes allow us to define functions to work on an abstraction.
- Syntax for defining a type class:

```haskell

class TypeName a where
  fun1 :: a -> a
  fun2 :: a -> String
  fun3 :: a -> a -> Bool
```

- Four common type classes in Haskell are `Ord`, `Eq`, `Bounded`, and `Show`.
- Types that implement the `Show` type class can be printed out in GHCi. This is why at times we get the error "No instance for (Show Something) arising from a use of 'print'". Without implementing `Show`, Haskell has no idea how to turn data constructors into strings.

#### Lesson 14: Using type classes

- Like in OO, functions in the context of type classes are called "methods" in Haskell.
- `Polymorphism` means that the same function behaving differently depending on the type of data it’s working with.
- Suppose we want to implement `Eq` class. The `Eq` class has two methods `(==)` and `(/=)`. Haskell is smart enough to figure out the implementation for either of these methods if _at least_ one of them is defined. This is done using Haskell's _default implementations_ of methods.
- Each type class has a "minimum complete definition" which can help programmers determine what minimum methods need to be defined while implementing a type class. The minimum complete definitions that can be looked at on [Hackage](https://hackage.haskell.org). A good way to search Hackage is [Hoogle](www.haskell.org/hoogle).
- The `Enum` class in Haskell helps us represent types as constants in turn helping us enumerate types.
- In general, if you don’t have a good reason to implement your own, deriving is not only easier, but also often better.
- Pending: Note about `newtype`

### Unit 3: Programming in types

#### Lesson 16. Creating types with "and" and "or"

- Haskell has the concept of Algebraic data types which are types that are created by combining other types.
- Types can be combined in two ways: using "and" OR "or. For example, a `Name` is a String `and` another String and `Bool` which is a `True` data constructor `or` a `False` data constructor.
- Types combined with `and` are called _product types_ and those combined with `or` are called `sum types`.
- Most programming languages allow programmers to create _product types_. The C programming language even has the concept of a `struct` available to create product types.
- The problem with having _only_ product types available to a programmer is that it take software design in a particular direction – top-down design ie. starting with the most abstract representation of a type you can imagine.
- Haskell offers a second way to combine types: Sum types. Sum types are a powerful tool that allows you to be much more expressive in defining new types. Some examples of Sum types in Haskell:
  - A die is either a 6-sided die or a 20-sided die or...
  - A paper is authored by either a person (`String`) or a group of people (`[String]`).
  - A list is either an empty list (`[]`) or an item consed with another list (`a:[a]`).
  - The most straightforward sum type is `Bool`.
