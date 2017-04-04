# week7-research

### FYI: [Handlebars](http://handlebarsjs.com/) is a logic-less templating engine that dynamically generates your HTML page. It’s an extension of Mustache with a few additional features. Mustache is fully logic-less but Handlebars adds minimal logic thanks to the use of some helpers (such as if, with, unless, each and more)...

## Built-In Helpers

Handlebars includes a few built in helpers that make life easier. They are `{{#each}}`, `{{#if}}`, and `{{#unless}}`.

### The each Helper

The `{{#each}}` helper iterates over each item in an array. Here’s an example.

```html
<script id="each-template" type="text/x-handlebars-template">
  {{#each people}}
    ... output person's info here...
  {{/each}}
</script>
```
The above template would iterate over each item in the array named people and output the content of the block.

### The if Helper

The `{{#if}}` helper does just what you’d expect. It allows you to implement an if block in your code. The if helper outputs the block that it contains if the value given to it is truthy.

One tricky aspect of the helper, though, is that Handlebars doesn’t support conditional statements, so code like `{{#if x > y}}` isn’t possible. That’s on purpose. Our take is that any tricky logic like that can be wrapped up into a helper to make sure that your template stays nice and clean.

Here’s an example:

```html
<script id="each-template" type="text/x-handlebars-template">
  {{#if people}}
    ... output person's info here...
  {{/if}}
</script>
```
That template would only output the inside of the block if people was truthy, so it wouldn’t output if people was null, 0, false, or undefined. Probably a more appropriate if statement in the above example would be `{{#if people.length}}`, so that the block would also not be displayed if a people array is present, but empty.

### The unless Helper

The `{{#unless}}` helper is basically just the opposite of if. It only outputs the contained block if the given expression is false. So, for example:

```html
<script id="each-template" type="text/x-handlebars-template">
  {{#unless people.length}}
    There aren't any people.
  {{/unless}}
</script>
```

The above template would only output the sentence There aren’t any people if people.length evaluates to a falsy value like null, 0, false, or undefined.

### The else Expression

Handlebars.js includes a special expression, `{{else}}`, that can be used with any block helper to represent what should be output if the given expression evaluates to a falsy value. Here’s an example of how to use it:

```html
<script id="each-template" type="text/x-handlebars-template">
  {{#if people.length}}
    ... output person's info here...
  {{else}}
    There aren't any people.
  {{/if}}
</script>
```

### Partials

Partials come in handy when you have a chunk of a Handlebars.js template that you need to use in a few different contexts. The Handlebars.registerPartial method registers a partial. It takes the name of the partial as its first argument and either a template source string or a compiled template as its second argument. The fact that it accepts a compiled template as the second argument is actually pretty useful. That allows you, for example, to use the partial in a loop that outputs a list but also append items to the list later using the partial’s template function.

To use a partial from a template, simply include `{{> partialName}}`. Here’s an example of using a partial:

```html
<script id="people-template" type="text/x-handlebars-template">
  {{#each people}}
    {{> person}}
  {{/each}}
</script>

<script id="person-partial" type="text/x-handlebars-template">
  <div class="person">
    <h2>{{first_name}} {{last_name}}</h2>
    <div class="phone">{{phone}}</div>
    <div class="email"><a href="mailto:{{email}}">{{email}}</a></div>
    <div class="since">User since {{member_since}}</div>
  </div>
</script>

<script type="text/javascript">
  $(document).ready(function() {
    var template = Handlebars.compile($("#people-template").html());
    Handlebars.registerPartial("person", $("#person-partial").html());

    template(yourData);
  }
</script>
```

## Writing Custom Helpers

To register an expression helper, use the `Handlebars.registerHelper` method. It takes the name of the helper and the helper function as arguments. Handlebars.js takes whatever is returned from the helper function and writes it out to the template, so be sure to always return a string from your custom helpers.

To write an expression helper function to output a formatted phone number, you could define the following helper:

```js
Handlebars.registerHelper("formatPhoneNumber", function(phoneNumber) {
  phoneNumber = phoneNumber.toString();
  return "(" + phoneNumber.substr(0,3) + ") " + phoneNumber.substr(3,3) + "-" + phoneNumber.substr(6,4);
});
```

You would use the `formatPhoneNumber` helper in a template like this: `{{formatPhoneNumber phoneNumber}}`

Another Example:

```js
var Handlebars = require('handlebars');

Handlebars.registerHelper('custom', function(name, nationality) {
  // do something with the parameters
  var newString = name + ' is ' + nationality;
  return newString;
});
```

Here the helper function is called link which takes two parameters (name and nationality), does something with them, and then returns something.

Calling a helper within a .hbs file

```html
<article>
  {{custom name nationality}}
</article>
```

Here `custom` is the name of the Handlebars helper, and name and nationality are parameters passed to the helper - you could think of it like `custom(name, nationality)`

```js
var context = {name: 'Yvonne', nationality: American};
var html = template(context);
```
```html
<!-- html / hbs template file -->
{{custom name nationality}}
<!-- Yvonne is American-->
```

You can also do this: 

```js
<!-- html / hbs template file -->
{{custom "Alice", "Australian"}}
<!-- Alice is Australian -->
```

## The Debug Helper

It can be easy at times to get confused about where you are in the stack in a Handlebars.js template. You can keep a debug helper around to help you figure that out.

```js
Handlebars.registerHelper("debug", function(optionalValue) {
  console.log("Current Context");
  console.log("====================");
  console.log(this);

  if (optionalValue) {
    console.log("Value");
    console.log("====================");
    console.log(optionalValue);
  }
});
```

You could then use that helper in any template like:

`{{debug}}`

or like:

`{{debug someValue}}`
and you’ll see output in the JavaScript console letting you know what’s going on :tada:
