#### Primitive types

uint32
bool
byte
...

$TYPEDEF can be primitive type or user defined type (ID)

#### Ident

{
"type": "ident",
"name": ID,
"def": $TYPEDEF
}

#### Selector

{
"type": "selector",
"name": ID,
"prefix": $TYPEDEF,
"item": ID
}

#### Parenthesis

{
"type": "parenthesis",
"def": $TYPEDEF
}

This can be omitted if the tree-like structure is kept

#### Channel

{
"type": "channel",
"name": ID,
"dir": "1|2|3",
"value": $TYPEDEF
}

#### Slice

{
"type": "slice",
"name": ID,	# not empty if directly used as a struct's field type
"elmtype": $TYPEDEF
}

#### Array

{
"type": "array",
"name": ID,
"elmtype": $TYPEDEF
}

#### Map

{
"type": "map",
"name": ID,
"def" {
    "keytype": $TYPEDEF,
    "valuetype": $TYPEDEF
}
}

New

{
"type": "map",
"name": ID,
"keytype": $TYPEDEF,
"valuetype": $TYPEDEF
}

#### Pointer

{
"type": "pointer",
"name": ID,
"def": $TYPEDEF
}

#### Ellipses:

{
"type": ellipsis",
"elt": $TYPEDEF
}

new

{
"type": ellipsis",
"def": $TYPEDEF
}


#### Interface

{
"type": "interface",
"name": ID,
"def": [
list of methods or interfaces    
]
} 

- interface consists of an array of methods or interfaces

#### Method

{
"type": "method",
"name": ID,
"def": {
    "type": "func",
    "params": [$TYPEDEF, $TYPEDEF, ...],
    "results": [$TYPEDEF, $TYPEDEF, ...]
}
}

New

{
"type": "method",
"name": ID,
"params": [$TYPEDEF, $TYPEDEF, ...],
"results": [$TYPEDEF, $TYPEDEF, ...]
}


#### Function

{
"type": "func",
"params": [$TYPEDEF, $TYPEDEF, ...],
"results": [$TYPEDEF, $TYPEDEF, ...],
"recv": $TYPEDEF	# can be empty, if non-empty => always one type
}

#### Struct

{
"type": "struct",
"name": ID,
"def": [
    {
    "name": ID,
    "def": $TYPEDEF
    }
]
}

Lets keep the "def" key in each field otherwise we duplicate the schema for each data type.

## INFO

- Each type definition starts with type ID (its name)
- Each field can be named (or anonymous with empty name)
- Method is only a part of an interface

### Identical structures

- ident:	type, name, def
- selector:	type, name, prefix, item
- channel:	type, name, dir, value
- slice:	type, name, elmtype
- array:	type, name, elmtype
- map:		type, name, keytype, valuetype
- pointer:	type, name, def
- ellipses:	type, elt
- method:	type, name, params[types], results[types]
- function:	type, name, params[types], results[types], recv(type)
- interface:	type, name, def
- struct:	type, name, def[(name, def)]

In which cases is name non-empty and empty?
Name is non-empty only if it is a field of a struct, method of an interface or start of a definition of a type.
Ellipses can not start a definition, field or method. It is used in arguments only to allowed variable number of arguments.

To get rid of the anynymous and named definition, what about to put the name one level higher? I.e.

{
	"
}

Each data type will start with:
{
"name": ID,
"def": $TYPEDEF
}

{ "$ref": "#/definitions/identifier" },
{ "$ref": "#/definitions/selector" },
{ "$ref": "#/definitions/channel" },
{ "$ref": "#/definitions/slice" },
{ "$ref": "#/definitions/array" },
{ "$ref": "#/definitions/map" },
{ "$ref": "#/definitions/pointer" },
{ "$ref": "#/definitions/ellipses" },
{ "$ref": "#/definitions/function" },
{ "$ref": "#/definitions/method" },
{ "$ref": "#/definitions/interface" },
{ "$ref": "#/definitions/struct" }
