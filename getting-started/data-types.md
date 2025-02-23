# Data Types

Stage: 1 - Proposal

Postman Flows in its heart is a dataflow language, which means that there flows
understands all kinds of values and associates a type to data. It should also be
noted that Flows in a hybrid typed language, which means that it perform static 
type checking to provide features like auto-complete and early warning, and it 
also performs some dynamic type checking during execution. Our intention, in the long run, is to move Flows towards being
strictly typed language over a period of time. 

We are assuming most flow programmers would either be coming from a 
javascript/typescript background, or it might be their first time programming in 
an API-First language; therefore we have tried to keep the vocabulary of the 
types close to javascript/typescript.

## Primitive Values
| type                 | What it accepts                                           | Example                   |
| -------------------- | --------------------------------------------------------- | ------------------------- |
| bool                 | **true** or **false**                                     | `true`                    |
| string               | utf-8 encoded characters                                  | `foo 😎 bar`               |
| number               | double precision 64-bit values or 64bit unsigned integers | `3.14` <br> `4294967295`  |
| timestamp            | string containing RFC3339 timestamp                       | `1985-04-12T23:20:50.52Z` |
| null                 | Exactly one value **null**                                | `null`                    |
| regex                | A regular expression                                      | `/ab+c/`                  |
| symbol               | Symbol of various type                                    | `string` <br> `null`      |
| range (pre-proposal) | A range value                                             | `0..10`                   |

### Boolean
The boolean value can contain two symbols `true` or `false` as with any programming language. The Boolean type is represented by the symbol `bool`.

The *boolean* value gets automatically converted to a *number* or a *string* if required.

| value   | number | string    |
| ------- | ------ | --------- |
| `true`  | `1`    | `'true'`  |
| `false` | `0`    | `'false'` |

### String
The String type is used to represent textual data. It is a utf-8 encoded list of characters. Just like javascript, string in Flows are also immutable, i.e once a string is created, it cannot be modified. But you can always create new strings out of existing string using operators like *concat* or *substring*.

> 💡 Tip: JSON values are of type string. To make sense of that data they serialize needs perform step called parsing to can generate a complex data-structure of primitive types. The `Send Request` block performs the parsing automatically if the `content-type` header hints at a JSON body. In cases where this automatic parsing does not take place, one will need to parse the string using the `JSON Parse` block.

A *string* value cannot get converted automatically to any other type.

### Number
The number type in Flows can represent all kinds of numerical values, like Integers, Decimals, etc. Temporarily, the number internally are stored as javascript numbers, but this is going to change account for 64-bit integers and providing a unified number type.

A *number* value gets automatically converted to a *string* or *boolean* if required.

| value  | boolean | string   |
| ------ | ------- | -------- |
| `0`    | false   | `'0'`    |
| `3.14` | true    | `'3.14'` |

### Timestamp
Unlike javascript, Flows consider dates are first-class type and provide a data type for storing dates. Dates are stored in-accordance to [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339).

A *timestamp* value gets automatically converted to a *string* or *number* if required.

| value                     | string                      | number         |
| ------------------------- | --------------------------- | -------------- |
| `1985-04-12T23:20:50.52Z` | `'1985-04-12T23:20:50.52Z'` | `482196050520` |

### Null

A *null* value represent a reference to non-existent values. Generally, any datatype could contain a null value instead of their actual value. In an API-First world it is very common to find this occurrence. The only possible value for the null type is `null`.

Because this is so common, in order to prevent null pointer exceptions, Flow performs some deterministic automatic conversion for null value.

| value  | boolean | string | number | regex  | timestamp              |
| ------ | ------- | ------ | ------ | ------ | ---------------------- |
| `null` | `false` | `''`   | `0`    | `/$^/` | `0000-00-00T00:00:00Z` |

### Regex
Regular expressions are patterns which can be used to match character combinations in strings. Flows borrows Javascript regular expression implementation. [Read More on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions).

A *regex* value gets automatically converted to a *string* if required.

| value  | string   |
| ------ | -------- |
| `/$^/` | `'/$^/'` |

### Range (Pre-Proposal) - TBD
*Note: Ranges are still in pre-proposal stage and might not be implemented*

## Abstract Types

> All complex/abstract data-types in Flows are built over the concept of [Streams](https://en.wikipedia.org/wiki/Stream_(computing)) internally.

### List
The list data-type represents a stream of ordered values, where values of the same type
may occur more than once. You might be familiar with Arrays in javascript, Lists are like
arrays but with the constraint that all elements present in the list are of same type.

A typescript equivalent of List would be
```ts
const list : number[] = [1, 2, 3, 4, 5];
```
*A stream of values where all values have the same type*

### Tuple
The tuple data-type represent an ordered sequence of finite elements. The values for each
element of the tuple can be of different type. Unlike Lists, tuples cannot be an infinite stream,
they are of fixed size and their size must be known before hand.

A typescript equivalent of Tuple would be
```ts
const list : [string, number, boolean] = ['flows', 20, true];
```

*A fixed stream of values where values have same or different types*

### Record
A record is a collection of fields(key-value pair) where the key is of string type and the values
can be of different types. These field are fixed in number and follow a particular sequence. If you have some knowledge of C, you will know these are `struct`. 

A typescript equivalent of Record would be
```ts
interface Box {
  name: string;
  length: number;
  width: number;
  height: number;
  empty: boolean
}
```

*A stream of fields(key:value) where key is string and values have same or different types*

### Map / Dictionary

A map is a collection of fields where the key can be a value of a primitive type and 
corresponding value can be of any data-type. The constraint here being that all fields 
must have keys and values of same type.

A typescript equivalent of Map would be
```ts
type map: {[key: string]: number} = {'a': 1, 'b': 2, 'c': 3};
```

*A stream of fields(key:value) where keys are of same type and values are of same type*