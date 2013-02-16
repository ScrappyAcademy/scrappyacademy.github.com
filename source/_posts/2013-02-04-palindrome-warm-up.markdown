---
layout: post
title: "Palindrome Warm-Up"
author: Emily Williamson
date: 2013-02-04 09:36
comments: false
categories:
- ruby
- warmup
- project euler
---
This week we continued our efforts at Project Euler tackling # 4, [Largest
Palindrome Product](http://projecteuler.net/problem=4)

> A palindromic number reads the same both ways. The largest palindrome made
> from the product of two 2-digit numbers is 9009 = 91 99.
>
> Find the largest palindrome made from the product of two 3-digit numbers.

This week instead of diving into code head-on we discussed what the exact
problem was, _We wanted to find the largest palindrome for a product of numbers
from 100-999._

We then wrote up our thoughts into a format for the code we thought we should
be writing to solve the problem:

`products(100..999).select(&:is_palindrome?).max`

Doing this allowed us to visualize a possible solution through code. It also
made obvious a few small problems we needed to solve before solving the main
question.


## Working with our code to find a solution:

**1. Produce an array of products**

We looked through [Ruby Doc](http://ruby-doc.org/core-1.9.3/Array.html) to find
the perfect method to call on an existing array to create a new array of
products.

[`repeated_combination`](http://ruby-doc.org/core-1.9.3/Array.html#method-i-repeated_combination)
was exactly what we were looking for. This creates a new array of all the
combinations of elements in your existing array, without duplicates.

We then used the `map` method to generate the products of these new number
pairs.

Our Resulting Code:

```ruby
def products(range)
  # Range can be an array or numbers or a Range. Either way calling `to_a`
  # ensures we have an array to work with
  range.to_a.repeated_combination(2).map{|nums| nums.reduce(:*)}
end
```

**2. Evaluate products to see which products are palindromes**

This involved much discussion. At first we discussed manipulating the values
as an array.

What if we called `reverse` on the first three and last three indexes of the
array values and if they were equal we had our palindromes?

This could be a solution, but would result in a lot of labor intensive work by
the function to manipulate every value several times.

Will, a Scrappy member, brought up changing the array to a string. This way we
could just call `reverse` on all the strings. Then it is simply a matter of
checking if the string is the same forwards and backwards.

This seemed like the best idea. So we decided to delegate the `is_palindrome?`
logic to `String`:

```ruby
class Numeric
  def is_palindrome?
    to_s.is_palindrome?
  end
end
```


**3.  Define `is_palindrome`**

This problem was pretty easily solved.

As we had already discussed figuring out what values were palindromes, we just
needed our strings before we could implement the function.

```ruby
class String
  def is_palindrome?
    self == reverse
  end
end
```

**4. Test our functions**

We had solved all our mini problems so now it was time to tackle the main
question:

> Find the largest palindrome made from the product of two 3-digit
> numbers from 100-999.

We tested our functions in IRB: `products(100...1000, 2).select{|n|
n.is_palindrome?}.max` sure enough returned the correct answer

**5. Correct our function to work with any number of arguments**

What excitement! We had collectively solved our weekly Project Euler problem,
but could we do more?

Yes! Our functions only returned the largest palindrome for a products of 2
numbers. What if we wanted to explore the largest palindrome for the product
of 3 numbers? No Problem!

To update our function we created a variable `n_elements` for the number of
arguments to be passed into the `repeated_combination` method in place of "2".

Defining a variable for the number of arguments allows you to customize the
numbers you're passing in to create a product.  Our new `products` function
looked like this:

```ruby
def products(range, n_elements = 2)
  range.to_a.repeated_combination(n_elements).map{|nums| nums.reduce(:*)}
end
```

**6. Wrapping Up**

So we had a solution. One thing that should be stated, was that we did monkey
patch Ruby core. There is a lot of discussion in the community about how this is
bad. In this case, we were willing to take the trade off with this simple patch
as it followed these rules:

  * Is local only to our project
  * Is generic and can easily be re-used
  * Another implementation would probably not differ at all

As always, Ruby is a language with lots of power. Use it responsibly.

Final solution:

```ruby
def products(range, n_elements = 2)
  # Range can be an array or numbers or a Range. Either way calling `to_a`
  # ensures we have an array to work with
  range.to_a.repeated_combination(n_elements).map{|nums| nums.reduce(:*)}
end

class String
  def is_palindrome?
    self == reverse
  end
end

class Numeric
  def is_palindrome?
    to_s.is_palindrome?
  end
end

products(100..999, 2).select(&:is_palindrome?).max
```


## What else happened at Scrappy?

After the warm-up we started exploring [Coffee
Script](http://coffeescript.org/).  The rest of our meetings this month will be
focusing on the Campfire chat client 'Hubot.'  Hubot uses Coffee Script to
interact with users and have some fun.

**What is Coffee Script?**

Coffee Script is a Ruby-like language that compiles to Javascript.

**Why would you use Coffee Script instead of good ole Javascript?**

There are several idioms specific to Javascript that can be overlooked after a
long day of coding in Rails. Coffee Script can make it easier to switch from
back end to front end work without overlooking some of those specific idioms.

Also, CoffeeScript is available by default in Rails. So it's just one of the
other technologies that are available for developer to learn.

We started exploring Hubot in campfire and even made our first update.  We
cloned the Hubot from Scrappy Academy's main repo directly onto our machine
from [GitHub](https://github.com/ScrappyAcademy/hubot)

Next we made a branch on the Scrappy Academy Hubot repo to match the issue we
wanted to solve, in this case we created the branch "Fix Cheer Me Up".

Our goal with "Fix Cheer Me Up" was to tell our Hubot to listen for the phrase
"cheer me up" and respond instead of having to ask our Hubot directly to "cheer
me up".

After cloning the repo onto our machine we opened up the `script`
file and then opened `cheer.coffee`.  We located "cheer me up" and
updated `robot.respond /cheer me up...` to `robot.hear /cheer me up...`

Now we needed to update the main repo with the changes we made on our local
machine.

We went to GitHub and created a Pull Request to merge the updates from our "Fix
Cheer Me Up" branch to the Hubot "Master" branch.

We checked to make sure all systems were a go and then merged our updates onto
Master, success!

Now our hubot takes note anytime "cheer me up" is mentioned in converstaion on
our Campfire site instead of having to ask our hubot directly to cheer us up!

This week our goal is to tackle some of the issues with Hubot in the Scrappy
Academy repo.  What issues are you tackling this week on Hubot?
