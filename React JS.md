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

React takes a declarative approach, you define the desired target state and react does the heavy lifting. Unlike vanilla JavaScript that requires you to everything step-by-step.

Look at [[JSX]]
#props Is short for the properties of an object with multiple key value pairs.  props is the object that is sent over

Known for its [[DOM manipulation methods]] and takes a #declarative approach

```
const [enteredTitle, setEnteredTitle] = useState('');

const titleChangeHandler = (event) => {
      setEnteredTitle(event.target.value);
   };

<div className="new-expense__control">
               <label>Title</label>
               <input type="text" onChange={titleChangeHandler} />
   };
```

Check if an input is 4 characters long or not
```
export default function App() {
    const [messageValidity, setMessageValidity] = React.useState('Invalid');
    //This sets the usestate as invalid as default
    
    function messageChangeHandler(event) {
        const value = event.target.value;
        if (value.trim().length < 4) {
            setMessageValidity('Invalid')
        } else {
            setMessageValidity('Valid');
        }
    }
    
    return (
        <form>
            <label>Your message</label>
            <input type="text" onChange={messageChangeHandler} />
            <p>{messageValidity} message</p>
        </form>
    );
}
```

#ChangeHandler example
```
//the parameters inside parenthesis can be whatever you decide it to be
export default function ExpenseForm() {
   const inputChangeHander = (identifier , value) => {
      if (identifier === 'title'){
         setEnteredTitle(value)
      } else if (identifier ==='date') {
         setEnteredDate(value)
      } else {
         setEnteredAmount(value);
      }
   }
```

Another way:
```
export default function ExpenseForm() {
   const [enteredTitle, setEnteredTitle] = useState('');
   const [enteredAmount, setEnteredAmount] = useState('');
   const [enteredDate, setEnteredDate] = useState('');

   const titleChangeHandler = (event) => {
      setEnteredTitle(event.target.value);
   };

   const amountChangeHandler = (event) => {
      setEnteredAmount(event.target.value);
   };

   const dateChangeHandler = (event) => {
      setEnteredDate(event.target.value);
   };

   return (
      <form>
         <div className="new-expense__controls">
            <div className="new-expense__control">
               <label>Title</label>
               <input type="text" onChange={titleChangeHandler} />
            </div>
            <div className="new-expense__control">
               <label>Amount</label>
               <input type="number" min='.01' step='.01' onChange={amountChangeHandler}/>
            </div>
            <div className="new-expense__control">
               <label>Date</label>
               <input type="date" min="2023-01-01" max='2025-12-31' onChange={dateChangeHandler}/>
            </div>
           </div>
            <div className="new-expense__actions">
            <button type="submit">Add Expense</button>
         </div>
      </form>
   );
```

How to make page not default on a submit.
```
   const submitHandler = (event) => {
   	event.preventDefault();
   };
````
