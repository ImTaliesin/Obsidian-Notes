[[Typescript]] interface is similar to a custom #type but seems clearer because you want to define a custom object as such. You can implement interfaces in classes. Interfaces can be used as a contract that a [[class]] must adhere to.

Interfaces are often used to share functionality amongst different classes. you can use an interface as a type on a const or variable that stores a class type that is based on the interface type.

You can set an interface to readonly, which means once its set, it cant be changed, basically an implicit const.
```
interface Greetable {
    name: string;

    greet(phrase: string): void;
}

class Person implements Greetable {
	name: string
	age = 30
	
	constructor(n:string) {
		this.name = n
	}

	greet(){
		console.log(phrase + ' ' + this.name)
	}
}

let user1: Greetable;

user1 = new Person('Max')
    
user1.greet('Hi there - I am');
```

