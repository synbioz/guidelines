# Ruby Guidelines #

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
users.map { |u| "#{user.first_name} #{user.last_name}" }

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
- freeze constants to [ensure they can't be changed](http://blog.endpoint.com/2011/12/working-with-constants-in-ruby.html)
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

## Strings and symbols ##

String class have multiple constructor, a common practice in Ruby is to convey the string purpose and context through it's constructor.
Symbols are not string at all. They are Objects that have both a string representation and an integer representation. They are immutable.

Most of the points below are easthetic considerations. Keep in mind that consistency will be prevalent criterion to choose wich constructor to use

### As identifier ###

Your first choice for identifier should be symbols where it is possible, ( identifier are mostly immutable, since symbols are immutable they are a good choice )

Avoid complex symbols they should be easy to read :

```ruby
{:key => :my_value} # Ok
{:'un ugly symbol' => :'is difficult to read'} # NOK
```

When you've got complex indentifier, or non alpha characters use single quoted stings:

```ruby
I18n.t('.do_not_start_with_alpha_character') # the leading dot would not fit nicely with symbols
```

A list of identifiers 

```ruby
%w(first second last)
```


### As template ###

When you declare a string that will change given it's context, it is a good practice to declare with double quoted strings.

Example :

```ruby
frequency = lambda { |t| 1.0/t }
heartbeat_period = 55
cardiac_frequency = frequency.call(heartbeat_period)
puts "your cardiac frequency is #{cardiac_frequency.round(5)} Hz"

```

In this example the double quoted string allow us to interpolate values. This allow the string to mutate given it's context. If your hearbeat is null, for example ( you seem to be dead )
Note that computation within strings is a bad habbit. Prefer to use variables.

Using double quoted stings for this purpose will warn you and your teamates that something will happen in the string just by reading the first character (").

### Multi-line strings ###

For multi-lines strings, like text fragments (you first have to avoid hardcoding text fragments, prefer to load files that contain text), there are here documents constructors:

```ruby
the_man = <<THEMAN
Haskell Brooks Curry (/ˈhæskəl ˈkɜri/; September 12, 1900 – September 1, 1982) was an American mathematician and logician. 
  Curry is best known for his work in combinatory logic; while the initial concept of combinatory logic was based on a single paper by Moses Schönfinkel,[1] much of the development was done by Curry.
  Curry is also known for Curry's paradox and the Curry–Howard correspondence. 
  There are three programming languages named after him, Haskell, Brooks and Curry, as well as the concept of currying, a technique used for transforming functions in mathematics and computer science.
THEMAN
```
The class remain String, but the constructor allow different behaviour.

### General advices ###

- use string interpolation rather than string concatenation
- avoid using `String#+` when you need to construct large data chunks. Use `String#<<` instead — `String#<<` mutates the string instance in-place which is faster than `String#+` which creates a new string objects on each call
- use `String#sub` and `String#tr` when possible rather than `String#gsub` which is much slower (`gsub` is required only for global substitution)
- keep consistent behaviour for ease of read

```ruby
name = 'Nico' # nothing happen in this string
message = "This is a message for #{name}:" # double quotes be aware something happen

notes.each do |note|
  message << "\n #{note}"
end

url.sub('http://', 'https://')
str.tr('-', '_')
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
