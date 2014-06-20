---
layout: post
title: "Double Pipes Equals What?"
date: 2014-06-19 23:56:26 -0400
comments: true
categories: "Flatiron&nbsp;School"
---
> Memoization is the process of storing a computed value to avoid duplicated work by future calls.
> - [Gavin Miller][1]

As novice Ruby programmers, it has not been a big deal for us so far to set variables equal to a given value, and re-evaluate the code setting that variable each time we need to call the method. However, our introduction to web scraping last week got me thinking about the potential benefits of memoization, and where else it might be useful as our skills and knowledge base expand.

But let's back up a minute first. What exactly *is* memoization, anyway? You actually probably already know and use it, even if you can't remember what it's called. It has to do with those funny `||=` symbols we've seen around, and with a process you are also likely familiar with from your web browser...

## Caching

[Web caches][2] sit between users and websites, watching requests come by and saving copies of the responses, so that when another request for the same website comes in, the cache can use the stored response it already has, instead of querying the origin server for it again. On the web, this improves response time for users, as well as reducing overall network traffic. **In other words, we can cache (or *memoize*) in order to save time and memory when accessing the same websites (or variables, in programming).** Memoization in Ruby functions the same way, and it carries out these evaluations using the `||=` operator. Let's take a look.

## Memoizing in Ruby

```ruby
a ||= b
```

Now, a common misconception about the `||=` is that it evaluates to `a = a || b`. Which would mean that we evaluate the statement `a = a` each time we run the code, and if the variable were not already defined, then returning `b` instead. I mean, the `+=` and `-=` operators work the same way, don't they?

Wrong! Well, yes, `a += b` does in fact evaluate to `a = a + b`. But in fact, memoization is implemented in an entirely different way from these math operators. We would do best not to think about them in the same terms at all. We can see a true memoized implementation demonstrated in the example [below][3].

```ruby
>> h = Hash.new(1)
=> {}

>> h[:x]
=> 1        # default value returned, but...

>> h
=> {}       # reference alone doesn't set key.



>> h[:x] ||= 2
=> 1        # again, default value returned...

>> h
=> {}       # but hash still empty!

>> h[:x] = h[:x] || 2
=> 1        # only when changing memoize's implicit implementation,

>> h
=> {:x=>1}  # can we set the key, changing what is returned.
```

## When to Use

Now, I sorta set you up in the above example. Hashes with default values are a bit of an edge case in memoization, in that most often, when we use memoization with hashes, we usually want to set a key equal to a value if the key does not already exist. But as you can see above, this was not the case until we expanded out the operation to include setting the key to itself *before* the double pipes. As an aside, the shorter memoize formula *does* work with hashes NOT initialized with a default value.

Welp! that about wraps it up. I'll leave off with a few general guidelines about when to use or avoid memoization, courtesy again of [Gavin Miller][5].

#### Bad Ideas
  - Methods that use instance variables.
  - [Class variables in your testing suite][6]. I haven't tried this one out myself, but apparently, your test database is wiped out before each subsequent example begins, **except for** class variables, which will then return the old memoized result on your fresh new test.

#### Good Ideas
  - Expensive calculations.
  - Repeated calculations that don't change.
  - Duplicated SQL database calls. (IF NOT EXISTS, anyone?)

#### Tricksy Edge Cases


  * Hashes with a default value. (See above, duh!)
  * Undefined variables. This is just Ruby being sneaky behind the curtain. Any time you see a variable assignment, *even if that code is never run* (such as within a false conditional statement), Ruby summons that variable into being, not raising a name error if you try to 'puts' it. So Ruby evaluates `a ||= b` at the parsing stage *differently* than it would see `a || a = b`, even though they evaluate the same way when the code is actually run. I am not sure why that changes between parsing and implementation, but Peter Cooper has a nice little clip about it at the bottom of [this post][4].

## Resources

- http://gavinmiller.io/2013/basics-of-ruby-memoization/
- https://www.mnot.net/cache_docs/
- http://dablog.rubypal.com/2008/3/25/a-short-circuit-edge-case
- http://www.rubyinside.com/what-rubys-double-pipe-or-equals-really-does-5488.html
- http://gavinmiller.io/2013/basics-of-ruby-memoization/
- http://cmme.org/tdumitrescu/blog/2014/01/careful-what-you-memoize/

[1]: http://gavinmiller.io/2013/basics-of-ruby-memoization/
[2]: https://www.mnot.net/cache_docs/
[3]: http://dablog.rubypal.com/2008/3/25/a-short-circuit-edge-case
[4]: http://www.rubyinside.com/what-rubys-double-pipe-or-equals-really-does-5488.html
[5]: http://gavinmiller.io/2013/basics-of-ruby-memoization/
[6]: http://cmme.org/tdumitrescu/blog/2014/01/careful-what-you-memoize/