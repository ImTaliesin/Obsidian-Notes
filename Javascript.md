[[!jQuery]] [[html]] [[css]] [[NPM]]


```
function handleTimeout() {
console.log("Timed out!")
}

const handleTimeout = () => {
	console.log("Timed out!")
}

setTimeout(handletimeout, 2000);  
```

Call a function in another function
```
function greeter(parameterFunction){
	parameterFunction();
}

greeter(() => console.log("Hi"))
//This function is passed into the function above^
//output:"Hi"
```

Functions inside of functions
```
function init() {
	function greet() {
		console.log('Hi')
	}
	greet();
}
init()
```

#arrowFunction Arrow function syntax may look strange but it's actually simple.
```
 function callMe(name) { 
    console.log(name); 
    }
```
which you could also write as:
```
const callMe = function(name) { 
	console.log(name);
}
```
becomes: 
```
const callMe = (name) => { 
	console.log(name);
}
```
**Important:** 
When having **no arguments**, you have to use empty parentheses in the function declaration:
```
const callMe = () => { 
	console.log('Hi!');
 }
```
When having **exactly one argument**, you may omit the parentheses:
```
const callMe = name => {
console.log(name);
}
```