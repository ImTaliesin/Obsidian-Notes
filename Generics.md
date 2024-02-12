Flexible & reusable code [[Type]] [[Typescript]]

```
const names: Array<string> = []; //this is the same thing as string[]

function merge<T extends object, U extends object>(objectA: T, objectB: U) {
	return Object.assign(objectA, objectB);
}

const mergedObject = merge({name: 'Talie'}, {age: 24})
console.log(mergedObject)
```