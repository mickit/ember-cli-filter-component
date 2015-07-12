# ember-cli-filter-component
[![Code Climate](https://codeclimate.com/github/zakmac/ember-cli-filter-component/badges/gpa.svg)](https://codeclimate.com/github/zakmac/ember-cli-filter-component)
[![Ember Observer Score](http://emberobserver.com/badges/ember-cli-filter-component.svg)](http://emberobserver.com/addons/ember-cli-filter-component)
[![Shields.io](https://img.shields.io/badge/tests-54%2F54-brightgreen.svg)](http://shields.io)
[![Test Coverage](https://codeclimate.com/github/zakmac/ember-cli-filter-component/badges/coverage.svg)](https://codeclimate.com/github/zakmac/ember-cli-filter-component/coverage)
[![Build Status](https://travis-ci.org/zakmac/ember-cli-filter-component.svg?branch=feature)](https://travis-ci.org/zakmac/ember-cli-filter-component)

## Contents
- <a href="#user-content-about">About</a>
- <a href="#user-content-installation">Installation</a>
- <a href="#user-content-usage">Usage</a>
- <a href="#user-content-examples">Examples</a>
- <a href="#user-content-contributing">Contributing</a>

## About

Filter an array of items based on specified properties using a text input field.

`ember-cli-filter-component` provides a `{{filter-content}}` block component, inside which you can specify a template and access some useful properties.

<img src="http://i.imgur.com/MiSiG2G.gif" width="300">

## Installation

```shell
cd /path/to/my-awesome-application
ember install ember-cli-filter-component
```

## Usage

**content (req.)** – The array of items being filtered.

**inputClassNames** – Class names to append to the query input field.
- Accepts a space-delimited string.
- **ex:** `"all-caps monospaced"`

**placeholder** – Placeholder string for the text input field.
- **ex:** `"Type here to filter..."`

**properties (req.)** – Properties on each item to filter.
- Accepts a space-delimited string.
- Specify `@each` to iterate an array.
- **ex:** `"title category.@each"`

**query** – The text string items on `content` are matched against.

**showInput** – Whether to show the filter query input field.
- Defaults to `true`.

## Examples

* <a href="#user-content-ex1">Dropping the component into an existing template</a>
* <a href="#user-content-ex2">Filter an array</a>
* <a href="#user-content-ex3">Filter a nested array</a>
* <a href="#user-content-ex4">Filter arrays of arrays</a>
* <a href="#user-content-ex5">Filter an object within an array</a>
* <a href="#user-content-ex6">Toggle `properties`' value with a button</a>
* <a href="#user-content-ex7">Add a showing/filtered count</a>
* <a href="#user-content-ex8">Filter multiple components simultaneously</a>

<a name="ex1"></a>**Dropping the component into an existing template**
```handlebars
{{! original template }}
<ul class="airports">
  {{#each busiestAirports as |airport|}}
    <li>{{airport.name.code}} – {{airport.name.longForm}}</li>
  {{/each}}
</ul>
```

```handlebars
{{! new template using filter-content }}
{{#filter-content content=busiestAirports properties="name.code name.longForm" as |fc|}}
  <ul class="airports">
    {{#each fc.filteredContent as |airport|}}
      <li>{{airport.name.code}} – {{airport.name.longForm}}</li>
    {{/each}}
  </ul>
{{/filter-content}}
```

<a name="ex2"></a>**Filter an array**
```handlebars
{{! filter by a specific item }}
{{filter-content content=htmlColors properties="1"}}
```
```handlebars
{{! filter by all items }}
{{filter-content content=htmlColors properties="@each"}}
```
```javascript
htmlColors: [
  ['00FFFF', 'Aqua'],
  ['FFE4B5', 'Moccasin'],
  ['708090', 'SlateGray']
]
```

<a name="ex3"></a>**Filter a nested array**
```handlebars
{{filter-content content=daysOfChristmas properties="title category.@each"}}
```
```javascript
daysOfChristmas: [{
  number: 1,
  title: 'A Partridge in a Pear Tree',
  category: ['vegetation', 'food', 'avian']
}]
```

<a name="ex4"></a>**![yodawg](http://i.imgur.com/wkB6nwQ.png)Filter arrays of arrays**
```handlebars
{{filter-content content=yoDawg properties="@each.@each"}}
```
```javascript
yoDawg: [[
  [1, 2, 3],
  ['A', 'B', 'C']
], [
  [98, 99, 100],
  ['X', 'Y', 'Z']
]]
```

<a name="ex5"></a>**Filter an object within an array**
```handlebars
{{filter-content content=cashBack properties="bills.@each.name coins.@each.name"}}
```
```javascript
cashBack: [{
  bills: [{
    count: 2,
    name: 'one dollar bill'
    value: 100
  }, {
    count: 1,
    name: 'two dollar bill'
    value: 200
  }],
  coins: [{
    count: 2,
    name: 'dime',
    value: 10
  }]
}]
```

<a name="ex6"></a>**Toggle `properties`' value with a button**
```handlebars
{{#filter-content content=busiestAirports properties=filterProperty as |fc|}}
  <button {{action "toggleFilterProperty"}}>Change filter type</button>
{{/filter-content}}
```
```javascript
filterProperty: Ember.computed('filterToggle', function() {
  return this.get('filterToggle') ? 'name.longForm' : 'name.code';
}),
filterToggle: true,
busiestAirports: [{
  name: {
    longForm: 'Hartsfield–Jackson Atlanta International Airport',
    code: 'ATL'
  },
  location: 'Atlanta, GA'
}],
actions: {
  toggleFilterProperty: function() {
    this.toggleProperty('filterToggle');
  }
}
```

<a name="ex7"></a>**Add a showing/filtered count**
```handlebars
{{#filter-content content=boardMembers properties="firstName" as |fc|}}
  <small>
    Showing {{fc.filteredContent.length}}/{{fc.content.length}} people matching:
    <strong>"{{fc.query}}"</strong>
  </small>
  {{! ... }}
{{/filter-content}}
```

<a name="ex8"></a>**Filter multiple components simultaneously**
```handlebars
{{! this input's value is set to a property on the controller }}
{{input value=sharedQuery}}

{{!--
  the following two filter-content components are configured as follows:
  - `showInput=false` disable the default filter query input
  - `query=sharedQuery` consume input from the external input field
--}}

{{#filter-content content=htmlColors.collectionA properties="name" query=sharedQuery showInput=false as |fc|}}
  {{! ... }}
{{/filter-content}}

{{#filter-content content=htmlColors.collectionB properties="name" query=sharedQuery showInput=false as |fc|}}
  {{! ... }}
{{/filter-content}}
```
```javascript
htmlColors: {
  collectionA: [{
    // ...
  }],
  collectionB: [{
    // ...
  }]
},
sharedQuery: ''
```

## Contributing

The more the merrier. **Please submit any PRs against** [__the__ `feature` __branch__](https://github.com/zakmac/ember-cli-filter-component/tree/feature)**.**

```shell
cd /path/to/projects
git clone git@github.com:zakmac/ember-cli-filter-component.git
```

--- 
<small>
For more information on using **ember-cli**, visit [http://www.ember-cli.com/](http://www.ember-cli.com/).<br>
For more information on **Ember.js**, visit [http://www.emberjs.com/](http://www.emberjs.com/).<br>
Looking for more great Ember addons? Check out [http://www.emberobserver.com/](http://www.emberobserver.com/).<br>
Check out the Ember.js IRC channel at `#emberjs` on **Freenode IRC** or join the [Ember Community Slack organization](https://ember-community-slackin.herokuapp.com/).
</small>
