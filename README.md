![](./logo.jpg)<br/>
**Overload** is not only a standalone library which to mimic function overloading for JavaScript.

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
&emsp;[Destructuring as ES6](destructuring-as-es6)<br/>
&emsp;&emsp;[Object Destructuring](object-destructuring)<br/>
&emsp;&emsp;[Array Destructuring](array-destructuring)<br/>
[3. Configuration](#configuration)<br/>
&emsp;[Define Type Predication](#define-type-predication)<br/>
[4. API](#api)<br/>
&nbsp;[Constructor](#constructor)<br/>
&nbsp;[Function Properties](#function-properties)<br/>
&nbsp;[Overload$Proxy Instance Methods](#constructor)<br/>
[5. Ref](#ref)<br/>
[6. Change Log](#change-log)<br/>
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
var params = Overload.params("{key::String, value::String}")
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
var params = Overload.params("{key::String, value='Overload.js'}")
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
#### Destructuring as ES6
##### Object Destructuring
```
var foo1 = Overload.destruct("{x,y}", "test", function(arg1, arg2){
  console.log("x:" + x + ";y:" + y)
  console.log("arg1.x:" + arg1.x + ";arg1.y:" + arg1.y + ";arg1.z:" + arg1.z)
  console.log("arg2:" +  arg2)
}).seal()
foo1({x:1,y:2,z:3}, "test")
// display
// "x:1;y:2"
// "arg1.x:1;arg1.y:2;arg1.z:3"
// "arg2:test"
foo1({x:1,z:3}, "test")
// display
// "x:1;y:undefined"
// "arg1.x:1;arg1.y:undefined;arg1.z:3"
// "arg2:test"

var foo2 = Overload.destruct("{x = 1,y}", function(arg1){
  console.log("x:" + x + ";y:" + y)
}).seal()
foo2()      // display "x:1;y:undefined"
foo2({})    // display "x:1;y:undefined"
foo2({y:2}) // display "x:1;y:2"

var foo3 = Overload.destruct("{x:z, y:obj.d}", function(arg1){
  try{
    console.log("x:" + x)
  }
  catch(e){
    console.log(e.message)
  }
  console.log("z:" + z)
  console.log("obj:" + JSON.stringify(obj))
}).seal()
foo3({x:1,y:3})
// display
// "throw error: x is not defined"
// "z:1"
// "obj:{d:3}"

var foo4 = Overload.destruct("{x, y} = {x:1,y:2}", function(arg1){
  console.log("x:" + x + ";y:" + y)
}).seal()
foo3({}) // display "x:undefined;y:undefined"
foo3()   // display "x:1;y:2"
```
##### Array Destructuring
```
var bar1 = Overload.destruct("[x, y]", function(){
  console.log("x:" + x + ";y:" + y)
}).seal()
bar1([1,2]) // display "x:1;y:2"
bar1([1])   // display "x:1;y:undefined"
bar1([])    // display "x:undefined;y:undefined"

var bar2 = Overload.destruct("[x, y = 10]", function(){
  console.log("x:" + x + ";y:" + y)
}).seal()
bar2([1,2]) // display "x:1;y:2"
bar2([1])   // display "x:1;y:10"
bar2([])    // display "x:undefined;y:10"

var bar3 = Overload.destruct("[, , y = 10]", function(){
  console.log("y:" + y)
}).seal()
bar3([1,2])   // display "y:10"
bar3([1,2,3]) // display "y:3"
bar3()        // display "y:10"

var bar4 = Overload.destruct("[, , ...y]", function(){
  console.log("y:" + JSON.stringify(y))
}).seal()
bar4([1,2,3,4,5])   // display "y:[3,4,5]"
bar4([1,2])         // display "y:[]"
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
#### `Overload.route(formalParamDefinition, overloadedFunction)`
**@description** Bind overloaded function to specific formal parameter definition.<br/>
**@param** {[[String|Function|POJO|Number]...|Overload$Params]} [formalParamDefinition] - formal parameter definition<br/>
**@param** {Function} overloadedFunction - overloaded function<br/>
**@returns** {Overload$Proxy} - Overload$Proxy instance<br/>
#### `Overload.destruct(formalParamDefinition, overloadedFunction)`
**@description** Bind overloaded function to specific formal parameter definition.<br/>
**@param** {[[String|Function|POJO|Number]...|Overload$Params]} [formalParamDefinition] - formal parameter definition<br/>
**@param** {Function} overloadedFunction - overloaded function<br/>
**@returns** {Overload$Proxy} - Overload$Proxy instance<br/>
#### `Overload.defType(type, typePredication)`
**@description** Define predication of user-defined type.<br/>
**@param** {String|Object.<type:typePredication>} type - user-defined type<br/>
**@param** {Function} [typePredication] - predication of user-defined type<br/>
**@returns** {Overload} - Overload instance<br/>
#### `Overload.isType(val, type)`
**@description** Predicate the type of val is the same as argument type whether or not.<br/>
**@param** {Any} val - value<br/>
**@param** {String} type - data type<br/>
**@returns** {Boolean}<br/>
#### `Overload.config(key, val)`
**@description** Global configuration<br/>
**@param** {String|Object.<key:val>} key<br/>
**@param** {Any} [val]<br/>
**@returns** {Overload}<br/>
```
Overload.config({
  /*
   * indicate when there are multiple best matching what would be done
   * error (default value) throw error.
   * info                  print infomation on console.
   */
  multiMatch: "error",
  /*
   * indicate when there are syntax bugs in the pattern string what would be done
   * error (default value) throw error.
   * info                  print infomation on console, then ignore the invalid routing.
   */
  patternParserMode: "error",
  /* 
   * mode of argument destructuring 
   * fast (default value) would set the properties of global object(or window) named by pattern temporarily,
   *                      more efficient but would bury some pigfalls.
   * safe                 would reflect the original function by eval or Function at definition time,
   *                      safe but inefficient.
   */
  destructMode: "fast"
})
```
### Overload$Proxy Instance Methods
#### `Overload$Proxy#route(formalParamDefinition, overloadedFunction)`
**@description** Bind overloaded function to specific formal parameter definition.<br/>
**@param** {[[String|Function|POJO|Number]...|Overload$Params]} [formalParamDefinition] - formal parameter definition<br/>
**@param** {Function} overloadedFunction - overloaded function<br/>
**@returns** {Overload$Proxy} - Overload$Proxy instance<br/>
#### `Overload$Proxy#destruct(formalParamDefinition, overloadedFunction)`
**@description** Bind overloaded function to specific formal parameter definition.<br/>
**@param** {[[String|Function|POJO|Number]...|Overload$Params]} [formalParamDefinition] - formal parameter definition<br/>
**@param** {Function} overloadedFunction - overloaded function<br/>
**@returns** {Overload$Proxy} - Overload$Proxy instance<br/>
#### `Overload$Proxy#seal()`
**@description** Return a pure overloaded function entry.<br/>
**@returns** {Function} - overloaded function entry<br/>

## Ref
[nathggns/Overload](https://github.com/nathggns/Overload.git)<br/>
[JosephClay/overload-js](https://github.com/JosephClay/overload-js.git)<br/>
[Moeriki/overload-js](https://github.com/Moeriki/overload-js.git)<br/>
[ES6-变量的解构赋值](http://es6.ruanyifeng.com/#docs/destructuring)<br/>
[ES6-函数的扩展](http://es6.ruanyifeng.com/#docs/function)<br/>

## Change log
