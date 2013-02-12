---
layout: post
title: "Fibonacci Warm-Up"
author: Aaron Kromer
date: 2013-01-28 19:24
comments: false
categories:
- ruby
- warmup
---
Something we started doing just this week, and plan on continuing moving
forward, is tackling "warm-up" problems. This is reminiscent of high school math
class. These are relatively simple programming problems that are solved via
_pairing_.

This week, we solved via _group-pairing_ is where the entire group participates
in solving the problem.  One person is designated as the _coder_. S/he displays
her/his laptop on the conference room TV (via an Apple TV). The group then
discusses the problem, and codes up a solution together.

Often this leads into side discussions about various programming techniques,
tips, tricks, etc. It's a good time for all. Everyone at different experience
levels walks away with something new.

Last week we started tackling [Project Euler](http://projecteuler.net/)
problems. Up first was [_Even Fibonacci numbers_](http://projecteuler.net/problem=2).
This appeared to be a relatively straight forward problem. The first solution
that was attempted looked a bit like:

```ruby
a = 1
b = 1
sum = 0
while b < 4_000_000
  a = b
  b = a + b
  if b.even?
    sum = sum + b
  end
end
puts sum
```

However, this produced an incorrect result. Upon further investigation, the bug
was in lines 5 and 6. Due to the order of the statements the value of `a` gets
overwritten, before it can be added to `b`. The evaluation looks a bit like
this:

```ruby
a = 1; b = 1    # Before loop
a = 1; b = 2    # 1st pass
a = 2; b = 4    # 2nd pass
a = 4; b = 8    # 3rd pass
```

So to fix this, the group replaced those two lines of code with:

```
tmp = b
b = a + b
a = tmp
```

Now when things get evaluated, it looks like:

```ruby
a = 1; b = 1; tmp = nil    # Before loop
a = 1; b = 2; tmp = 1      # 1st pass
a = 2; b = 3; tmp = 2      # 2nd pass
a = 3; b = 5; tmp = 3      # 3rd pass
```

This led into a side discussion about some Ruby idioms. Namely, multiple
assignment. We were able to replace these three lines of code with one:

```ruby
a, b = b, a+b
```

This will do exactly as you expect. It helps to understand what Ruby is doing
behind the scenes. In essence, it's evaluating and storing the right-hand-side
(_rhs_ as it is commonly seen in error messages). Then it uses the `splat` to
dereference the values and assign them in turn. Think of it as:

```ruby
tmp = [b, a+b]
a, b = *tmp
```

Additionally, we talked about the `if` and `unless` one-liners. These are great
ways to shorten up the code. Our final solution, took the form:

```ruby
a = b = 1
sum = 0
while b < 4_000_000
  a, b = b, a+b
  sum += b if b.even?
end
puts sum
```

While this is a good solution. It wasn't very re-useable. So to make it more
re-useable we wrapped it in a method:

```ruby
def even_fibonnaci_sum(upper_limit)
  a = b = 1
  sum = 0
  while b < upper_limit
    a, b = b, a+b
    sum += b if b.even?
  end
  sum
end
```

{% pullquote %}
That's great. Now we can re-use it anywhere, and we are no longer locked into a
`4_000_000` cap. However, the
[Fibonnaci](http://en.wikipedia.org/wiki/Fibonacci_number) sequence is very
common. And this method doesn't let us re-use that. {" So at this point we took
a step back, and asked: "What is the code that we wish we could write?" "} The
answer looked something like:
{% endpullquote %}

```ruby
Fibonnaci.upto(4_000_000).select(&:even?).sum
```

A nice little one-liner. And it's very clear what we are doing. In English,
this would read: _"For Fibonnaci numbers up to 4,000,000. Take the even numbers
and sum them."_

Sadly, there is no Fibonnaci number generator, but we can
[build sequences](http://www.dev.gd/20130114-building-sequences-with-enumerator.html).
In fact the Ruby docs show an example of how to create one
[Fibonacci Generator](http://ruby-doc.org/core-1.9.3/Enumerator.html#method-c-new).

So we coded one up:

```ruby
def Fibonnaci
  Enumerator.new do |y|
    a = b = 1
    loop do
      y << a
      a, b = b, a + b
    end
  end
end
```

Great! We have an [`Enumerator`](http://ruby-doc.org/core-1.9.3/Enumerator.html),
but there's no `upto`. So we created one:

```ruby
module Sequentially
  def upto(limit, &block)
    enum = Enumerator.new do |y|
      each do |num|
        break unless num < limit
        y << num
      end
    end

    block ? enum.each(&block) : enum
  end
end
```

And updated the Fibonnaci generator accordingly:

```ruby
def Fibonnaci
  Enumerator.new{ |y|
    # see above for meat
  }.extend Sequentially
end
```

Sweet! `select` will work just fine. However, `sum` doesn't exist (at least not
outside Rails). So, let's patch that in.

```ruby
module Enumerable
  def sum
    reduce(:+)
  end
end
```

So this final solution is **_a lot longer_**. However, it is composed of very
re-usable parts that we can continue to use for more
[Project Euler](http://projecteuler.net/) puzzles. Put that all together and
you have the elegant solution:

```ruby
module Enumerable
  def sum
    reduce(:+)
  end
end

module Sequentially
  def upto(limit, &block)
    enum = Enumerator.new do |y|
      each do |num|
        break unless num < limit
        y << num
      end
    end

    block ? enum.each(&block) : enum
  end
end


def Fibonnaci
  Enumerator.new{ |y|
    a = b = 1
    loop do
      y << a
      a, b = b, a + b
    end
  }.extend Sequentially
end

Fibonnaci.upto(4_000_000).select(&:even?).sum
```
