---
layout: page
title: '01 - Letting Elixir Soak In'
summary: 'Getting your eyes used to Elixir syntax'
category: manual
---

## A Very Simple Syntax

Elixir was created by a Rubyist: José Valim, so you'll see a lot of similarities. Elixir is an *entirely different language* however. You know this already but **Elixir is a functional language**. We'll talk about that later.

Here are some common things you'll see when reading/writing Elixir:

```elixir
# this is a comment
title = "a string"
price = 12.00
quantity = 1
```

No semicolons. No braces. Elixir is a *clean* language which, like Ruby, drives expressiveness and clarity. Many people don't like this aspect of some languages, preferring some type of structural guidance that you might find with C-based languages. If this is you, give it a little more time before you give up - there are a number of things that make this an exciting thing.

By the way, you can play along by opening up a terminal and typing in "iex":

```
Interactive Elixir (1.1.1) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> title = "a string"
"a string"
iex(2)> price = 12.00
12.0
iex(3)> quantity = 1
1
iex(4)>
```

Those are the basics, and they're easy to figure out. Let's get to the more common stuff you'll see now.

## Common Syntax: Maps and Lists

These can look very strange until you understand what's going on. This is what they look like:

```elixir
#This is a Map
map = %{key: "value", another_key: "another value"}
map.key == "value"

#This is a list
list = ["one", 2, 3.000]

#This is a Keyword list
keyword = [one: "one", two: 2, three: "three"]
```

It's common to pass keyword lists as arguments to functions; so common as a matter of fact that there is syntactic sugar just for that purpose.

**Idiom:** Passing keyword lists as the final argument to a function

```elixir
#a typical structure for an Elixir function.
#we'll get into the details later - for now look at opts
def a_function(arg, opts) do
  #do something
end

#call your function
a_function("something", limit: 1, sort: "desc", columns: "id, name")

#parens are optional... sort of. More on that below
a_function "something", limit: 1, sort: "desc", columns: "id, name"
```

You can omit the square braces entirely and pass values here as you need. You can access these options easily (and we'll get to that) so for now know what you're looking at.

You might be wondering by now how we can be sure of the types sent in - and what kind of goofy errors will arise when you try to send in garbage. I'll touch on that quickly, and we'll get into it much more later on.

## Guards

If you expect arguments to be structured a particular way, you can guard against trash being passed to you. Let's refine our function with some guards:

```elixir
def a_function(arg, opts) when is_map(arg) and is_list(opts) do
  #do something
end
```

The `when` you see above is a Guard Clause and you'll see them all over the place. This locks down your function and "guards" against bad data coming in. This is nice, but what's even nicer is how concise and obvious it makes your code - and also gives you the ability to pass in truly variable data.

If someone tries to call you function without the appropriate types, they'll get an error.

**Idiom:** Elixir functions typically have *at most* three arguments. If you find yourself using more, reconsider. More on this below.

## Atoms

If you've ever used Ruby then you know what a label is. That's an Atom in Elixir. Atoms are useful and also confusing - the language is still sorting through how they should be used when and where.

Here's an Atom:

```elixir
my_atom = :atom
```

They're like strings, but their name is also their value. They're very light weight and are used often when describing things - especially data passed to a function using a Tuple. What's a tuple? Glad you asked.

## Tuples

Tuples are simply data structures identified by some braces. They can contain anything and are brilliant for passing as a function argument:

```elixir
{:example, "I'm a tuple"}
```

Here I've used an Atom in the first position to describe the data in the tuple.

**Idiom:** Elixirists like using Atoms in the first position of a tuple to describe what's in the tuple.

This makes writing functions even more exciting because you can call functions based on tuple structures:

```elixir
def handle_sweets({:chocolate, yummies}) do
  #yummies are bound and ready to use
end

def handle_sweets({:fruit, fruits}) do
  #fruits are bound and ready to use
end
```

What you're seeing here is, perhaps, one of the "sweetest" things about working with Elixir: *Pattern Matching* and descriptive data. When calling `handle_sweets`, Elixir will evaluate the best match. Obviously the tuple you send in will determine which function is called.

That means that **the type of your data is not as important as the data itself**. Welcome to Elixir.

I'll get to Pattern Matching later on. There's one more thing we need to get to.

## Pipes

You'll see these everywhere - a simple symbol with a pipe and a right arrow: `|>`. This "pipes" data from one function to the next - that's it. It's a cornerstone of any functional language.

Let's see how this would work with our sweets above:

```elixir
def handle_sweets({:chocolate, yummies}) do
  #yummies are bound and ready to use
  yummies
    |> unwrap
    |> eat
end

def handle_sweets({:fruit, fruits}) do
  #fruits are bound and ready to use
  fruits
    |> peel
    |> eat
end

def peel(fruits) do
  #peel algorithm
end

def unwrap(candy) do
  #unwrap things
end

def eat(sweets) do
  #pop in mouth
end
```

**Idiom:** Elixirists favor, heavily, lots of little functions rather then a large function with many `ifs` or `switch` statements.

Here we have a function `pipeline` in each of our `handle_sweets` functions that handles the data as required. It just depends on how it gets called:

```elixir
{:candy, ["Reeses", "Twix"]}
  |> sweet_thing

{:fruits, ["Apple", "Banana"]}
  |> sweet_thing
```

**Idiom:** Piping data to functions is a preferable visual treatment when calling functions in Elixir. It highlights the idea that the data is about to be transformed in some way.

## Maps and Structs

I mentioned maps already, but it's worth mentioning one more time as you *will see them everywhere* as well as their big brother, *Structs*. Here's a struct:

```elixir
defmodule Customer do
  defstruct [
    name: nil,
    email: nil,
    status: :new,
    tags: ["untagged"],
    id: UUID.uuidv4() #this is an external module
  ]
end

#use the struct
customer = %Customer{name: "Jill Customer"}
IO.inspect customer
```

Structs are formalized maps and are very useful for complex data interactions. Elixir developers from typed language backgrounds that use classes etc will find this a comfortable construct. **Be careful** with this notion - *structs are NOT CLASSES*.

Elixir differs from Erlang in this way: *you can change the value of a struct*:

```elixir
customer = %Customer{name: "Jill Customer"}
customer = %{customer | name: "Joe Customer"}
```

For some functional developers, this might seem absolutely crazy. Consider, however, what Joe Armstrong thinks about this (he's the creator of Erlang):

> Elixir breaks a few Erlang holy cows - variables can be re-bound in sequences. This is actually ok, the resulting forms can still be normalized into a static-single-assignment (SSA) form. While this is OK in sequences it would totally verboten-nicht-do-not-do-it in loop constructs. But this is fine, Elixir has no loops, only recursion. Actually it could not have loops with mutable variables since this would be impossible to compile into anything remotely sensible in the EVM

*Elixir doesn't have loops*. You have to use recursion ... fun stuff!

Anyway - most of the time you can just let the data be the data without resorting to using a struct, which is often just extra weight and complexity. It can be useful for defaults, etc, but consider using a function to create a map or some data you want to use other places in your app.

<hr>

{% include message_popup.html link="/email/i-need-you-to-setup-a-project" message="I need you to setup a project real fast" %}
