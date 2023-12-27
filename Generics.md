Flexible & reusable code [[Type]] [[Typescript]]

```
const names: Array<string> = []; //this is the same thing as string[]

const promise: Promise:<string> = new Promise((resolve, reject) => {
	setTimeout(() =>{
		resolve('This is done!')
	}, 2000)
});

function merge<T extends object, U extends object>(objectA: T, objectB: U) {
	return Object.assign(objectA, objectB);
}

const mergedObject = merge({name: 'Talie'}, {age: 30})
console.log(mergedObject)
```