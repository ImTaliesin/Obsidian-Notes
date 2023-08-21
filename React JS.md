[[NodeJS]] #NPM i react
React is a [[Javascript]] framework.

Every #component updates asynchronously from each other component.

"Lifting the state up means that it moves data from a child component to a parent component"

Vocab words:
#jsx #props #styling #declarative_Programming #Virtural_Dom #State #Babel #Container #Hooks #useState #Destructuring #Spread_Operator #MapAndFilter #import #expert

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

<<<<<<< Updated upstream
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

render from an array
```
	const expenses = [
		{
			id: 'e1',
			title: 'Toilet Paper',
			amount: 94.12,
			date: new Date(2020, 7, 14),
		},
		{
			id: 'e2',
			title: 'Taco Bell',
			amount: 6969.69,
			date: new Date(2021, 2, 12),
		},
		{
			id: 'e3',
			title: 'Car Insurance',
			amount: 294.67,
			date: new Date(2021, 2, 28),
		},
		{
			id: 'e4',
			title: 'New Desk (Wooden)',
			amount: 450,
			date: new Date(2021, 5, 12),
		},
	];
				<ExpenseItem
                    title={props.items[0].title}
                    amount={props.items[0].amount}
                    date={props.items[0].date} />
                <ExpenseItem
                    title={props.items[1].title}
                    amount={props.items[1].amount}
                    date={props.items[1].date} />
                <ExpenseItem
                    title={props.items[2].title}
                    amount={props.items[2].amount}
                    date={props.items[2].date} />
                <ExpenseItem
                    title={props.items[3].title}
                    amount={props.items[3].amount}
                    date={props.items[3].date} 
                />
```

A #key is a prop you can add to any component to help react identify each item. Key needs to be a unique value. 

#ControlledComponents  are those in which form’s data is handled by the component’s state. It takes its current value through props and makes changes through callbacks like onClick,onChange, etc. A parent component manages its own state and passes the new values as props to the controlled component.