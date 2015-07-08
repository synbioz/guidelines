# Ruby Guidelines #

## Table of Contents
  1.  [Source format](#source-format)
  1.  [Coding Style](#coding-style)
      1. [Spaces](#spaces)
      1. [Indentation](#indentation)
      1. [Breaking up long method calls](#breaking-up-long-method-calls)
      1. [Naming](#naming) 
  1.  [Control structures](#control-structures)
  1.  [Classes and Modules](#classes-and-modules)
      1. [Modules and classes nesting](#modules-and-classes-nesting)
      1. [Modules and classes references](#modules-and-classes-references)
  1.  [Methods](#methods)
  1.  [Exceptions](#exceptions)
  1.  [Strings](#strings)
  1.  [Collections](#collections)
  1.  [Regular expressions](#regular-expressions)
  1.  [Metaprogramming](#metaprogramming)
  1.  [Documentation](#documentation)
      1. [Comments](#comments)
      1. [Annotations](#annotations)
  1.  [Misc](#misc)


## Source format ##

All text files should:

- use `UTF-8` as charset
- use Unix-style line endings
- use `.rb` extension
- use **soft-tabs** with a **two space** indent. Don't use hard tabs at all.
- never leave trailing whitespace
- end with a blank newline

Try to avoid long lines as much as possible, split statement on multiple lines.

Always use English for everything everywhere.

## Coding Style ##

It can be a little annoying to change your coding style if you're used to something else but we need to use only one style to ease code reading.

### Spaces ###

Add spaces:

- around operators
- around curly braces
- after commas
- after colons
- after semicolons

Example:

```ruby
sum = 1 + 2
a, b = 1, 2
1 > 2 ? true : false
[1, 2, 3].each { |e| puts e }
```

Don't add spaces:

- around parentheses
- around brackets
- after exclamation mark

Example:

```ruby
some(arg).other
[1, 2, 3].length
!array.include?(element)
```

### Indentation ###

Indentation is a key component to a good readability:

- indent `when` as deep as `case`
- indent the `public`, `protected`, and `private` methods as deep as the method definitions they apply to
- align assignments

Examples:

```ruby
case some_var
when :foo
  puts "foo"
when :bar
  puts "bar"
else
  puts "something unknown"
end
```

```ruby
class Something
  private

  def private_method
    # body omitted
  end
end
```

```ruby
message = "Something"
array   = %w(one two three)
hash    = {one: 1}
```

### Breaking up long method calls ###

If you have a long line with multiple method calls, try to break it up on multiple lines:

```ruby
array.
  map(&:foo).
  reduce(&:bar)
```

Be consistent, always use trailing dots as it is a well known syntax for breaking lines. Even old version of the Ruby parser can handle it and you can paste the code as is in IRB.

With leading dot notation there's a slight performance overhead for the interpreter and moreover IRB don't know how to handle it.

You can also break up method calls with a lot of parameters in multiple lines:

```ruby
def send_mail(source)
  Mailer.deliver(
    to: 'bob@example.com',
    from: 'us@example.com',
    subject: 'Important message',
    body: source.text
  )
end
```

Pay attention to the indentation of parameters. You can omit the outer braces around an implicit options hash.

### Naming ###

Using good names for your classes, methods, variables and files is *very important* because it helps the reader to easily understand what kind of information the variable stores. Always use *clear and descriptive* names:

- use English only
- use snake_case for methods, variables and symbols
- use snake_case for file and directory names
- use plural names for arrays, hashes and other collections
- use CamelCase for classes and modules
- use UPPER_SNAKE_CASE for other constants
- use `_` or names prefixed with `_` for unused variables to avoid warnings

Method names that:

- set values should always end with a `=` — avoid things like `set_foo`
- return a boolean should always end with a `?`
- modify the receiver should always end with a `!`
  + when `foo!` method exists implement the `foo` counterpart if possible

## Control structures ##

Control structures are the basis of the code logic, be consistent:

- don't use parentheses around the condition of an `if` / `unless` / `while`
- never use `and` and `or`, use `&&` and `||` instead
- never use `unless` with `else`. Revert the condition to use `if` / `else`
- for single line body conditional use postfixed `if` / `unless`
- for simple single line conditionals use ternary operator
- for single line code blocks use `{` ... `}` otherwise use `do` ... `end`
- use `||=` freely to initialize variables
- don't use `||=` to initialize boolean variables since it will not act as expected if the current value is `false` — use `enabled = true if enabled.nil?` instead
- avoid more than three levels of block nesting
- leverage the fact that `if` and `case` are expressions which return a result

Example:

```ruby
# Postfixed condition
puts "OK" if condition && other_condition

# Ternary operator
title = user.male? ? "Mr" : "Mrs"

# Single line code block
users.map { |user| "#{user.first_name} #{user.last_name}" }

# Variable initialization (default value)
country ||= "France"

# Use return value
result =
  if condition
    x
  else
    y
  end
```

## Classes and Modules ##

- use modules whenever you only write class methods — classes should be used only if you need instances
- use `Struct.new` for trivial classes where you only need accessors, constructor and comparison operators (more info [here](http://stephaniehoh.github.io/blog/2013/12/28/the-ruby-struct-class/) and [there](http://blog.rubybestpractices.com/posts/rklemme/017-Struct.html))
- use `def self.method` to define class methods
- avoid `class << self` except when necessary
- explicitly use `self` as the recipient of internal class or instance messages to avoid shadowing by a variable and ease reading
- freeze constants to [ensure their content can't be changed](http://blog.endpoint.com/2011/12/working-with-constants-in-ruby.html)
- for stronger immutable constant needs create a module to hold it then freeze this module
- use a consistent structure in your class definitions
    + extend and include
    + constants
    + attribute macros
    + other macros (custom or framework ones for example)
    + public class methods
    + public instance methods
    + protected
    + private

Example:

```ruby
class SomeClass
  include SomeModule

  CONST = %i(draft open closed).freeze

  attr_accessor :name

  def self.class_method
    # Body omitted
  end

  def instance_method
    # Body omitted
  end

  protected

  def protected_method
    # Body omitted
  end

  private

  def private_method
    # Body omitted
  end
end
```

### Modules and classes nesting ###

Don't nest classes within the same file. Create one file per class in a folder named like the containing class:

```ruby
# foo.rb
class Foo
  # methods
end

# foo/bar.rb
class Foo
  class Bar
    # methods
  end
end

# foo/baz.rb
class Foo
  class Baz
    # methods
  end
end
```

### Modules and classes references ###

Use `::` to reference classes, modules and constants.
Use `.` for regular method invocation.

Example:

```ruby
SomeModule::SomeClass::SOME_CONST
SomeModule::SomeClass()
SomeClass.some_method
some_object.some_method
```

## Methods ##

When writing methods stick to the following rules:

- use `def` with parentheses when there are arguments
- use `def` without parentheses when the method doesn't accept any arguments
- never put a space between a method name and the opening parenthesis
- always set sensible defaults for arguments if possible
- use spaces around the `=` operator when assigning default values to method parameters
- use [keyword arguments](http://robots.thoughtbot.com/ruby-2-keyword-arguments) rather than old hash style
- use an empty line before the return value unless it's a one-liner
- avoid `return` where not required
- use empty lines to break up method into logical blocks
- avoid long methods, break it in smaller ones
- avoid long parameter lists

Example:

```ruby
def foo
  # body omitted
end

def method_with_args(arg1, arg2)
  # body omitted
end

def method_with_default_args(arg1, arg2 = :something)
  # body omitted
end

def bar?
  true
end

def remove_names_starting_with_a_letter!
  self.names.reject! { |name| name.start_with?("a") }
end

def keyword_arguments(user, membership: false, notification: true)
  # body omitted
end
```

## Exceptions ##

Exceptions are handy to notify and handle unexpected errors:

- [don't use exceptions for flow of control](http://www.codinghell.ch/blog/2013/03/31/how-and-when-not-to-use-exceptions/)
- avoid rescuing the `Exception` class, be more precise
- declare rescue blocks from the more precise exception to the less one
- raise supplying an exception class and a message

## Strings ##

`String`s are widely use, you must master it:

- define strings using double quotes where possible — single quoted strings don't bring much in term of performances
    
    Strongly disagree ! : 

    define strings with double strings where you mean double strings , do not mess with object's semantic.
    'my\string' == "my\string" => false
    
    Single quoted String and double quoted strings does not share the same behaviour and does not share the same escape sequences. 
    
    This is not a good advice regarding « you must master it » to encourage confusion between the two string litterals constructors.
    
    Thinking about you string's meaning is always better advice than « avoid to think about ... » 
    
    When reading code it is a context indicator, how can you interpret informations if you leave the context on the side ?
    It is like using the word 'fanny' out of it's context it does not mean the same in US in UK, in Australia. This lead to confusion.
    Also yet double quoted and single quoted string does not behave the same way there is not garantee that this differences will not get more significant divergences in later or earlier ruby versions.
    
    If you mean cat don't say dog !
    
    
    I would advice : Use double quoted strings when you mean it is a 'one shot string template' interpolation happen here. Where parsing string may alter it's content
    Use single quote where parsing does not alter it's content ( no interpolation ), and where you do not use symbols ...
    

- use string interpolation rather than string concatenation
- avoid using `String#+` when you need to construct large data chunks. Use `String#<<` instead — `String#<<` mutates the string instance in-place which is faster than `String#+` which creates a new string objects on each call
- use `String#sub` and `String#tr` when possible rather than `String#gsub` which is much slower

```ruby
name = "Nico"
message = "This is a message for #{name}:"

notes.each do |note|
  message << "\n #{note}"
end

url.sub("http://", "https://")
str.tr("-", "_")
```

## Collections ##

Collections are the kind of object you'll use the most along with strings. There's some gotcha you must know to use them at their best:

- prefer `%w` to the literal array syntax when to declare an array of strings
- prefer `%i` to the literal array syntax when to declare an array of symbols
- use `Set` instead of `Array` when dealing with unique elements
- do not modify a collection while traversing it
- don't use `count` as a substitute for `size` — for enumerable objects other than `Array` it will [iterate the entire collection to compute its size](http://rubyinrails.com/2014/01/ruby-count-vs-length-vs-size/)
- use `flat_map` instead of `map` + `flatten` when possible (arrays with a depth of 2)
- use `reverse_each` instead of `reverse.each` — it's more efficient since it doesn't allocate a new array

For `Hash`es:

- use symbols instead of strings as keys
- avoid the use of mutable objects as keys
- use `Hash#fetch` when dealing with keys that should be present as it raises a `KeyError` exception if missing
- use `Hash#fetch` to set a default values for given key
- use `Hash#values_at` to retrieve several values in a row
- rely on the fact that as of Ruby 1.9 hashes are ordered

Example:

```ruby
# Array of strings

STATES = %w(draft open closed)

# Array of symbols

STATES = %i(draft open closed)

# Using set

set = Set.new
# => #<Set: {}>
set << :foo
# => #<Set: {:foo}>
set << :bar
# => #<Set: {:foo, :bar}>
set << :foo
# => #<Set: {:foo, :bar}> #

# Symbols as hash keys

hash = { one: 1, two: 2, three: 3 }

# Using fetch

hash[:onee] # => nil
hash.fetch(:onee) # raises a KeyError
hash.fetch(:missing, "I'm not here") # => "I'm not here"
hash.fetch(:lazy_evaluated) { some_method } # only triggered in case of KeyError exception

# Retrieve multiple values
email, username = hash.values_at('email', 'username')
```

You should break up long collection assignments in multiple lines:

```ruby
menu_item = [
  "Item 1", "Item 2", "Item 3", "Item 4", "Item 5", "Item 6", "Item 7",
  "Item 8", "Item 9", "Item 10"
]
```
## Regular Expressions ##

Regular expressions can sometimes be the easiest way to parse a string but shouldn't be overlooked. Most of the times there's an alternative solution:

- don't use regular expressions for plain text search in string
- avoid using `$1-9` as it can be hard to track what they contain — use named groups instead
- use `x` modifier for complex regexps so whitespaces and comments in the pattern will be ignored

Example:

```ruby
# Named groups

/(?<meaningful_var>regexp)/ =~ string
...
process meaningful_var

# x modifier

regexp = %r{
  start         # some text
  \s            # white space char
  (group)       # first group
  (?:alt1|alt2) # some alternation
  end
}x
```

Take a look at the [Regexp API Documentation](http://ruby-doc.org/core-2.1.1/Regexp.html) since it's really exhaustive and well explained.

## Metaprogramming ##

Ruby is well known for its metaprogramming capabilities. Things you can do are really amazing. Nonetheless metaprogramming can be hard to understand and is resources heavy:

- avoid needless metaprogramming
- do not monkey-patch core classes when writing libraries
- use `method_missing` with care
    + only catch methods with a well-defined prefix, such as `find_by_*`
    + call `super` at the end of your statement 
    + delegate to assertive, non-magical methods. Don't do the job inside `method_missing`
    + whenever possible use `define_method` to avoid going through `method_missing` on each call

```ruby
def method_missing?(meth, *params, &block)
  if /^find_by_(?<prop>.*)/ =~ meth
    find_by(prop, *params, &block)
  else
    super
  end
end
```

## Documentation ##

*Always* use English!

Use [YARD](http://yardoc.org). It's a documentation generation tool shipped with a load of features and easily extensible and customizable.

Example:

```ruby
# Converts the object into textual markup given a specific format.
#
# @param format [Symbol] the format type, `:text` or `:html`
# @return [String] the object converted into the expected format.
# @note This is a note about a edge case of this method
def to_format(format = :html)
  # format the object
end
```

### Comments ###

Try to write self-documenting code as much as possible.

Avoid writing comments to explain bad code. If an explaination is needed then the code probably needs a refactor to become self-explanatory.

### Annotations ###

Annotations should usually be written on the line immediately above the relevant code. The annotation keyword is followed by a colon and a space, then a note describing the problem:

- use `TODO` to note missing features or functionality that should be added at a later date
- use `FIXME` to note broken code that needs to be fixed
- use `OPTIMIZE` to note slow or inefficient code that may cause performance problems
- use `HACK` to note code smells where questionable coding practices were used and should be refactored.

## Misc ##

Here are some general advices that don't belong to any previous section specifically:

- write `ruby -w` safe code
- never use `then`
- never use `for`, unless you know exactly why — `for` doesn't introduce a new scope and variables defined in its block will be visible outside of it
- use the new lambda literal syntax (`->`) for single line body blocks and the lambda method for multi-line blocks
- prefer `proc` over `Proc.new`
- use `$stdout` / `$stderr` / `$stdin` instead of `STDOUT` / `STDERR` / `STDIN`
- use `warn` instead of `$stderr.puts` which allows for warnings suppression when needed
- add underscores to large numeric literals to improve their readability
