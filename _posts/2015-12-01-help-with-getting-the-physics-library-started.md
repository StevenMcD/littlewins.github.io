---
layout: email
title: 'The Physics Lib'
category: email
---

The Rocketry Team is hoping we can get started on the Physics library - it's not something we're supposed to be doing all the time (helping them out with scientific calcs) but they're completely buried for the demo coming up next week.

**Yeah, I know.**

Anyway: I think this would be a fun thing for you to play around with. I'll walk you through it and along the way you can put to practice what you've been reading in my manual. **You are reading it right?** You better be, my ass is on the line here.... no pressure :p.

You might be wondering why I don't just do this Physics library myself if I have the time to write the email? Are you always this much trouble? Jeez! Because you get to test it and testing is fun isn't it. OK pipe down let's go.

## Organizing the Project

We need to create some functions that we can use to calculate escape velocity (EV) from any planet. In our case we need the Earth and Mars as our rockets will be leaving both, ideally.

Let's start by creating a project to work in:

```
mix new physics
```

You should see a familiar splash of text. Now, let's get to work. We'll put our code in the `lib` directory, leaving `physics.ex` at the root level. I'll talk about what's in there later on.

We need to create a module that will contain the functions we'll need to calculate EV. Let's call ours `rocketry`:

```
mkdir lib/physics
touch lib/physics/rocketry.ex
```

Every app in Elixir is made up of smaller apps and modules. Organizing these modules can be tricky, but an agreed-upon rule is that you have an "entry module" with the same name as your app. This typically goes in the `lib` directory. The modules that you write for your app go in a directory with the same name.

Now let's define our module:

```elixir
defmodule Physics.Rocketry do
  #code here
end
```

Here I've defined a `namespace` for the module that follows the structure of our directories. .NET and Java people will find this a familiar thing. Using namespaces helps keep modules organized.

## Calculating Escape Velocity

The equation for escape velocity from a planet is pretty straightforward:

{% include math.html equation="V<sub>e = √(2GM/R)" %}

That's the square root of two times the gravitational constant (G) of whatever body, the mass M of that body divided by R, which is the radius of that planet.

To do this properly, we should represent a planet with a Struct:

```elixir
defmodule Physics.Planet do
  defstruct [
    name: "Earth",
    radius: 6371000,
    mass: 5970000000000000000000000
  ]
end
```

The namespace `Physics.Planet` is a bit odd - but we can have a think on refactoring that out later, as needed. Right now I need you to focus.

The radius is in meters (`m`) and the mass is in kilograms (`kg`) - these units will work for what we need (`km/s`). The next number is a bit hard to write:

{% include math.html equation="G = 6.67x10-11" %}

I was going to write this out but my hand started to cramp. The neat thing is that we can use scientific notation here - and I'll also identify the units on the struct itself:

```elixir
defmodule Physics.Planet do
  defstruct [
    name: "Earth",
    radius_m: 6.37e6,
    mass_kg: 5.97e24
  ]
end
```

G now becomes `6.67e-11`. For fun, pop this into iex:

```elixir
6.67e-11 * 10000
```

## Designing Our Functions

Now we need to do some straightforward math. Given the above struct, it should seem pretty straightforward. In fact we could even stick on the Planet module itself for tidiness:

```elixir
defmodule Physics.Planet do
  defstruct [
    name: "Earth",
    radius_m: 6.37e6,
    mass_kg: 5.97e24
  ]

  def escape_velocity(planet) do
    g = 6.67e-11
    gmr = 2 * g * planet.mass_kg/planet.radius_m
    vms = :math.sqrt gmr
    vkms = vms/1000
    Float.ceil vkms, 1
  end

end

v = %Physics.Planet{}
 |> Physics.Planet.escape_velocty

v == 11.2 #true
```

*What's up with the `:math` notation here? **You can use any Erlang function from within Elixir** using the atom notation above. We're using Math to calc the square root.*

That works nicely. And this is the kind of code you'll probably write when you first start writing Elixir, coming from an object-oriented background. This **is not** idiomatic Elixir at all. Let's see why.

## The Problems With Our Solution

Overall: *we're thinking in terms of an object*. A `Planet` to be specific; attributes, methods, messages. Now this is *working code* and working code is decent code. It can be reused, and tested well - so who cares?

Also: *why did I show you the wrong way first*? Because that's probably what you were thinking - and that's not a bad thing. Modeling in this way has been around for a while and it takes a bit to unlearn.

Also, learning by failure is far more fun! For me at least - watching you fail is pretty entertaining. Seriously, though, I like to help by both pushing and pulling when it comes to cliffs. Working with me is awesome.

We need to think in terms of **data and transformations**. We can rewrite this code and make some lovely improvements.

Let's describe our planet using a simple map and then make the functions a bit more usable. For this, let's use our `Physics.Rocketry` module that we created and never properly used:

```elixir
defmodule Physics.Rocketry do

  def escape_velocity(planet) when is_map(planet) do
    planet
      |> calculate_escape
      |> convert_to_km
      |> rounded_to_nearest_tenth
  end

  defp rounded_to_nearest_tenth(val) do
    Float.ceil(val, 1)
  end

  defp convert_to_km(velocity) do
    velocity / 1000
  end

  defp calculate_escape(%{mass: mass, radius: radius}) do
    newtons_constant = 6.67e-11
    2 * newtons_constant * mass / radius
      |> :math.sqrt
  end

end
```

This reads much differently doesn't it? In fact it's begging to be refactored - the rounding and conversion bits belong in their own module. We'll refactor in just one second.

You'll notice that I've defined functions with both `def` and `defp`; the latter just means "this is private". The `calculate_escape` function is essentially the same but demands that we send in the `mass` and `radius` in a map.

I've added a guard clause to the `escape_velocity` function, but it's bit too loose don't you think? Let's refactor this once more:

```elixir
defmodule Calcs do
  def rounded_to_nearest_tenth(val) do
    Float.ceil(val, 1)
  end

  def convert_to_km(velocity) do
    velocity / 1000
  end
end

defmodule Physics.Rocketry do

  def escape_velocity(:earth) do
    %{mass: 5.972e24, radius: 6.37e6}
      |> escape_velocity
  end

  def escape_velocity(planet) when is_map(planet) do
    planet
      |> calculate_escape
      |> Calcs.convert_to_km
      |> Calcs.rounded_to_nearest_tenth
  end

  defp calculate_escape(%{mass: mass, radius: radius}) do
    newtons_constant = 6.67e-11
    2 * newtons_constant * mass / radius
      |> :math.sqrt
  end

end
```

With this code I've broken out a `Calcs` module - it just makes more sense doesn't it? I also added another `escape_velocity` that just takes an atom: `:earth`. This makes perfect sense in our case because Earth's mass and radius are relatively constant - so all we need to do is get those values and plug them in.

Finally, notice how I'm "piping" data? I'm transforming the raw planetary data (mass, radius) through a set of functions, ultimately getting what I want back.

**Idiom**: Declaring data and piping it into a function (or set of functions) is preferred in Elixir

Also, notice how I accept the `planet` map in the `escape_velocity` main function, but then break out the components of the map into parameters for `calculate_escape`. This is **Pattern Matching** and it's a core feature of Elixir.

## That's a Win! Now Pick It Up and Run...

OK I helped you through this one. I'm *sure* there are things that are right over your head right now but do me a favor and wrap this with some tests will you? Have a look in the tests directory of our new Physics project. You'll see a sample one stubbed out, just follow the lead there.

You can probably get by with just `assert` for right now, but if you want to know more [you can have a look at the docs](http://elixir-lang.org/docs/v1.0/ex_unit/ExUnit.Assertions.html).

To run your tests, just use `mix test`. If you want to see more detail run `mix test --trace`.

Have fun! When you're all done, have a look at the manual again - I did a video this time! We'll be playing with Pattern Matching.

{% include next_page.html link="../../manual/pattern-matching-basics" text="Learn About Pattern Matching" %}
