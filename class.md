[[Typescript]] classes can be their own [[Type]]s
```
class AccountingDepartment extends Department {
	private lastReport: string;

	get mostRecentReport(){
	if (this.lastReport) {
		return this.lastReport//'get' mut always include a return
	}
	throw new Error ('No report found.')
}

set mostRecentReport(value: string) {
	if (!value) {
		throw new Error('No pass in a valid value')
	}
	this.addReport(value) //you add parenthesis because your're inside the class still. 
}

console.log(account.mostRecentReports) //you access mostRecentReports as a property, do not add parenthesis when calling. It does that behind the scenes

account.mostRecentReport = 'Year End Report' //Since we are wanting to add things to a class from outside the class component, we have to call it usign a parameter, an equal sign, and what you want to add to the class.
```

Lets say we want a method to create an employee to our class
```
//Inside the class
static fiscalYear = 2024

static createEmployee(name: string) {
	return { name: name };
}

//outside the class
let newEmployee = Depatmnet.createEmployee('Talie')
consolelog(newEmployee, Department.fiscalYear) //output = {Talie} 2024
```
Static Methods are detached from instances and cannot be used in constructors inside the class, since constructors are only used when instancing.

To access a static method from inside the class you must type for example:
`Department.fiscalYear`

```
//in class
describe() {
	console.log ('Accounting Department - ID: ' + this.id)
}
```

Abstract classes mean that you have to create subclasses that instance the data. Unlike how normal classes tell the subclasses what data to have. Each subclass must have the abstract functions that are mentioned in the main class. Abstract classes cant be instantiated theselves, you cant make a Department class, only sub-departments.
```
abstract class Department {
	static fiscalYear = 2024
	protected employes: string[] = []

	abstract describe(this: Department): void //this has no curly braces because it is abstract. Void means it isnt returning anything.
	
}

class ITDepartment extends Department {
	admins: sting[];
	constructor(id: string, adminsL strin[]) {
	super(id, 'IT')
	this.admins = admins;
	}

	describe() {
		console.log('IT DEpartment - ID ' + this.id)
	}
}
```

If you want to make sure that you only want to instance one class, for example you only want one accounting department, you can make the constructor private. If you do make a constructor private, you must create static methods to get the data outside of the class. 

A #StaticMethod is a method you call directly on a class, not on an object created based on it. 
For example; 
```
class AccountingDepartment extends Department {
	private lastReport: string;
	private static instance: AccountingDepartment; //Here you store an accounting department in one instance.
}

static getInstance() {
	if (AccountingDepartment.instance) {
		return this.instance;
	}
	this.instance = new AccountingDepartment('d2, []');
	return this.instance   //ID and empty reports array are instanced
	
}

private constructor(id:string, private reports: string[]) {
	super(id, 'Accounting');
	this.lastReport = reports[0];
}
```

