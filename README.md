# Guidelines

## General Rules

- All text files should use Unix-style text format
- All text files should use UTF-8 as charset
- All code files should use the .rb extension
- All existing copyright notices must be retained. You can add your own if necessary

## Coding Style

It can be a little annoying to change your coding style if you're used to something else, but we need to use only one style to allow people to read the code and understand it easily.

- Use 2-space tabs for indentation and expand tabs to spaces (if you use VIM, set ts=2 and set et). Don't use tabs at all.
- Join words of class names by capitalizing the first letter of each word (CamelCase)
- Join words of method names and variables with underscores
- Constants should always be in uppercase and should have words separated by undescores
- Use plural names for arrays, hashes, ...
- Methods and functions should always have sensible defaults if possible
- Strings should be defined using double quotes where possible
- Space should be used liberally - don't be afraid to spread things out a little to gain some clarity. Generally, there should be one space between brackets and normal statements, but no space between brackets and variables, methods or functions
- For code blocks, use { ... } when they can be written into a single line, otherwise use do ... end
- When setting properties of objects, always use foo= instead of set_foo
- When a method returns a boolean, always use foo?
- Comment your code using RDoc style

## Patches

- Always provide patches in unified form (diff -u) and with a ChangeLog entry
- Always provide Unit Test for all the functionnalities you've added