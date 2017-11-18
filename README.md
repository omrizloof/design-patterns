# Dependency injection
### Document subjects
- What is a dependency, and how it affects us as programmers
- What is dependency injection, and the three form of the DI (dependency injection) design pattern
- What are DI frameworks

### Intro - what is a dependency
**A dependency is when one object uses the API of another object**

From a practical perspective, you can identify a dependency in your code whenever you use the **new**  keyword to instantiate an object.  In such a case, you are fully responsible for creating and properly configuring the new object.

### Case study 1: constructor initialization
Before we dive into the DI (Dependency Injection) design pattern, we must first understand why it is needed. consider the following code:
```
class Car {
	constructor() {
		this.mEngine = new BigEngine();
		this.mTires = Tires.getInstance();
		this.mDoors = new Doors()
	}
}
```

#### Case study 1: issues
The code has two main issues, which stem from the fact that it assigns needed dependencies to internal properties:
1. The constructor knows what class dependencies it contains.
2. The constructor knows how each of its dependencies is created.

#### Case study 1: issues implications
We now know that the constructor in this case study is fully responsible for the creation of its dependencies, but why is that bad? 

Letting the constructor know how to create all of its dependencies leads to code that is harder to maintain
* What happens if the Tires class evolves and its getInstance() requires a parameter? (the car class will break, and we will need to change the code in its constructor)
* What happens if you want to use a different class of engine for your car (instead of BigEngine)
* When you write tests for the Car class, you are at the mercy of its dependencies - What does Door depend upon? Will getting a new instance of Door make an asynchronous call to the server? If we would like to test how a car works with a broken BigEngine, how can we give it one?

To solve these issues, we will use the Dependency Injection design pattern.

### The Dependency Injection (DI) Design pattern
The goal of the DI design pattern is to create flexible classes, improving our ability to test and reuse our code.
There are three ways to implement the DI design pattern:
#### Constructor injection
Class dependencies are inserted into the class using the constructor arguments.

*example:*
```
class Car {
  		constructor(engine, tires, doors)  {
  		  this.engine = engine;
  		  this.tires = tires;
  		  this.doors = doors;
  		}
	}
```
#### Setter injection
Class dependencies are inserted using setter functions.

*example:*
```
class Car {
		private Engine mEngine;
		private Tire mTires;
		private Door[] mDoors;

		constructor()  {}

		set mEngine(engine) {
			this.mEngine = engine;
		}
		
		set mTires(tires) {
			this.mTires = tires;
		}
		
		set mDoors(doors) {
			this.mDoors = doors;
		}
	}
```
#### Interface injection
Class dependencies are inserted using an interface that explicitly defines the point where a dependency can be set.

*example:*
```
public interface InjectedEngine {
		public void injectEngine(engine);
}

public interface InjectedTires {
		public void injectTires(tires);
}

public interface InjectedDoors {
		public void injectDoors(doors);
}

class Car  implements InjectedEngine, InjectedTires, InjectedDoors {
		private Engine mEngine;
		private Tire mTires;
		private Door[] mDoors;

		constructor()  {}

		@Override
		public void injectEngine(engine) {
			this.mEngine = engine;
		}

		@Override
		public void injectTires(tires) {
			this.mTires = tires;
		}

		@Override
		public void injectDoors(doors) {
			this.mDoors = doors;
		}
	}
```

### Case study 2: constructor DI
Consider the following code:
```
class Car {
  		constructor(engine, tires, doors)  {
  		  this.engine = engine;
  		  this.tires = tires;
  		  this.doors = doors;
  		}
	}
```
In this case study, we moved the dependency creation out of the constructor, and changed the constructor function to expect all needed dependencies as arguments. The code no longer contains concrete implementations, as we moved the responsibility of creating those dependencies to a higher level.

#### Case study 2: notable improvments
After making the dependencies constructor arguments instead of obects created inside the constructor, we created code that is far easier to maintain:
* It is now easy to insert mock objects to test our car.
* it is now easy to create cars with different engines/tires/doors without having to change our car class.


#### Case study 2: issues
In this case study, the responsibility of creating and managing the constructor dependencies is moved to a higher level - whomever creates a car will be forced to supply its dependencies.
**Who takes care of assembling all those dependencies? We do.**
#### Case study 2: conclusions
Moving the dependencies to be  injected into the constructor using the constructor arguments improved code testablity and reusability, but we still have to provide the class dependencies ourselves.
### DI frameworks

Using DI frameworks allows us the flexibility of our code gained for using DI, without the trouble of creating and initializing class dependencies ourselves.

*DI frameworks analyze the dependencies of a class. 

*With this analysis, the DI framework is able to create an instance of the class and inject it into the defined dependencies.
