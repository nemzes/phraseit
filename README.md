# Phraseit

[![Build
Status](https://travis-ci.org/nemzes/phraseit.svg?branch=master)](https://travis-ci.org/nemzes/phraseit)

`phraseit` is a Javascript module for madlibs-style sentence templating. It has
been forked from [Sentencer](https://github.com/kylestetz/Sentencer), which
seems abandoned.

It is a simple templating engine that accepts strings with actions embedded in
them:

```javascript
"This is {{ an_adjective }} sentence.";
```

Where each action returns a random string selected from a list:

```javascript
"This is a bankrupt sentence.";
```

Think of it as madlibs for Javascript. Want to roll your own lorem ipsum
generator? `Phraseit` allows you to write the structure of your sentences and
plug in any kind of vocabulary you choose. The noun and adjective lists come
from a relatively small curated selection of Ashley Bovan's excellent [Word
Lists for Writers](http://www.ashley-bovan.co.uk/words/partsofspeech.html).

### How

`npm install phraseit --save`

```javascript
var Phraseit = require("phraseit");

Phraseit.make(
  "This sentence has {{ a_noun }} and {{ an_adjective }} {{ noun }} in it."
);
// returns something like "This sentence has a bat and a finless cinema in it."
```

Here are all of the options, described in detail below.

```javascript
var Phraseit = require("phraseit");

Phraseit.configure({
  // the list of nouns to use. Phraseit provides its own if you don't have one!
  nounList: [],

  // the list of adjectives to use. Again, Phraseit comes with one!
  adjectiveList: [],

  // additional actions for the template engine to use.
  // you can also redefine the preset actions here if you need to.
  // See the "Add your own actions" section below.
  actions: {
    my_action: function() {
      return "something";
    }
  }
});
```

### Actions

`Phraseit` works by recognizing "actions" within `{{ double_brackets }}`. It
replaces these actions with strings. The default actions are `{{ noun }}`, `{{
a_noun }}`, `{{ adjective }}`, and `{{ an_adjective }}`, but you can extend
`Phraseit` to include any kind of actions you need!

The default actions will continue to work if you pass in new a `nounList` and/or
`adjectiveList` using `Phraseit.configure`.

`Phraseit`'s actions are written semantically so that your sentence template
still reads as a sentence. While this was simply a design decision, it does make
templates easier to read and you are encouraged to follow this format if you
create custom actions.

#### `"{{ noun }}"`

Returns a random noun from the noun list.

```javascript
var noun = Phraseit.make("{{ noun }}");
// "actor", "knight", "orchid", "pizza", etc.
```

#### `"{{ a_noun }}"`

Returns a random noun from the noun list with "a" or "an" in front of it.

```javascript
var nounWithArticle = Phraseit.make("{{ a_noun }}");
// "an actor", "a knight", "an orchid", "a pizza", etc.
```

#### `"{{ adjective }}"`

Returns a random adjective from the adjective list.

```javascript
var adjective = Phraseit.make("{{ adjective }}");
// "blending", "earthy", "rugged", "untamed", etc.
```

#### `"{{ an_adjective }}"`

Returns a random adjective from the adjective list with "a" or "an" in front of
it.

```javascript
var adjective = Phraseit.make("{{ an_adjective }}");
// "a blending", "an earthy", "a rugged", "an untamed", etc.
```

### Add your own actions

When configuring `Phraseit` you can provide your own "actions", which are just
functions that return something. The name of the function that you pass into
`actions` is how you will reference it within a sentence template.

Here's an example of an action that returns a random number from 1 to 10.

```javascript
var Phraseit = require('phraseit');

Phraseit.configure({
  actions: {
    number: function() {
      return Math.floor( Math.random() * 10 ) + 1;
    }
  }
});

console.log( Phraseit.make("I can count to {{ number }}.")
// "I can count to 5."
```

#### Actions can take arguments

You can pass arguments into your actions. We can use this to make a smarter
version of the random number generator above...

```javascript
var Phraseit = require('phraseit');

Phraseit.configure({
  actions: {
    number: function(min, max) {
      return Math.floor( Math.random() * (max - min) ) + min;
    }
  }
});

console.log( Phraseit.make("I can count to {{ number(8, 10) }}.")
// "I can count to 8."
```

A technical note: if `Phraseit` finds that you have provided arguments to your
action it will use `eval` in order to call it. It will `try`/`catch` this in
case it fails, but one definite limitation is that your action can't contain
characters that would force you to use `object["property"]` notation. For
example, `"{{ my-custom-action(3) }}"` would fail, whereas `"{{
my_custom_action(3) }}"` would succeed.

### Where are the verbs?

Verb pluralization, singularization, and tense modification are difficult
computer science problems. `Phraseit` doesn't aim to solve those problems,
however _present tense_ verb pluralization/singularization is an experimental
feature of [`natural`](https://github.com/NaturalNode/natural) and could be
integrated if necessary.

---

`Phraseit` was originally created and maintained as
[Sentencer](https://github.com/kylestetz/Sentencer) by [Kyle
Stetz](https://github.com/kylestetz). The original prototype came out of
[Metaphorpsum](https://github.com/kylestetz/metaphorpsum) but waseen rewritten
from the ground up. It was forked and renamed as `Phrasit` by [Nelson
Menezes](https://github.com/nemzes) to address old dependency issues, since
`Sentencer` seemed abandoned.
