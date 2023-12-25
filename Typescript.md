[[Javascript]] [[React JS]] 

Typescript has trouble understanding [[html]] types and often needs bypassing

Compiler options, best default "Lib" section is : [
	"es6"
]]

set "outDir" to "./dist"
set "rootDir" to ""./src"
set "strictNullChecks": false if you don't want to use the ! to tell typescript that a null will not occur
set sourcemap to true for debugging

Function #overloading 
```
function add (a: string, b: string:): string;
function add (a: number, b: number:): number;
function add (a: Combinable, b: Combinable:){
	....................
}
```

#Optional_Chaining 
```
const fetchedUserData = {
	id = 'u1',
	name = 'Max'
	job = { title: 'CEO', description: 'My own company'}
}

console.log(fetchedUserData?.job?.title); //The function checks to see if user data exists, if so check if job exists, if so get the title.
```

```

const userInput = null;
const storedData = userInput ?? DEFAULT //Double question marks means if data is null, then do ->
```