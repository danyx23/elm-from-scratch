Types
=====

In the last chapter we wrote down a few type annotations but never really talked about what these types are. Let's take a close look at them.

Types are a concept the Elm compiler uses to track what kind of stuff your functions expect and return and what the named values store. Elm uses this to make sure that there are no mismatches between any types in your program. If you have experience in other statically typed languages like Java, C# or C++, you already know what types are (even though Elms type system is more powerful as we will see soon). If you know other "dynamically" typed languages like Python or Javascript, you are probably vaguely familiar with the term. There are strings and there are functions and numbers, but there is a lot of leeway in using them. If you have a string in Javascript and you concat a number to it, the javascript runtime will make sure to convert this on the fly from a numeric representation in memory to a character based representation suitable for concatenating with other character sequences. Elm, as we saw in the last chapter, is much stricter in this regard and we have to explicitly tell it if we want to convert from one type to another.

Types in elm are capitalized (e.g. String, Int), whereas functions and named values start with a lower case letter.

Type inference
--------------

Elm *infers* the type of every value and function, i.e. it figures it out on it's own. Type annotations are a way to tell the Elm compiler what you think the types of certain values and functions will be - Elm doesn't need them, but if you provide them it will often be easier to figure out type errors at the earliest point, not at the final point where the mismatch becomes relevant.

To simplify a little, the type inference works by tracking the values you create through functions. I.e. in this contrived example:
```Elm
name = "Francis"

capitalize nameArgument =
	toUpper nameArgument

capitalizedName =
	capitalize name
```
Elm can work out the types because it will follow the values from the creation of literals through all functions of  the entire program. It knows that `"Francis"` is a literal of type String (because of the double quotes). Therefore, the named value `name` must be of type String. This means that the function `capitalize` must accept one value of type String because of the way it is used in `capitalizedName`. `toUpper` is defined in the standard library and has the type `toUpper : String -> String`, i.e. it takes and returns a String. The requirement we had from the usage (that capitalize must take a String) works out, and we now know that capitalize will also return a String. I.e. capitalizedName will be a value of type String.

As I have said before, I would advise you to always give type annotations, i.e. write the above code like this:
```Elm
name : String
name = "Francis"

capitalize : String -> String
capitalize nameArgument =
	toUpper nameArgument

capitalizedName : String
capitalizedName =
	capitalize name
```

Basic types
-----------

The basic types in Elm are:

* Char (a single unicode character, e.g. 'A')
* String (character sequences, e.g. "Hello")
* Int (Integer numbers, e.g. 42)
* Float (Floating point numbers, e.g. 42.42)
* Bool (Boolean values, e.g. True)

There some more essential types: functions (identifiable by the occurrence of an arrow in the type description), and, since Elm compiles to Javascript, a special Json.Value type that can be used to pass native Javascript objects through Elm without modifying them, or to extract Elm compatible values using a Json Decoder (more on that later).

Here are a few definitions that create some values:
```Elm
someChar : Char
someChar =
	'a'

someString : String
someString =
	"Test"

someFloat : Float
someFloat =
	5.0

someBool : Bool
someBool =
	True

someFunction : a -> a
someFunction arg =
	arg -- this function just returns the argument unmodified

someNum : number
someNum =
	4
```

That last one, `someNum` is a bit odd. First of all, you may think that this should be of type Int. Second, why does `number` not start with a capital letter if we said that all types start with capital letters? The answer is that `number` is a special construct that I will call a typeset. `number` describes a set of types that can be either a Float or an Int. If numeric literals without a decimal point were always of type Int, this would be a bit annoying because all Float literals would then have to use the decimal point, so `number` was introduced for cases like this.

Note that it would be perfectly find if you would write in the type annotation of someNum that you expect this to ben an Int (or a Float), i.e. if you give a *more specific* type, because the Elm compiler just cares that it satisfies the requirement that is has to be some `number`. Unless you have a good reason to use `number` in your type annotations, I would suggest to always write down the specific kind of number type you intend to use.

You can't define such typesets like `number` on your own, only the language itself can define them (there are two more, comparable and appendable. As far as I know, the thing I call a typeset here does not have an official name).

Record types
------------

A common need when writing programs is to keep certain pieces of data together and be able to treat it as one unit. In the previous chapter, we had a function to format a string for a movie based on its title and year of release. Maybe these two pieces of information are all we have on a movie in our program and we'd like to keep them together, similar to an object in Javascript or a class in Python or many other languages. In Elm, these are called records are are defined like this:

```
type alias Movie =
	{ title: String
	, yearOfRelease: Int
	}
```

A couple of things to note here: We start with type alias and the name, with a capital initial, of the new Record. The word alias already hints at something in Elm that is a bit different from other languages - the type name Movie is actually just a shorthand for the whole shebang between curly braces. Whenever you have a function or type declaration and want to reference a Record of this type, you can either write down `Movie` or the whole string of `{title: String, yearOfRelease: Int}`.

Aside: You can also type alias basic types like Int to give them a new name. This can be useful because it gives you a more descriptive type name in some cases, e.g. if our Movie record had an ID of type Int we could do `type alias MovieId = Int` and then use MovieId interchangeably with Int.

The next thing to note is that the fields have a lower case initial and are spelled out in between curly braces, and for each field the type is given after a colon. The type can of course be any type, not just a primitive type as in our example (even function values as fields are possible, tough much less used in this way than in object oriented languages like Java).

The indention style with the comma at the beginning might take a bit of time to get used to, but makes it easier to spot if you have one too many or too little than if you write it down in the end (but you can of course use that style too).

Ok, so how do you construct and use such a value? Here we go:

```Elm
-- We construct the first value by using the automatically
-- generated "value constructor" that is a function with the same as
-- the type and takes a positional argument in the order the fields
-- in the record were defined
doubleIndemnity : Movie
doubleIndemnity =
	Movie "Double Indemnity" 1943

-- The second value is constructed by using the record creation
-- syntax
tokyoMonogatari : Movie
	{ title = "Tokyo Monogatari"
	, yearOfRelease = 1951
	}


-- A function that takes two Movies and returns the
-- number of years between their releases
calculateYearsBetweenReleases : Movie -> Movie -> Int
calculateYearsBetweenReleases movie1 movie2 =
	let
		signedDifference = movie1.yearOfRelease - movie2.yearOfRelease
	in
		abs signedDifference -- difference may be negative, so use abs to get the absolute value


-- This is a contrived example to show how to create new values
-- based on existing ones.
createDummyDocumentary : Movie -> Movie
createDummyDocumentary movie =
	{ movie | title = movie ++ " - a Documentary"
			, yearOfRelease = 2016
			}

```

Creating values can either use an automatically generated "Value Constructor" that has the same name as the type (first example), or the record creation syntax (second example). Field values can be accessed with a dot, similar to many other languages. Since all values in Elm are immutable, the only way to "change" the value of a field is to create a new value based on another one. The last function is a contrived example of how to do this - you use curly braces, give the identifier of the value you want to base your new value on (in this case the parameter of the function, movie), then a vertical bar, and then you list the fields you want to give a new value to (in this case we do it for both of the possible fields, but you can of course only state a single field). All fields you don't set to a different value will have an unchanged value.

Creating new values of a record by updating one or a few fields like in the last example will be something we do a lot when we start writing elm applications that deal with a central model Record that then get's changed in an update function.

There is one final piece of syntax related to records that is mostly useful for advanced use cases, but that you may stumble across if you let the Elm plugin of your editor generate type definitions or you may see it in error messages. It is this syntax:

```Elm
calculateYearsBetweenReleases : { a | yearOfRelease : Int } -> { b | yearOfRelease : Int } -> Int
```

This is the type Elm actually infers for the type `calculateYearsBetweenReleases`. It says that the first and second argument are both a record, called `a` in the first parameter and `b` in the second, that has, potentially among other things, a field called `yearOfRelease` of type Int. If you had another type `Book` that also had a field `yearOfRelease` of type Int, Elm would allow you to pass either two Books, or two Movies, or even a mixture of the two, into this function. You can, if you like, use the *same* type variable for both the first and second parameter (use the type variable `a` for the first and second parameter), but this would change the meaning - it would mandate, that the first and second argument be of the same type - i.e. you could still compare either two Books or two Movies but no longer either a Book and a Movie (unless they are defined exactly identical, i.e. with the exact same field names and types, in which case the types become interchangeable for Elm).

Tuples
------

Another way to group pieces of data together is tuples. These are written in parenthesis, separated by a comma. They can be useful for simple cases where you need to store two or three values together and a record seems a bit like overkill, like e.g. for 2-dimensional coordinates:

```Elm
addCoordinates : (Int, Int) -> (Int, Int) -> (Int, Int)
addCooridnates (x1, y1) (x2, y2) =
	(x1 + x2, y1 + y2)
```

This function takes two arguments, both a tuple of two Ints, and returns a Tuple of two Ints. On the second line you see an important technique in Elm called *destructuring* - by using the same syntax that you would use to create a value on the righthand side of an equals sign but instead using it on the left of it (or, as we will see soon, in a case statement), we can "pull out" or destructure the content. We do this for both tuples - we give the name x1 to the first Int of the first tuple passed into the function, y1 to the second value in the first tuple, and x2 and y2 to the first and second value of the second tuple. We finally construct a new tuple by adding x1 and x2 value together and using that as the first value for the returned tuple, and similarly for the y1 and y2 values.

Be aware that if you are coming from other languages, it's easy to write function calls accidentally that use tuple syntax and that can lead to confusion. E.g. recall our function to generate strings from a movie title and year:

```Elm
formatMovieString : String -> Int -> String
formatMovieString title year =
	let
		yearAsString = toString year
	in
		title ++ " (" ++ yearAsString ++ ")"

-- This one is broken because it would require formatMovieString to
-- accept a single parameter of type Tuple of (String, Int)
brokenFormattedMovieString =
	formatMovieString ("Double Indemnity", 1943)

correctFormattedMovieString =
	formatMovieString "Double Indemnity" 1943

alsoCorrectFormattedMovieString =
	formatMovieString ("Double Indemnity") (1943)
```

You can create 2-Tuples (i.e. tuples with two values), but you can also create tuples with more values, e.g. this would be a 3-Tuple of a String and two numbers: `("Hello", 1, 2)`. Often, when you want to create a tuple, it would be easier to read if you instead created a Record, but this of course depends on the particular use.

Since 2-Tuples are the most common, the base library contains two helper functions `fst`and `snd` that extract the first/second value of a 2-Tuple (not of other Tuples that have more members tough!). As a final example of how to use destructuring, here we define our own `first` function that does the same as `fst`:

```Elm
first : (a, b) -> a
first (firstArg, secondArg) =
	firstArg
```

Hm, what's this - lower-case identifiers in the type annotation? Aren't types, except for those weird ones like *number*, supposed to have an upper case initial? Yes - *concrete types* have to start with an upper case character. But here, in this function, we don't really care what the concrete type of either the first or the second part of the tuple is - we just care that the only argument to the function is a tuple. So we use a *type variable* to state that any type can be inserted here. What the Elm compiler will enforce tough, is that in this case, because we use `a` for the first value of the tuple, and `a` as the return value, these two types match. It also follows, that because both `a` and `b` could be any type, you can't "do anything" with either the firstArg or the secondArg value. You can't concat them together for example because concat is only defined for `List`s, not for all types. You can't add them together because addition is only defined for numeric types, and so on. You can only do things where the actual content doesn't matter. We will see another, similar case when we look at Lists.

Another thing about naming: you can of course use any identifier you like for type variables, e.g. you could give the following type annotation for the example above: `first: (firstType, secondType) -> firstType` - this is totally up to you. For very generic cases like here, where there firstType is arguably not much more useful than a, most Elm code will use type variable names like a, b, c etc. But feel free to use more descriptive ones if you like it better!

Union types
-----------

So far, the types we defined were very similar to what you may know from other languages like Python or Java. Records are similar to Classes, Tuples exist in Python and of course you have basic types. Now we get to a feature that is really powerful and sadly missing in many languages: Union Types (also called Sum Types in other languages). From languages like Java you may know enums. With them you can define a set of "Labels" that are the possible values for a type. They are often used when you want to define a type that has a limited number of possible values, e.g. the state of an operation. In Elm, this is a Union type. Let's look at the simple base case before we get to the true power:

```Elm
type OperationStatus =
	NotStarted
	| Running
	| Failed
	| Completed
```

Note that this time we didn't write `type alias OperationStatus`, but just `type OperationStatus` - this is because here we create a new type, and we can't exchange it with the definition in e.g. the type annotation of a function, but always have to state the type as OperationStatus from now on.

When you want to write a function that e.g. creates a human readable status message from a value of type OperationStatus, you use the `case ... of` construct to *pattern match* on the possible values:

```Elm
getStatusMessage : OperationStatus -> String
getStatusMessage status =
	case status of
		NotStarted ->
			"Not yet started"
		Failed ->
			"Failed"
		Running ->
			"In progress"
		Completed ->
			"Finished!"

```

Our function getStatusMessage takes a value of type OperationStatus and evaluates to the result of the case statement. All branches have to evaluate to the same type (in this case String), and we have to state all possible values - if we don't, we get a compiler error. You can use the underscore character ( _ ) to declare a catch-all case as the last branch, but I advise you to use this as seldom as possible. If you add another case later on to your type, the Elm compiler can warn you about all places where you explicitly stated all values, but can't do this if you use the catch-all case.

Ok, so far this is not so special. In other languages like Javascript, you could use e.g. a number value, a few constants and a bunch of if ... else if ... else  to do something similar. Yes, it's nice that the compiler can check that you took care of all cases, but what is the big deal?

The cool thing about Union types is that you can attach additional data to some of the possible cases, or, as they are actually called, to the possible value constructors. Each value constructor lets you construct a value and the vertical bar that separates them is supposed to signal that a value can be constructed using either NotStarted, or Running, or Failed or Completed - and nothing else.

Let's say that you'd like to track an error message of type String if you are in the Failed state. In a language like Java, you would now define a Class that has two fields, an operationStatus field and an errorMessage. This second field would only be "useful" or "valid" if the operationStatus equals Failed.

Here is what we can do in Elm:

```Elm
type OperationStatus =
	NotStarted
	| Running
	| Failed String
	| Completed

getStatusMessage : OperationStatus -> String
getStatusMessage status =
	case status of
		NotStarted ->
			"Not yet started"
		Failed errorMessage -> -- note the new errorMessage identifier
			"Failed! Error message: " ++ errorMessage
		Running ->
			"In progress"
		Completed ->
			"Finished!"
```

Everything is the same except the Failed value constructor that now got a String payload in the definition, and in the case statement in getStatusMessage we use destructuring to "unwrap" the payload and give it the identifier "errorMessage". We then use it to construct a better message for the Failed case.

If you are relatively new to programming this may not seem like a big deal - but in my experience with writing code in C++ and C#, cases like this come up all the time, and they lead to a lot of headache because the type system in those languages don't have this power. Instead, you have to always keep a field errorMessage around but remember when it should be set and when it is valid to use it.

To get more of a feel for this, let's add some payload to another value constructor, this time two Ints to track the progress where the first Int is the current progress and the second is the final progress value (e.g. number of items completed and total number of items):

```Elm
type OperationStatus =
	NotStarted
	| Running Int Int
	| Failed String
	| Completed

getStatusMessage : OperationStatus -> String
getStatusMessage status =
	case status of
		NotStarted ->
			"Not yet started"
		Failed errorMessage ->
			"Failed! Error message: " ++ errorMessage
		Running current total ->
			let
				progress = (toFloat current) / (toFloat total) * 100.0
			in
				"In progress (" ++ toString progress ++")"
		Completed ->
			"Finished!"
```

Instead of using two Ints, it may of course be nicer to quickly define a record type and use that as a payload for the Running type constructor, or use a tuple or alias Int to Current and Total to communicate the intent a bit clearer. Experiment a bit to see what that would look like!

Null / Undefined
----------------

One of the great things about Elm is that it didn't do the "billion dollar mistake", of letting all or most types be nullable by default. Many other languages do this in a way that either all variables (Javascript), or just those holding complex types (e.g. C#) or some other important subset (e.g. pointers in C++) can be null - i.e. invalid. This information is implicit in the language however, not explicitly modelled in the type system (if there even is a static type checker).

In Elm, ever field, every named value, must have a valid value at every point in time - it can't be null or undefined, or any such magic value.

Wait a minute, you might say, what do you do when you legitimately don't have a value yet? For example because it will only be known once a request to a server has been successfully processed? Enter: Maybe

Polymorphic types: Maybe
------------------------

Maybe is a simple, polymorphic union type. Polymorphic literally means that it can "take many forms", or, to put it more simply, that it uses a type variable like the function first that we defined previously when we were looking at tuples (you may know the concept of polymorphic types as Generics in other languages). Let's look at the definition of Maybe and a few uses:

```Elm
type Maybe a =
	Nothing
	| Just a

maybeString1 : Maybe String
maybeString1 =
	Nothing

maybeString2 : Maybe String
maybeString2 =
	Just "Hello"

maybeInt1 : Maybe Int
maybeInt1 =
	Just 42

maybeMovie : Maybe Movie
maybeMovie =
	Just (Movie "One, Two, Three" 1946)
```

If we look at the type definition, you would read it like this "We define a type Maybe with a type variable `a`. The value constructors are `Nothing`, without any data attached, and `Just` with a single payload value of type `a`". When we actually want to construct a value of a Maybe, we have to decide what a should be in this case. In the first example of maybeString1, we define a "Maybe String". This means that a value of this type can be constructed either with "Nothing", or with "Just" and a string. Just as in all other cases, Elm does not need type annotations to know what the type variable has to be - if you use `Just "hello"`, Elm infers that this must be of type `Maybe String`.

When we want to use the data that is "inside" a Maybe, we have to use case ... of to tell Elm how to handle all (two) possible value constructors:

```Elm
maybeMovieToString : Maybe Movie -> String
maybeMovieToString maybeMovie =
	case maybeMovie of
		Nothing ->
			""
		Just movie ->
			formatMovieString movie

maybeMovieToMaybeString : Maybe Movie -> Maybe String
maybeMovieToString maybeMovie =
	case maybeMovie of
		Nothing ->
			Nothing
		Just movie ->
			Just (formatMovieString movie)
```

These are two different ways you can handle such cases. The first function returns a String, and chooses to return an empty string in the Nothing case. The second function returns a Maybe String, and so the Nothing value constructor gets matched to a Nothing again and in the just case we call formatMovieString with the unwrapped String value and then wrap the result with the Just value constructor (otherwise we would return a String, not a Maybe String and Elm would complain)

More than one type variables - Result
-------------------------------------

Maybe is a very useful type, but there are cases where you would like to have more information if something goes wrong. A function that returns a Maybe of some type can just express that the operation can fail (when the return value is `Nothing`), but it can't give any more information. This is where a very similar type is useful - `Result`. Just like `Maybe`, the definition is really simple, so let's just define it here:

```Elm
type Result error value
	= OK value
	| Err error
```

This is a type called Result that is defined with two type variables. There are two value constructors, Ok that comes with a payload of the type given with the type variable `value`, and Err, which comes with a payload of the type given by the type variable `error`. If you have a function that returns a `Float` if the operation worked and a `String` error message otherwise, you could use a return type of `Result String Float`. Whenever you get a value like this and want to use the `Float` number, you have to use pattern matching to handle both the `OK` case where you get your number, and the `Err` case where you get an error message:

```Elm
someOperation : Float -> Float -> Result String Float

case someOperation 10.0 -1 of
	Ok resultValue ->
		-- do something with it
	Err errorMessage ->
		-- do something with the error message
```
