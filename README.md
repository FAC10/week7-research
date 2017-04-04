# week7-research

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


