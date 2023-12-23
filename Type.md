[[Typescript]] types, very similar to [[Interface]]

Here is an example of custom  #Type , #Intersecting, and #Typeguard
```
type Admin = {
	name: string;
	privileges: string[];
}

type Employee = {
	name: string;
	startDate: Date;
}

type ElevatedEmployee = Admin & Employee 

const e1: ElevatedEmployee = {
	name: 'Talie'
	privileges: ['create-server'],
	startDate: new Date;
}

type Combinable = string | number;
type Numberic = number | boolean

type Universal = Combinable & Numeric;

//Typeguard
function add(a: Combinable, b: Combinable) {
	if (typeof a === 'string || typeof  === 'string') {
		return a.toString() + b.toString();
	}
	return a + b;
}

type UnknownEmployee = Employee | Admin;

function printEmployeeInformation)emp: UnknownEmployee {
	console.log('Name' + emp.name);
	if ('privileges' in emp) {
		console.log('Privileges' + emp.privileges);
	}
	if ('startDate' in emp) {
		console.log('Start date: ' + emp.startDate);
}

printEmployeeInformation({e1})
```

more #Typeguard 
```
const userInputElement = document.getElementById('user-input')! as HTMLInputElement;

InputElement.value = 'Hello there'
```