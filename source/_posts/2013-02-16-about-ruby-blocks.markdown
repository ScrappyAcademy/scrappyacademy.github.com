---
layout: post
title: "About Ruby Blocks"
author: Christopher Sigg
date: 2013-02-16 16:10
comments: false
categories:
- ruby
---
After learning a bit about Ruby and the basic way that data is stored and
manipulated I came across the concept of the Block.

I was enthralled...and bewildered.

The concept seemed simple enough. Blocks are pieces of code, or instructions,
that are passed to a function.

Amazing! Passing messages to other messages! This sounds highly advanced.  I
confess my first thought _"Why would I ever want to pass a message to another
message"?_

This blog entry will begin with the syntax used to pass a Block to a function
and finish with a discussion on the usefulness of being able to pass code to a
function.


## Syntax

So first a bit about Blocks in general. A Block is a bit of code that follows a
function call and is contained within a set of braces `{}` or `do` / `end`
delimiters. There is a common convention that braces should be used where the
Block is only one line and `do` / `end` delimiters are used where the code
extends over multiple lines. Examples below...

```ruby
some_function(arguments) { puts "Short Code Example" }
```

```ruby
some_function(arguments) do
  puts "This is a style"
  puts "commonly used for code in a block"
  puts "fitting over multiple lines"
end
```

_Jim Weirich discusses another convention specific to the presence of a return
value in his [blog
entry](http://onestepback.org/index.cgi/Tech/Ruby/BraceVsDoEnd.rdoc).

Regardless of how the Block is defined, it is always listed after a message
starting on the same line as the message call.

**General Syntax**

```ruby
object.message(arguments) {block}

object.message(arguments) do
  # block
end
```

**Example 1:**

```ruby
3.each { puts "I can't stop printing!" }
```

**Console:**

```text
I can't stop printing!
I can't stop printing!
I can't stop printing!
```

**Example 2:**

```ruby
array = %w[ geography art science ]
array.each do |subject|
  puts "I love #{subject}"
end
```

**Console:**

```text
I love art
I love science
I love math
```

The second example demonstrates how a Block can accept an argument passed to it
from the function. In this case it is the subject or value of the array.

## Usage

_So a block is a piece of logic that is passed with a message. But other than
utilizing simple pre-built iterators how can I unleash a Block within my own
code? How can I write a function that accepts a Block?_

Blocks are implicitly accepted by all functions. Even if the function does not
use the block, it is valid syntax to pass it.

However, often, you do want to use the block. To write the function to
accommodate the presence of the block use `yield`. To provide a default
behavior if no block is provided it can be switched on with `if block_given?`

When the function reaches `yield` the block is invoked. If arguments are to be
passed to the block they are listed after `yield`. ( `yield arguments`)

```ruby
def type_of_day
  if block_given?
    puts yield
  else
    puts "I havn't used a block all day, this code sucks"
  end
end

type_of_day { "I'm using a Block just like a real Ruby programmer!" }
```

Blocks are particulary usefull when you find yourself writing lots of functions
containing the same code. If you have five functions with code that differs
only by one line...consider using a block. This way, you can condense all of
the functions into one well written function that contains a `yield` statement
with the differing code.

Another similar example would be writing a function that contains a bit of
logic that might change in the future (think `each`). If you build a function
that iterates through an array and performs a bit of logic on each index you
could write a function that accepts a block containing the logic to perform.

The following example shows code that might exist for a program designed to
automate yard maintenance. The `Yard` function has a public interface called
`maintain_garden` that contains several functions needed for garden upkeep.

```ruby
class Yard
  def maintain_garden
    water_garden
    weed_garden
    yield if block_given?
  end

  # other functions omitted for brevity
end
```

Utilizing a block the `maintain_garden` function is dynamically able to provide
a way for others to hook in additional behavior. This way, if we decide to add
functions like `fertilize_garden` and `harvest_garden` we can pass them into
the function without having to modify the default behavior.

```ruby
first_yard = Yard.new

### During the winter
first_yard.maintain_garden

### During the spring
first_yard.maintain_garden { fertilize_garden }

### During the fall
first_yard.maintain_garden do
  harvest_garden
  till_garden
end
```

## Conclusion

Block's can be used to make your functions more versatile allowing you to
reduce duplication of code (making your code more DRY). They can also be
utilized where logic may change in the future. Instead of having to go back and
manipulate the code within a function a block can be used to pass new logic to
the existing function.
