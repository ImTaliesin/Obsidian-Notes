[[Typescript]][[class]][[Type]]

Decorators are functions that apply to classes.
```
function Logger(constructor: Function) {
	console.log('Logging...');
	console.log(constructor) //This prints the entire constructor function and class
}

@Logger //this will execute the decorator code when the class is found by javascript. So only once.
class ___
	(class code)
```

```
function Logger(logString: string) {
	return function(constructor:Function) {
	console.log(logString); //This prints what ever parameter is inputted by the function call when the class is found.
	console.log(constructor) //This prints the entire constructor function and class;
	}
}

@Logger('LOGGING - PERSON')
class Person {
	name='Talie'
}
```

This code can render HTML code on the screen just by a class decorator.
```
function WithTemplate(template: string, hookID: string){
	return function(_: Function) {
	const hookElement: = document.getElementById(hookID)
	if (hookElement) {
		hookElement.innerHTML=template;
		}
	}
}

@WithTemplate('<h1>My Person Object</h1>', 'app')
class Person {
	name='Talie'

	constructor() {
		console.log('Creating person object...')
	}
}
```

#property #decorators

