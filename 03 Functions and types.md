Functions
=========

Let's take a look at the code we wrote in the last chapter:

```Elm
import Html exposing (text)

main =
	text "Hello from the MovieSearch app!"
```

The first line imports the module Html, and brings the function `text` into scope. This means that of the many functions in the Html module, only text will be useable in our file without using the full name in the form Module.function. Another way to write the above code, without bringing text into scope would be this:

```Elm
import Html

main =
	Html.text "Hello from the MovieSearch app!"
```

Both pieces of code do exactly the same: they define a function `main` that takes no arguments and evaluates to a value that represents the text "Hello from the MovieSearch app!".

If you know other programming languages, you may note that there is no "return" statement. We will see this in more detail soon, but functions in Elm basically always consist of a single statement that defines the output of this function. There is of course a way to have intermediate steps, but as we will see there is still only ever a single statement that defines what the return value of a function is.

Every Elm program needs a function called `main` so Elm knows where to start your program.

`text` is a function that takes one argument of type `String` as it's input and returns a value of type `Html a`. We will shortly see how to define our own functions. We'll take a more detailed look at what types are, what kind of types Elm understands and how to define your own in the next chapter. This chapter about functions goes into quite a bit of detail, but I think if you understand the basics of functions and types really well, you will be spared a lot of potential confusions later on.

Type annotations
----------------

If you use the Atom editor and save this file or if you run elm make with -warn (which is a good idea), then you will see that Elm complains about the main function not having a type annotation. The error message is "Top-level value 'main' does not have a type annotation". Let's fix this and see how to add type annotations:

```Elm
import Html

main : Html.Html a
main =
	Html.text "Hello from the MovieSearch app!"
```

Type annotations are a way to tell the compiler what you think the type of a value is. They are always in the form you see here: you state the name of the function you define, then a colon, and then give the type. You don't *have* to write them down as the Elm compiler uses a cool technique called Hindley-Milner type inference to figure out the types of most constructs on its own, but especially if you are learning Elm it is a very good idea to always add type annotations (and that is also what I will keep doing in this series) so Elm can tell you at the earliest point if you get something wrong and your types do not match in different parts of your program.

Comments
--------

There are two and a half kinds of comments in Elm.
* Line comments, that start with -- and include everything after that until the line break.
* Block comments, that include everything within {- and -}, regardless of line breaks
* Documentation block comments, that work like block comments but are stated as {-| and -}. The single documentation block comment before every top level definition will be picked up by elm package as part of the documentation that will be generated for public elm packages.

```Elm
-- a line comment

main = -- another line comment
	text {- a weird inline comment. You will rarely see this used like this -} "Hello from the MovieSearch app!"

{- a block comment
	that spans
	multiple lines.
-}

{-| documentation for the function hello
  * this is
  * a bullet list

  You can use `markdown` here
-}
hello : String -> String
hello name =
    -- hello implementation
```

Importing modules
-----------------

Let's try something else and see what happens if you take the `text` function away and try to return our message directly:

```Elm
import Html exposing (Html)

main : Html a
main =
	"Hello from the MovieSearch app!"
```

This gives us the following error message:

```
The type annotation for `main` does not match its definition.

3| main : Html a
          ^^^^^^^^^^^
The type annotation is saying:

    VirtualDom.Node a

But I am inferring that the definition has this type:

    String
```

True - we didn't change the type annotation and so it no longer matches (if you don't really understand what types are, don't worry, we'll get to that soon!). Let's just change our type annotation to have the type Elm inferred:

```Elm
import Html

main : String
main =
	"Hello from the MovieSearch app!"
```

And we get a new error:

```
The `main` value has an unsupported type.

4| main =
   ^^^^
I need Html, Svg, or a Program so I have something to render on screen, but you
gave me:

    String
```

Huh. So main can only have these three types so that Elm has something to display. Either a static Html value, or a static Svg value (vector graphics) or a Program that allows us to display interactive html and svg (the thing you will usually use).

So this is why we use the `text` function - it turns our salutation of type String into a `Html a` thingy that Elm actually knows how to display. Again, if this is not entirely clear, stay with me until the next chapter when types will hopefully make more sense, but it is a good thing if you have a rough understanding what each of the words we wrote down do before we go on.

Defining our own functions
--------------------------

Let's write another function so we can see how the syntax works for defining and calling functions. I'll start by just extracting the greeting message string into a function:

```Elm
import Html

salutation : String
salutation =
	"Hello from the MovieSearch app!"

main : Html.Html a
main =
	text salutation
```

If you are coming from a traditional imperative language, you may think of what happens here as: "In main we call the function text to return a `Html a` value of a string, and we construct that string by calling the function salutation that takes no arguments and returns a string".

Elm comes from a family of languages descending from ML that goes back to the 70ies and represented a pretty significant shift in how to write programs. Instead of trying to express the execution the computer will perform directly, it models computation more along the lines of how mathematical equations are written down - as a series of equivalences.

This may seem like nit-picking, but here is how someone who knows functional languages well would describe this program: "We defined two top level values, main and salutation. Main is a value of type Html a. It evaluates to the result of applying the function text to the argument salutation. Salutation is a value of type string."

The difference in terminology ("apply a function to it's arguments" vs "call a function") is not that important for now, but notice that we didn't describe salutation as a function in the second paragraph. What we defined is just a named value of type string - not a function! This is an important concept (but don't worry if it will only fully "click" later): The definitions you give in Elm are always either named values (if they have no arguments, like here), or functions (if they do take arguments), or type definitions (we'll explore those in the next chapter). Let's finally get around to creating a function then!

```Elm
import Html

salutation : String -> String
salutation name =
	"Hello, " ++ name ++ ". Welcome to the MovieSearch app!"

main : Html.Html a
main =
	text (salutation "John")
```

Ok, the first thing you will notice is that the type annotation for salutation changed. It is now "String -> String". The arrow separates the individual arguments from each other and the return type (the return type is always the last thing in the list). You would read this as "salutation is a function that takes a string and returns a string".

The definition in the next line changed too - it is now "salutation name = ". This is how you define arguments. Salutation is now a function that takes one argument and it gives it the identifier "name".

Then we changed the actual salutation to make use of this argument - we concatenate three strings together with the ++ operator so that we get a greeting with the name that was passed to the salutation function in the middle.

Finally, we changed the definition of main a little by passing an argument to the function salutation. If you know other programming languages, the way we use parenthesis here may confuse you a little - we didn't have parenthesis before when it was just `text salutation`, and now we need them?

Function application with multiple arguments
--------------------------------------------

The thing is, function application (function calls in imperative lingo) of a function `fn` with three arguments `a`, `b` and `c` looks like this in elm:
```Elm
fn a b c

-- just to show what the equivalent would be in python:
-- fn(a, b, c)
```

If `a` should now be another function application of a function `fn2` with argument `x`, we can't write this:
```Elm
fn fn2 x b c

-- python equivalent:
-- fn(fn2, x, b, c)
```
because Elm would think we want to call function fn with 4 arguments: the first one being the function `fn`, then `x`, `b` and `c`. Functions are "first class citizens" in Elm, i.e. they are just another kind of value and can be passed in parameters if you like (we will later see how powerful this can be). But in this case, this is not what we intended.

Instead we have to use parenthesis to tell Elm "evaluate the content inside these parenthesis first, then use it as a single argument"
```Elm
fn (fn2 x) b c

-- python equivalent:
-- fn(fn2(x), b, c)
```

So just to wrap this up, if you get rid of the parenthesis, you will get a compile error because Elm will think you want to call the function `text` with two parameters, a function and a string, but this doesn't fit the definition of `text`which just takes a single argument.

Our example program will be concerned with searching for actors and movies, so let's define another function that takes a movie title and the year of it's release and turns both into a string in the form "The Movie Title (1999)". The movie title will be of type String, the year of type Int (for Integer numbers). Let's give it a try:

```Elm
formatMovieString : String -> Int -> String
formatMovieString title year =
	title ++ " (" ++ year ++ ")"
```

This type annotation tells Elm that we expect the function `formatMovieString` to take two arguments, the first of type String, the second of type Int, and it will return a value of type String. But when we compile this, we get an error:

```
The type annotation for `formatMovieString` does not match its definition.

3| formatMovieString : String -> Int -> String
                       ^^^^^^^^^^^^^^^^^^^^^^^
The type annotation is saying:

    String -> Int -> String

But I am inferring that the definition has this type:

    String -> String -> String
```

Ok, what does that mean? Why does Elm think that the second parameter, `year`, should be a of type String? The answer lies in the ++ operator.

Operators
---------

```Elm
-- somewhere in the Elm base library code:
(++) : List a -> List a -> List a
(++) list1 list2 =
	-- implementation of concat
```
It is the operator Elm uses to concatenate two lists, regardless of what the content of those lists is. Here you see that operators are just functions with exactly two parameters in elm, where the name of the operator consists of non-letter characters. When *using* an operator (like + for addition or ++ for concat), you do not use parenthesis around the operator. When *defining* it, or importing it explicitly, or passing it to a function, you wrap the operator in parenthesis, like in the fake definition above.

Back to the problem at hand - the type definition for both the first and the second argument is `List a`. We will go into more detail about this in the next chapter, but the type `List` is defined with a "type variable", i.e. it is a `List` of some values of a type that will be defined later, for now called `a`. Because the same variable `a` is used in both the first and the second argument, both arguments have to be Lists of the same type. The type `String` is really just a shorthand in Elm for `List Char`, i.e. a List of Characters.

That is why, when we use the ++ operator to concatenate a string with something else, Elm demands that something else to also be a `List Char` (= `String`). So we could either change how we store the year and pass it in as a `String` so that we can concat strings easily, or we find a way to turn an `Int` into a `String`. The first way may look easier, but it might be quite annoying later on when we want to e.g. calculate how old a film is.

Luckily, there exists a way to convert `Int`s to `String`s in the base library: the function `toString`. Let's use it:

```Elm
formatMovieString : String -> Int -> String
formatMovieString title year =
	title ++ " (" ++ (toString year) ++ ")"
```

The function toString turns an `Int` into a `String` (the signature is: `Int -> String`) and is exactly what we need here. Again, we use parenthesis to make it clear that we want to concat the result of `toString year`.

This whole thing is now a little long and doesn't read very nicely. Could we store "toString year" somewhere and then reference to it by name? We can't extract it to a top level value because the year is an argument to our function. We could extract it into a "yearToString year =" function, but then we would still have to use parenthesis. Let's try something else:

```Elm
formatMovieString : String -> Int -> String
formatMovieString title year =
	yearAsString = toString year
	title ++ " (" ++ yearAsString ++ ")"
```

But we get a compiler error:

```
I ran into something unexpected when parsing your code!

5|     yearAsString = toString year
                    ^
I am looking for one of the following things:

    end of input
    whitespace
```

This is one of the few cases where the usually fantastic error message of the Elm compiler are a bit harder to decipher. The key is that a function in elm has to evaluate to exactly one expression, and we gave it two - the definition for yearAsString and an expression that builds the string our formatMovieString function should evaluate to.

To be able to do use a local named value, we need to use a special construct called let ... in. It allows us to give, a number of new, local definitions, each on a new line, between let and in that can then be used in the final expression. These local definitions can also be functions if you like. All these expressions are only visible inside the function, i.e. you would not be able to use `yearAsString` anywhere in the program outside the `formatMovieString` function. Here is what the definition with let ... in looks like:

```Elm
formatMovieString : String -> Int -> String
formatMovieString title year =
	let
		yearAsString = toString year
	in
		title ++ " (" ++ yearAsString ++ ")"
```

The line after in is what the function formatMovieString will actually evaluate to, and the stuff above, between let and in, are temporary definitions to increase the readability.
