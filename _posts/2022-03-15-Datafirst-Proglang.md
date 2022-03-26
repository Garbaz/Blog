
# A Data-First Programming Language

Most programming languages are constructed in analogy to the SVO (Subject Verb Object) word order found in the English language.

`Subject.Verb(Object)`

Even mathematical expressions are effectively SVO:

`7 - 3`,  `counter += 1`

Alternatively, an VSO-like order of terms is also sometimes employed:

`foldl (+) 0 [1,2,3]`

In either case, I would call these styles of programming languages "data-last", meaning that we write the data towards the end of the statements, which then is put through functions from right to left:

`result = foo(bar(data))`

In this project, I would like to consider a programming language that is "data-first", inspired by the SOV word order found in the majority of natural languages, and also by the intuition with which we reason as we are writing a program.


## Why?

The core idea is to think in terms of data flowing through the program, going through various transformations, before being spat out the other end. All programs effectively work in that way, which, at least in imperative programming languages, is generally also represented in the macro structure of the language, with a series of statements that are executed from top to bottom.\
However, when it comes down to the syntax of individual lines, the order tends to be reversed, with the input data standing to the right, and the result being to the left:

```c
// Data flows from top to bottom (input ~> x ~> y ~> z ~> return)
foo(x) {
    y = bar(x); // but when applying functions
    z = x + y;  // or operators
    return z;   // the data is to the right.
}
```

So, as we start writing our line of code, before we even consider what data we want to transform, and what function we want to apply, we already have to think about what we will want to do with the result.

For example, say, we're given the data `[1,2,3]`, and want to take the inverse of the numbers, and then sum the result up. To arrive at the final line of code that would achieve this, the thought process therefore would be:

- Given `[1,2,3]`
- Apply `map(inverse, _)`
- Apply `sum(_)`
- Save result

However, if we were to write this out as one line in a data-last programming language, as we type, we have to consider these steps backwards:

`result = `\
`result = sum(`\
`result = sum(map(inverse`\
`result = sum(map(inverse, [1,2,3]))`

Which, at least to me, seems to go against the intuitive way we think about solving the problem.


## An intuitive order of terms

Taking the intuition above, perhaps a better order of terms would be something like this:

`[1,2,3] map(inverse,_) sum(_) ~> result`

Reading from left to right, we have a term defining some data, which we then put through the map function, then through the sum function, and finally assign it to the result.\
At each step, all we have to think about, is what data we have in our hand, and what we want to do with it next.

If we split this up into multiple lines:

```
[1,2,3]
map(inverse,_)
sum(_)
~> result
```

we notice that this is analogous to how it would look in an imperative style with intermediate values:

```c
x = [1,2,3];
y = map(inverse, x);
z = sum(y);
result = z;
```

However, in the data-first style we don't have to make the choice between either writing a nested chain of functions or writing multiple sequential statements with intermediate results. Instead we simply write a sequence of functions, independent of whether we want to keep it in a single line, or split it up across multiple lines.


## Parameters

Of course, as is already evident above with `map`, a question that immediately comes to mind, is how to handle functions that take multiple arguments. In the case of a function like `map`, it does make sense to have the operation that we want to apply (here: `inverse`) be in some way attached to it, since in the "flowing data" intuition we don't really think about the operation we apply until we want to map it over our current data. In a way, `inverse` is less viewed as data, and more as a parameter to the transformation itself.

Perhaps, we could even incorporate this differetiation between data and parameters into the language's syntax:

`[1,2,3] map(inverse) sum ~> result`

with `map` being defined to have one input data stream and one parameter, and `sum` being defined to have one input data stream and no parameters.


## Multiple input streams

However, in some cases we might simply have multiple independent streams of data, with each one coming itself out of a long sequence of transformations, which we want to put into a function. In a data-last style of programming, this is handled rather neatly:

`concat(map(f, list1), reverse(list2))`

Which, besides order, does not discriminate between the two inputs.

However this syntax does not necessarily agree with ones intutition about approaching the problem. In the given example, given the lists `list1` and `list2`, we would first want to think about applying `map(f,_)` to the former and `reverse` to the latter, giving us two intermediate results, and then, finally, to apply `concat`. Again, the data-last style requires us to consider these operations backwards, unless we split them up with explicit intermediate results:

```c
t1 = map(f, list1);
t2 = reverse(list2);
z = concat(t1, t2);
```

\
Putting these thoughts into the syntax of our data-first programming language, we might want to write something like this:

`list1 map(f) | list2 reverse | concat`

The `|` here signifying, that we want to put the current data stream aside and start a new one. When calling `concat` at the beginning of the third new datastream, the ones put aside are collected, and put through the final transformation. In a way, the `|` symbol could be considered optional, since for example in writing `list2` in our statement above, a term which does not take any input, it already is clear that we have to leave the current data stream dangling and start a new one, or raise a syntax error.


## Mathematical expressions

For better or worse, the use of infix operators in mathematical expressions is quite deeply ingrained into the way we think about calculations, even if that sometimes requires some juggling of symbols to achive the desired result. To take an example:

`(1000 + 729) * (4096 + 8)`

Writing this in the above syntax:

`1000 | 729 | + | 4096 | 8 | + | *`

or, taking `|` as optional, perhaps more clearly as:

`1000 729 + | 4096 8 + | *`

This effectively gives us our expression in Reverse Polish notation.

While perfectly readable with some rethinking, I still would consider it perhaps sensible to take a similar route to the one Haskell took, and allow symbolic infix operators, while also keeping open the option to turn an infix operator into a function when needed.


## Function definitions

Taking the above idea of separating a function's arguments into data and parameters, perhaps a syntax for defining a function as follows would make sense:

```
(INPUT_TYPE) \FUNCTION_NAME( PARAMETERS ) -> OUTPUT_TYPE {
    CODE
}
```

For example:

```
(List) \map_in_a_trenchcoat(f:Function) -> List {
    map(f)
}
```

Or, with allowing for type inference as done in Rust or Haskell:

```
\map_in_a_trenchcoat(f) {
    map(f)
}
```

As with the idea of parameters, in writing the function name before the code, we do go against the idea of data-first for the sake of readability, since generally, when looking at a function again at a later point in time, we tend to primarily be interested in it's name and signiature, so it makes sense to have that all in one place.

An alternative syntax that adheres more strictly to the data-first principle might look like this:

```
(List) \(f) -> List {
    map(f)
} ~> map_in_a_trenchcoat
```

Or with inferred types and written into one line:

`\(f) { map(f) } ~> map_in_a_trenchcoat`

However, I would not consider these two styles mutually exclusive. In fact, if we consider the first syntax with the option for a function to be anonymous:

`\map_in_a_trenchcoat(f) { map(f) }` => `\(f) { map(f) }`

Then it would make sense for this to be considered simply a statement of data to be transformed or assigned as usual, automatically giving us the option for the second style of syntax for free.

## Data to parameters/transformation

With the ability to treat a function like data, it follows that we perhaps would like to put it through a few transformations, before finally still taking it as a parameter. A way to allow for this would be to simply have to option to mark a parameter as such:

```
[1,2,3] |
inverse |
map(_) sum
```

Analogously, we might want to instead take a data function and apply it:

```
[1,2,3] |
map |
_(inverse) sum
```

With the rule here being, that the actual data streams come first, then the function/parameter data streams in order of their blanks (`_`). I.e. we could in theory write the above program as such:

```
[1,2,3] |
map |
inverse |
sum |
_(_) _
```

Though that of course would be unlikely to be done in praxis.

It should be noted, that in this situation the `|` symbol can not be treated as optional, which perhaps would speak against this particular style of syntax. Alternatively we might want to require for a function expression to be enclosed as a code block `{...}` for it to be treated as data:

```
[1,2,3] |
{inverse} |
map(_) sum
```

This way, it is clear that we do not want to invoke the function `inverse` or the current data stream, but rather take it as data directly, restoring the previous optionality of our `|` symbol. This also would fit closely with the function syntax we defined above. For consistency, we might want to even require for an anonymous function to be defined explicitly:

```
[1,2,3] |
\(){inverse} |
map(_) sum
```

or, allowing for a degree of brevity:

```
[1,2,3] |
\{inverse} |
map(_) sum
```

No matter which of these last three style we were to permit, or perhaps all three, this way it would also make much sense to take a sequence of functions as one function to be applied, for example:

```
[1,2,3] |
{sqrt inverse} |
map(_) sum
```

In this sense, be it with the `\()` in front or not, the code block `{...}` would be to be understood in general as representing itself a function, a transformation that takes certain data in and puts certain data out, but lifted into being data itself, to be passed along a data stream or to be assigned to a label (i.e. to be defined as a named function).