[[NodeJS]] #NPM i react
React is a [[Javascript]] framework.

Every component updates asynchronously from each other component.

Vocab words:
#jsx #props #styling #declarative_Programming #Components #Virtural_Dom #State #Babel #Container #Hooks #useState #Destructuring #Spread_Operator #MapAndFilter #import #expert

`ReactDOM.render(what, where);
`ReactDOM.render(<h1>Hello</h1>
``,document.getElementById('root'));


#Components Component example
```
function Card(props){
return(
	<div>
		<h2>{props.name}</h2>
		<img src={props.img} alt="avatar_img" />
		<p>{props.email}</p>
	</div>
)
}
```
```
<Card
	name="TempName"
	img="URL_HERE"
	tel="(888) 888-888"
	email="Tempemail@gmail.com"
	/>
```

#props Is short for the properties of an object with multiple key value pairs.  props is the object that is sent over

Known for its [[DOM manipulation methods]] and takes a #declarative approach