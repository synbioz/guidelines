# Stylesheets Guidelines

[toc]

## Source format

- UTF-8
- Use Unix-style line endings
- Use **soft-tabs** with a **two space** indent. Don't use hard tabs at all.
- Never leave trailing white-space
- End with a blank newline
- Always use English

## Sass usage

Use `.sass` syntax as much as possible.
`.scss` can be used to easely import a `.css` file (just rename it).
Avoid using large `@mixins`, prefer `@extends`. 
Sass partials (imported in compiled sass files) are prefixed with `_` like `_form.sass`

## Compatibility issues

Use Autoprefixer as much as possible OR look at the [Can I Use Database](https://caniuse.com/).
Keep in mind that all CSS properties aren't available everywhere with the same behaviour. Flexbox, Grid layout or multiple backgrounds for example.
Test as much as possible in the two last version of Chrome, Edge, Safari, Firefox and IE (10-11)


## Coding Style

### Indent

Be careful of the white-space between _property key_ and _value_ (not needed in CSS but it is in SASS).

### Values 

Null value like `border: 0` don't need unit, so don't write it.
Write decimal values with the _optional_ `0` to be more consistent. ex: `margin: 1.5em auto 0.5em`

# File structure

```
src/stylesheets/
├── application.sass       # Compiled file, present at root, import other files
├── common                 # Common's stylesheets are used on multiple pages
│   ├── _anim.sass
│   ├── _button.sass
│   ├── _common.sass
│   ├── _errors.sass
│   ├── _form.sass
│   ├── _table.sass
│   ├── _title.sass
│   └── _var.sass
├── modules                # Specific to a VueJS components for example
│   ├── _alert.sass
│   ├── _autocomplete.sass
│   ├── _comments.sass
│   ├── _document-list.sass
│   ├── _events.sass
│   ├── _filestep.sass
│   ├── _modal.sass
│   ├── _multiselect.sass
│   └── ...
└── vendor                 # Imported stylesheets
    └── _normalize.scss
```

# [BEM](http://getbem.com/) and [OOCSS](http://oocss.org/) — Classes naming

## OOCSS for commons styles

In the `_common.sass` are generally the OOCSS classes for very common styles and _helpers_ like `align-right`, `.container` or other useful stuff that are not designed for BEM. If you come from Bootstrap or Foundation, they're OOCSS friendly.

## BEM for components 

**BlockName**--**elementName**__**modifierName**

> The Block start with an upper-case, all selectors use camelCase.

**Block** are like a JS components, if it can be copied anywhere in your app, it's a block, like `Navigation` or `Comment`
**Element** is generally a direct child or sub-child of the Block. It can be the title or the submit button of the component like `__link` or `__avatar`.
The **Modifier** is the state of the current Element (or Block in some cases). It can be `--isActive`, `--hovered` or `--alert`.

Better example:
```sass
.Comment
  color: blue
  &__avatar
    border-color: currentColor
    &--isActive
      border-color: white
```

# [Smacss](https://smacss.com/) - Properties Order

Categorize rules with this categories:

1. Base
1. Layout
1. Module
1. State
1. Theme

## Tips to follow

- Don't use `!important`, never, it's important.
- Avoid using `#id-selector`, they can't control their high specificity and you'll regret it later.
- Avoid using tag selector like `ul.nav li span a.link`, prefer specific classes that allow you to change DOM elements without changing styles (`.nav-link` for example).

