# Filters
[![Build Status](https://travis-ci.org/apastors/jqfilters.png?branch=master)](https://travis-ci.org/apastors/jqfilters)

`jqfilters` allows to easily create filter objects, which can be applied to JSON-like dictionaries.
It is based on the popular JSON parsing tool [jq](https://stedolan.github.io/jq/manual/), so the
syntax mimics jq's.

For showing both, the same JSON object will be used:

```python
>>> person = {
...     "name": "Alice"
...     "books": [
...         {"name": "The name of the wind"},
...         {"name": "A feast for crows"},
...         {"name": "Continuous delivery"}
...     ],
...     "age": 24,
...     "location": "Lancashire"
... }
```

## Simple Queries
We will create a filter that will return ``True`` for people living in Yorkshire.

```python
>>> from jqfilters import Filter
>>> is_from_yorkshire = Filter(op1='.location', operator='eq', op2='Yorshire')
>>> is_from_yorkshire(person)
False
```

We can also apply some transformation to operands (see `jqfilters.operations`):

```python
>>> from jqfilters import Filter
>>> reads_a_lot = Filter(op1='.books', transform1='len', operator='ge', op2=3)
>>> reads_a_lot(person)
True
```

Complex Queries
---------------
We will create a filter that will return ``True`` when any of the book name is "A game of thrones"
or when the person is older than 18. As this specification is more complex, the
:meth:`fromConfig <jqfilters.filters.Filter.fromConfig>` method will be used.

```python
>>> specs = {
...     "op1": {
...         "op1": ".books[].name",
...         "operator": "contains",
...         "op2": "A game of thrones"
...     },
...     "operator": "or",
...     "op2": {
...         "op1": ".age",
...         "operator": "ge",
...         "op2": 18
...     }
... }
>>> adult_got_fans = Filter.fromConfig(specs)
>>> adult_got_fans(person)
True
```

A filter can be inspected in an easier way by just prompting it:

```python
>>> adult_got_fans
>>> ((.books[].name - contains - A game of thrones) - or - (.age - ge - 18))
```
