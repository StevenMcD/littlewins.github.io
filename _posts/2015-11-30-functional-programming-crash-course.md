---
layout: page
title: 'A Quick Functional Programming Primer'
summary: "If you're not familiar with functional programming, read on."
category: manual
---

## Why Are Functional Languages Becoming So Popular?

The simple answer is that computers aren't getting *bigger*, they're getting *wider*. More CPUs that can process things at the same time. Most popular languages today don't embrace parallel processing directly. Javascript allows for asynchronous programming but does so with a single thread and an external process called the Event Loop.

Functional languages are a natural for distributed programming because they don't allow *mutation* and tend to be quite self-contained. Moreover the Erlang VM operates in a distributed way, encouraging programmers to develop processes, agents and tasks as opposed to objects, classes and libraries.

Sound familiar? Perhaps distributed, message-based architectures or (to use a buzzword) *microservices*? Yes, this is the same pattern. And Jessica Kerr ([@jessitron](http://twitter.com/jessitron)) sums it up pretty well:

> Erlang has been providing [the connection between services] for literally 25 years. As we get more and more sophisticated microservice implementations, each one grows their own crappy version of Erlang

It's true. Erlang has been around for many, many years and has created the standard for bullet-proof, fast, distributed programming. Other languages and platforms are trying to emulate what it does already with interesting new architectural approaches.

Microservices, message-based architectures, CQRS... or you can use Elixir, which has it all built in. Another way to look at this is to tweak [Greenspun's Tenth Rule](https://en.wikipedia.org/wiki/Greenspun%27s_tenth_rule):

> Any sufficiently complicated [distributed architectural pattern in an object-oriented language] contains an ad hoc, informally-specified, bug-ridden, slow implementation of half of [Erlang].

This is also true.

## Immutability

One of the core aspects of functional programming is *Immutability*, which basically means "you can't change things". Elixir is not as strict as Erlang and other languages in this regard.

Consider some data that represents the state of something - like a `ShoppingCart`. With Javascript you might have this:

```js
var ShoppingCart = function(){
  items = [];

  this.addItem = function(item){
    //find the item, if not there push.
  };

  this.total = function(){
    var sum = 0;
    //loop the items and sum it up
  }

  this.count = function(){
    return items.length;
  }
};

var cart = new ShoppingCart();
cart.addItem({sku: "MAV", price: 10000000000});
```

You create an instance of the `ShoppingCart` and then mutate it by adding an item to it. This changes the state of the cart. You can keep calling `addItem` and it will keep changing the items array within the cart:

```js
cart.addItem({sku: "ROVER", price: 3333223322});
cart.addItem({sku: "HAB", price: 4433222234});
```

The `cart` variable acts like a container that both holds data and receives messages (`addItem`) to change that data. Variables in functional languages don't act this way. In fact **you can stop thinking of them as variables and use the term 'labels'**.

In a pure functional language you would express adding an item to a cart like this:

```
cartNoItems = ShoppingCart.new()
cartStateOneItem = ShoppingCart.add_item(cartNoItems, {sku: "MAV", price: 10000000000})
cartStateTwoItems = ShoppingCart.add_item(cartStateTwoItems, {sku: "ROVER", price: 3333223322})
cartStateThreeItems = ShoppingCart.add_item(cart, {sku: "HAB", price: 4433222234})
```

Obviously that's a bit over the top, but the idea is that functions in a functional language try to be *pure*, taking in what they need to perform their work in the form of parameters, and passing back their value. *Nothing else*.

Elixir bends the rules a bit on this. In a strict functional language you can't reuse variables/labels because, as I mention, *you're not allowed to change things*. Elixir gets around this by *rebinding* a variable on the fly - essentially dropping the old value and reassigning:

```elixir
cart = ShoppingCart.new()
cart = ShoppingCart.add_item(cart, {sku: "MAV", price: 10000000000})
cart = ShoppingCart.add_item(cart, {sku: "ROVER", price: 3333223322})
cart = ShoppingCart.add_item(cart, {sku: "HAB", price: 4433222234})
```

The important thing to notice here is that we **must** pass the cart to our function as well as what we want to add to it. What we get back is a new cart with the items added. This is a tough style of programming to get used to, but the benefits are huge.

## Side Effects

Not being able to change things will drive you insane initially. It did for me. Tweaking properties, calling messages on objects and having events fire was something that I was used to. Looking back on this now after having worked with nothing but Elixir for the last few months has completely changed my perspective.

The very essence of object-oriented design is the notion of *encapsulation*. In other words, my program doesn't know how `ShoppingCart` maintains its state, nor should it care. This is a good design goal, but it also can be problematic to debug for the very same reason: *you don't know up-front what's going on*.

With a functional language, each function does a specific thing and relies solely on the parameters given to it. Values in, values out - and that's it. You can't tweak an internal setting, you can't change any state from within the function.

These tweaks and changes are called *Side Effects* - literally just changing something. So in conversation when you say "immutable" you're also saying "no side effects" - it's the same thing.

In our Elixir `ShoppingCart` example our function `add_item` operates completely within its own little scope:

```elixir
cart = ShoppingCart.new()
cart = ShoppingCart.add_item(cart, {sku: "MAV", price: 10000000000})
cart = ShoppingCart.add_item(cart, {sku: "ROVER", price: 3333223322})
cart = ShoppingCart.add_item(cart, {sku: "HAB", price: 4433222234})
```

The benefits of this cannot be overstated:

 - Immutability pushes you to write smaller, more targeted functions focused on doing a single thing
 - Your tests are clearer, simpler, and cleaner as you control the state before and after each one
 - Debugging your code is greatly simplified because of the above

Some lofty claims here, but as you read through this manual and start writing your own code, you will experience a shift in thinking. This can be uncomfortable and frustrating but once it clicks, you won't want to look back.

## Distributed Programming

As mentioned above, functional programming is a natural fit for distributed programming *precisely because of immutability*. You don't share state between objects and structures - and this pattern ripples out to a macro level with your system architecture: **independent systems that perform isolated tasks concurrently**.

Erlang has been doing this for the last 25 years, and doing it well. It's an old pattern and is now gaining momentum because processor speed is, basically, *maxing out*. The only thing we can do to make a machine faster is to add another processor.

Now, the software needs to catch up. And that's why you're here.

{% include message_popup.html message="Your help with our Physics library" link="../../email/help-with-getting-the-physics-library-started"  %}
