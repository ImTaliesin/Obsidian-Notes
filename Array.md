```
const hobbies = ["Sports", "Cooking", "Gaming"]
const index = hobbies.findIndex((item)) => item === "Sports");
//true , index is [0]

cosnt editedHobbies = hobbies.map((item) => item + '!')
//["Sports!", "Cooking!", "Gaming!"]

```

You can turn the output into a [[Javascript]] object #KeyValuePair too by doing this:
```
function transformToObjects(numberArray) {
    return numberArray.map(item => {
    return {val: item}
});
}

//input: 1, 2, 3
//output: [{val: 1}, {val: 2}, {val: 3}]
```

```
function storeOrder({id, currency}) {
localStorage.setItem('id', id);
localStorage.setItem('currency', currency);
}
```

If you want to merge two arrays together use the #Spread_Operator which is: ...
`const mergedhobbies = [...hobbies, ...newHobbies]

```
const user = {
	name: "Talie"
	age: 23
}

const adminUser = {
	isAdmin: true,
	...user
}
console.log(adminUser)
	//isAdmin: true
	//name: "Talie"
	//age: 23
```

-The **`map()`** method **creates a new array** populated with the results of calling a provided function on every element in the calling array.
- `find()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)
- `findIndex()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)
- `filter()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
- `reduce()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce?v=b](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce?v=b)
- `concat()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat?v=b](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat?v=b)
- `slice()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
- `splice()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)

```
import Todo from './Todo';
const DUMMY_TODOS = [
    'Learn React',
    'Practice React',
    'Profit!'
];
 
// don't change the Component name "App"
export default function App() {
    return (
        <ul>
          {DUMMY_TODOS.map(todo => 
          <Todo words={todo} 
          />
          )}
        </ul>
    );
}

---------------
export default function Todo(props) {
    return <li>{props.words}</li>;
}
```

#Destructure an array
```
const [firstName, lastName] = ["Talie", "Sin"];
console.log(firstName)
```
#Destructure an javascript item. The variable names have to be the same as the item names inside
```
const {name, age} = {
	name: "Taliesin"
	age: 23
}
```
Alternatively if you didnt want to have to use the item name, you can assign it like this:
```
const {name: userName, age} = {
	name: "Taliesin"
	age: 23
}
//console.log(userName)
//output: Taliesin
