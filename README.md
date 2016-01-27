![](./logo.jpg)<br/>
**Overload** is a standalone library which to mimic function overloading for JavaScript.

### In This Documentation
[1. Basic Usage](#basic-usage)<br/>
&emsp;[Create Overload$Params Instance](#create-overloadparams-instance)<br/>
&emsp;[Define Formal Parameters by Exact Arity](#define-formal-parameters-by-exact-arity)<br/>
&emsp;[Define Formal Parameters by String](#define-formal-parameters-by-string)<br/>
&emsp;[Define Formal Parameters by Structure](#define-formal-parameters-by-structure)<br/>
&emsp;[Define Formal Parameters by User-defined Function](#define-formal-parameters-by-user-defined-function)<br/>
[2. Advanced Usage](#advanced-usage)<br/>
&emsp;[Existing Pattern-match](#existing-pattern-match)<br/>
&emsp;[Type of Property/Element Pattern-match](#type-of-propertyelement-pattern-match)<br/>
&emsp;[Default Value of Property/Elemen Pattern-match](default-value-of-propertyelement-pattern-match)<br/>
[3. Configuration](#configuration)<br/>
&emsp;[Define Type Predication](#define-type-predication)<br/>
[4. API](#api)<br/>
&nbsp;[Constructor](#constructor)<br/>
&emsp;[Overload()](#Overload)<br/>
&nbsp;[Function Properties](#function-properties)<br/>
&nbsp;[Overload$Proxy Instance Methods](#constructor)<br/>
[5. Change Log](#change-log)<br/>
## Basic Usage 
Overload functions or constructors.<br/>
```
var Foo = Overload()
Foo.prototype.getName = function(){return this.name}
Foo.prototype.getAge = function(){return this.age}

Foo.route("String", "Number", function(name, age){
  this.name = name
  this.age = age

  return "name:" + name + ";age:" + age // would be ignore when Foo called by new operator
})
Foo.route("String", function(name){
  this.name = name
  this.age = 18

  return "name:" + name + ";age:" + age // would be ignore when Foo called by new operator
})
Foo.seal()

/* function overloading */
Foo() // throw Error
Foo("fsjohnhuang", 12) // return "name:fsjohnhuang;age:12"
Foo("fsjohnhuang") // return "name:fsjohnhuang;age:18"

/* constructor overloading */
var f = new Foo() // throw Error
var f = new Foo("fsjohnhuang", 12) 
f.name                           // return "fsjohnhuang"
f.age                            // return 12
f.getName()                      // return "fsjohnhuang"
f.getAge()                       // return 12
var f = new Foo("fsjohnhuang") 
f.name                           // return "fsjohnhuang"
f.age                            // return 18
f.getName()                      // return "fsjohnhuang"
f.getAge()                       // return 18
```
#### Create Overload$Params Instance
```
var params1 = Overload.params("Number", "String")
var params2 = Overload.params(3)
var foo = Overload
	    .route(params1, function(){console.log("foo-param1")})
	    .route(params2, function(){console.log("foo-param2")})
var bar = Overload
	    .route(params1, function(){console.log("bar-param1")})
	    .route(params2, function(){console.log("bar-param2")})

foo(1,"")   // display "foo-param1"
foo(1,"",3) // display "foo-param2"
bar(1,"")   // display "bar-param1"
bar(1,"",3) // display "bar-param2"
```
#### Define Formal Parameters by Exact Arity
```
var params0 = Overload.params()
var params4 = Overload.params(4)
```
#### Define Formal Parameters by String
```
var paramsWithType = Overload.params("Number", "String", "RegExp")
var paramsWithDefaultValue = Overload.params("Number", "String", "RegExp=/\\d+/i")
var paramsWithEllipsis = Overload.params("Number", "String", "...")
var paramsWithTypedEllipsis = Overload.params("Number", "String", "String...")
```
#### Define Formal Parameters by Structure
```
var paramsWithType = Overload.params(Number, String, RegExp)
var paramsWithDefaultValue = Overload.params(Number, String, {type:RegExp, val:/\d+/i})
var paramsWithEllipsis = Overload.params(Number, String, {type:String, isParams: true})
```
#### Define Formal Parameters by User-defined Function
```
var params = Overload.params(function(arg){
                          return arg instanceof jQuery
			},
			function(arg){
			  return arg.age > 10
			})
```
## Advanced Usage 
We can define formal parameters by pattern-match.<br/>
#### Existing Pattern-match
```
var params = Overload.params("{key, value}")
var foo = Overload.route(params, function(keyValuePair){
  console.log(keyValuePair.key + ": " + keyValuePair.value)
})
foo({key: "name", value: "Overload.js"}) // display "name: Overload.js"
foo({key: "name"})                       // throw Error
```
```
var params = Overload.params("[,,]")
var foo = Overload.route(params, function(coll){
  console.log(coll[2])
})
foo([1,2,3]) // display "3"
foo([1])     // throw Error
```
#### Type of Property/Element Pattern-match
```
var params = Overload.params("{key:String, value:String}")
var foo = Overload.route(params, function(keyValuePair){
  console.log(keyValuePair.key + ": " + keyValuePair.value)
})
foo({key: "name", value: "Overload.js"}) // display "name: Overload.js"
foo({key: "name", value: 1})             // throw Error
```
```
var params = Overload.params("[String,Number]")
var foo = Overload.route(params, function(coll){
  console.log(coll[0] + ":" + coll[1])
})
foo(["str", 2]) // display "str:2"
foo([1, "str"]) // throw Error
```
#### Default Value of Property/Element Pattern-match
```
var params = Overload.params("{key:String, value='Overload.js'}")
var foo = Overload.route(params, function(keyValuePair){
  console.log(keyValuePair.key + ": " + keyValuePair.value)
})
foo({key: "name", value: 123}) // display "name: 123"
foo({key: "name"})             // display "name: Overload.js"
foo({value: 1})                // throw Error
```
```
var params = Overload.params("[String,=12]")
var foo = Overload.route(params, function(coll){
  console.log(coll[0] + ":" + coll[1])
})
foo(["str", "val"]) // display "str:val"
foo(["str", undefined]) // display "str:12"
foo(["str", null]) // display "str:12"
foo([1, "str"]) // throw Error
```
## Configuration
#### Define Type Predication
```
Overload.defType('$', function(o){
  return o instanceof jQuery
})
// usage
Overload.isType($(), '$') // display "true"
```
## API
### Constructor
#### `Overload()`
**@description** Create new Overload object<br/>
**@returns** {Overload$Proxy} - The Overload$Proxy object<br/>
```
var foo = Overload()
```

### Function Properties
#### `Overload.params(formalParamDefinition)`
**@description** Define formal parameters.<br/>
**@param** {[String|Function|POJO|Number]...} [formalParamDefinition] - formal parameter definition, default value is 0<br/>
**@returns** {Overload$Params} - FormalParameterDefinition instance<br/>
```
```
#### `Overload.route(formalParamDefinition, overloadedFunction)`
**@description** Bind overloaded function to specific formal parameter definition.<br/>
**@param** {[[String|Function|POJO|Number]...|Overload$Params]} [formalParamDefinition] - formal parameter definition<br/>
**@param** {Function} overloadedFunction - overloaded function<br/>
**@returns** {Overload$Proxy} - Overload$Proxy instance<br/>
```
```
#### `Overload.defType(type, typePredication)`
**@description** Define predication of user-defined type.<br/>
**@param** {String} type - user-defined type<br/>
**@param** {Function} typePredication - predication of user-defined type<br/>
**@returns** {Overload} - Overload instance<br/>
```
```
#### `Overload.isType(val, type)`
**@description** Predicate the type of val is the same as argument type whether or not.<br/>
**@param** {Any} val - value<br/>
**@param** {String} type - data type<br/>
**@returns** {Boolean}<br/>
```
```
#### `Overload.config(key, val)`
**@description** Global configuration<br/>
**@param** {String} key<br/>
**@param** {Any} val<br/>
**@returns** {Overload}<br/>
```
```
### Overload$Proxy Instance Methods
#### `Overload$Proxy#route(formalParamDefinition, overloadedFunction)`
**@description** Bind overloaded function to specific formal parameter definition.<br/>
**@param** {[[String|Function|POJO|Number]...|Overload$Params]} [formalParamDefinition] - formal parameter definition<br/>
**@param** {Function} overloadedFunction - overloaded function<br/>
**@returns** {Overload$Proxy} - Overload$Proxy instance<br/>
```
```
#### `Overload$Proxy#seal()`
**@description** Return a pure overloaded function entry.<br/>
**@returns** {Function} - overloaded function entry<br/>
```
```

## Ref
[nathggns/Overload](https://github.com/nathggns/Overload.git)<br/>
[JosephClay/overload-js](https://github.com/JosephClay/overload-js.git)<br/>
[Moeriki/overload-js](https://github.com/Moeriki/overload-js.git)<br/>

## Change log
