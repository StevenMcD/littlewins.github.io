---
layout: email
title: 'I need you to setup a project'
from: "rob@redfour.io"
category: email
---

Hope you're enjoying the manual - it's taking me forever to put that thing together. I think I'll start using videos as it's a little faster than typing everything. Man I suck at typing. I learn better by *doing something* rather than reading up on concepts. But they're important so keep reading/watching.

I have something I need you to do first, however. I need you to setup a project so we can get some data out of a database. Specifically: PostgreSQL (surprise).

**Make sure you have Postgres installed**. If you don't, set it up thus:

 - **Mac**: Use Homebrew or go to http://postgresapp.com and install it as an app (it's amazing).
 - **Windows**: [Grab the installer at Enterprise DB](http://www.enterprisedb.com/products-services-training/pgdownload#windows)
 - **Unix**: You know what to do

## Use Mix

The manual will explain what Mix does but right now you can think of it as your Elixir Utility Belt. It builds your projects, runs tasks for you, installs packages - a fascinating tool. That comes later - right now open up your terminal and navigate to a directory where you can save your work. Then run this:

```elixir
mix new db
```

You should see some stuff splash on your screen:

```
* creating README.md
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/db.ex
* creating test
* creating test/test_helper.exs
* creating test/db_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd db
    mix test

Run "mix help" for more commands.
```

As you can see Mix created the harness of an application for you. There are different ways to think about this structure - as a package that's part of a library or as an app in its own right. Right now don't worry about it we'll get to that later.

As you can see you have a test directory, a `.gitignore` file, a `/lib` directory which you should be able to discern is where you code goes. Nothing in here should be weird except for one thing...

## exs vs ex

In the root of the project you see `mix.exs`. In your test directory you see `db_test.exs` and `test_helper.exs`. In the `lib` directory, however, we have `lib/db.ex`. So, what's the difference here?

The simple answer is the files ending with `ex` are **compiled** at compile time. Files ending with `exs` are scripts and are not compiled. If you forget and name a file with an `exs` extension and expect to use it with a file ending in `ex` you'll get a compile error.

It makes sense therefore that configuration, tests and settings are all scripted content.

## Running tests

You have a test already done for you that shows you the general structure of things. We'll get to that in a bit but you can run it right now (make sure you navigate into your new app directory):

```elixir
mix test
```

You should see this output:

```
Compiled lib/db.ex
Generated db app
.

Finished in 0.03 seconds (0.03s on load, 0.00s on tests)
1 test, 0 failures

Randomized with seed 378005
```

Mix is very helpful. It compiled our single Elixir file (`db.ex`), created our app, and then ran our test. This test is kind of silly - have a look:

```elixir
defmodule DbTest do
  use ExUnit.Case
  doctest Db

  test "the truth" do
    assert 1 + 1 == 2
  end
end
```

This is an Elixir module with a macro called `test`. It's wonderfully sparse isn't it? Play around with the assertions from what you know already with strings and so on. Feel free to output stuff to the terminal using `IO.inspect` too.

## Adding a Dependency

Crack open `mix.exs` in your favorite editor. It should look something like this:

```elixir
defmodule Db.Mixfile do
  use Mix.Project

  def project do
    [app: :db,
     version: "0.0.1",
     elixir: "~> 1.1",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps]
  end

  # Configuration for the OTP application
  #
  # Type "mix help compile.app" for more information
  def application do
    [applications: [:logger]]
  end

  # Dependencies can be Hex packages:
  #
  #   {:mydep, "~> 0.3.0"}
  #
  # Or git/path repositories:
  #
  #   {:mydep, git: "https://github.com/elixir-lang/mydep.git", tag: "0.1.0"}
  #
  # Type "mix help deps" for more examples and options
  defp deps do
    []
  end
end
```

There's a lot here - don't get bogged down with it, you'll understand it all soon enough I promise. What we need to do is head down to line 29, the `deps` function. These are our dependencies.

Let's add one for accessing Postgres:

```elixir
defp deps do
  [{:postgrex, "~> 0.9.1"}] #this is the current version as of this writing
end
```
Hey that's a tuple! I think you already know what that is. See how it's used here to specify a package and its version?

In order to use this in our application we have to make sure it's started up and configured properly. In other platforms this would mean configuring in the bootstrap file and calling a method like `start` or `init`. With Elixir we have a simpler way, which is to simply add this to our *appliction list*.

Let's do that next. On line 17 you should see a function called `application` - this is our startup routine. In there you will see a list of applications that are part of ours and there should be only one, the `:logger`.

Let's add Postgrex:

```elixir
def application do
  applications: [:logger, :postgrex]
end
```

That's it! Your entire `mix.exs` file should look like this:

```elixir
defmodule Db.Mixfile do
  use Mix.Project

  def project do
    [app: :db,
     version: "0.0.1",
     elixir: "~> 1.1",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps]
  end

  # Configuration for the OTP application
  #
  # Type "mix help compile.app" for more information
  def application do
    [applications: [:logger,:postgrex]]
  end

  # Dependencies can be Hex packages:
  #
  #   {:mydep, "~> 0.3.0"}
  #
  # Or git/path repositories:
  #
  #   {:mydep, git: "https://github.com/elixir-lang/mydep.git", tag: "0.1.0"}
  #
  # Type "mix help deps" for more examples and options
  defp deps do
    [{:postgrex, "~> 0.9.1"}]
  end
end
```

Now let's install our dependencies. Back in the terminal pop this in:

```
mix deps.get
```

Just like NPM or RubyGems, Mix heads over to [Hex](http://hex.pm) to find the package you asked for and it then downloads it for you, typically from Amazon S3:

```
Running dependency resolution
Dependency resolution completed successfully
  decimal: v1.1.0
  postgrex: v0.9.1
* Getting postgrex (Hex package)
Checking package (https://s3.amazonaws.com/s3.hex.pm/tarballs/postgrex-0.9.1.tar)
Using locally cached package
Unpacked package tarball (/Users/rob/.hex/packages/postgrex-0.9.1.tar)
* Getting decimal (Hex package)
Checking package (https://s3.amazonaws.com/s3.hex.pm/tarballs/decimal-1.1.0.tar)
Using locally cached package
Unpacked package tarball (/Users/rob/.hex/packages/decimal-1.1.0.tar)
```

You should now have a new directory in your project called `deps` and in there you should see `postgrex` as well as `decimal` - a project that `postgrex` needs. Go have a look! Dependencies are flattened with Elixir, which keeps directory bloat down (as opposed to NPM, whose recursive dependency structure can be maddening).

In fact, browse through the code some. When you install a dependency with Mix you get the code that then gets compiled into your project - not some hidden binary.

Let's compile our project again with `mix compile` and you'll see what I mean:

```
> mix compile

==> decimal
Compiled lib/decimal.ex
Generated decimal app
==> postgrex
Compiled lib/postgrex/binary_utils.ex
Compiled lib/postgrex.ex
Compiled lib/postgrex/extension.ex
Compiled lib/postgrex/error.ex
Compiled lib/postgrex/error_code.ex
Compiled lib/postgrex/result.ex
Compiled lib/postgrex/type_info.ex
Compiled lib/postgrex/extensions/text.ex
Compiled lib/postgrex/extensions/json.ex
Compiled lib/postgrex/utils.ex
Compiled lib/postgrex/builtins.ex
Compiled lib/postgrex/type_server.ex
Compiled lib/postgrex/types.ex
Compiled lib/postgrex/messages.ex
Compiled lib/postgrex/connection.ex
Compiled lib/postgrex/extensions/binary.ex
Compiled lib/postgrex/protocol.ex
Generated postgrex app
==> db
Compiled lib/db.ex
Generated db app
```

If there's a problem with the code you'll know about it immediately.

## That's a Win!

Nice job. Do me a favor and `git init` this, commit, and push it to a repo where you can build it out later. For now, read up on functional programming in the manual I'm putting together.

{% include next_page.html text="Functional Programming Crash Course" link="../../manual/functional-programming-crash-course"  %}
