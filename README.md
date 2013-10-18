```
  __                               
_/  |_____________    ____   ______
\   __\_  __ \__  \  /    \ /  ___/
 |  |  |  | \// __ \|   |  \\___ \ 
 |__|  |__|  (____  /___|  /____  >
                  \/     \/     \/ 
```

*The ultimate object transformer*

## Overview

####The purpose of trans is to make it super easy to transform complex json objects
  
Trans allows specifying composite field names such as ``a.b.c`` and it does the 
right thing even across multiple arrays.  
For example, the field above could be used to modify or extract a value from an object 
that looks like this

``` javascript 
{ a: { b: { c: 1 } } }
```
but also if the object looks like this 
``` javascript
{ a: [ { b: { c: 1 } }, { b: { c: 2 } } ] }
```
or like this
``` javascript
[ { a: { b: [ { c: 1 }, { c: 2 } ] } } ]
```

There are three types of transformation methods:  
- ``map(*transformers)`` transforms the entire object
- ``mapf(field, *transformers)``  transforms a particular field
- ``mapff(source, destination, *transformers)`` takes the value of one field, transforms it, and sets it on another field

The transformers specified as parameters to the transformation methods can be functions, 
field names, or even objects (which will be used as hash maps). The functions that are not properties
on the object being transformed are assumed to take that object as the first parameter. But they can 
take additional parameters as well. In those case the function should be specified as an array. 
The examples below are identical in outcome:
``` javascript
trans({ a: [ 1, 2 ] }).mapf('a', 'length', [add, 5], [mul, 10]);
```
``` javascript
trans({ a: [ 1, 2 ] }).mapf('a', function(obj) {
    return mul(add(obj.length, 5), 10);
});
```
The result in both cases is ``{ a: 70 }``

## Quickstart

Using trans is easy. First wrap the data to be transformed by calling ``trans(data)``,  
as below, and then call transformation methods on the wrapper. Multiple transformation  
methods can be chained. When done call ``value()`` to get back the raw data that has been transformed.

Here's a quick taste. Assuming we have an object that looks like this  

``` javascript
var data = [ 
      { a: { b: 'abc' }, { c: 1 } }
    , { a: { b: 'ade' }, { c: 2 } }
    , { a: { b: 'def' }, { c: 3 } }
    , { a: { b: 'ghk' }, { c: 4 } } ];
```

We can use trans to group the data array by the first letter capitalized of the a.b field  
and set the group value to a.c as follows  

``` javascript

var trans = require('trans');
var value = trans(data)
    .group('a.b', 'a.c', ['charAt', 0], 'toUpperCase')
    .value();

```

Now value looks like this  

``` javascript
  [ { key; 'A', value: [ 1, 2 ] }, { key: 'D', value: [ 3 ] }, { key: 'G', value: [ 4 ] } ];
```

## Methods