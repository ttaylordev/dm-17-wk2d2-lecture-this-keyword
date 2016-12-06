# Context
Context is always set and determined at invocation.
```js
function a(p1, p2){
  this
};

```
## Explicit
```js
a.call(ctx);
a.apply(ctx);
a.bind(ctx);

```
**examples**
### .call()
#### call 1
```js
function func(arg1, arg2) {
        console.log(this); // a
        console.log(arg1); // b
        console.log(arg2); // c
}

func.call('a', 'b', 'c');
/// will log out: 
// { '0': 'a' }
// b
// c
```

#### call 2
```js
function func(arg1, arg2) {
        console.log(this.letter); // a
        console.log(arg1); // b
        console.log(arg2); // c
}

func.call({letter: 'a'}, 'b', 'c');
/// will log out: 
// a
// b
// c
```

#### call 3
```js
function func(arg1, arg2) {
        console.log(this.letter); // a
        console.log(arg1); // b
        console.log(arg2); // c
}

var ayy = 'a'
var bee = 'b'
var cee = 'c'
func.call({letter: ayy}, bee, cee);

```

#### call 4
```js
function average() {
     return (this.x + this.y) / 2;
}
var ctx = {x: 5, y: 15};
average.call(ctx);
// will print:
// 10
var ctx = {a: 5, b: 15};
average.call(ctx);
// will print:
// NaN
/// there is not an x and a y in the object that was assigned as context.
// It's risky to require the contextual setup of the boject in such an explicit manner.

```


### .apply()
Apply is very similar to call, except it's great for arrays.

#### apply 1
```js
function func(arg1, arg2) {
        console.log(this.letter); // a
        console.log(arg1); // b
        console.log(arg2); // c
}

func.apply({letter:'a'}, ['b', 'c'])

```

#### apply 2
```js
function func(arg1, arg2) {
        console.log(this.letter); // a
        console.log(arg1); // b
        console.log(arg2); // c
}

var ayy = 'a'
var beecee = ['b', 'c']

func.apply({letter: ayy}, beecee);


```

#### apply 3
```js
function average() {
     return this.x + this.y / 2;
}
var ctx = {
  x: 10,
  y: 20
}
average.apply(ctx);
/// will print:
// 15


```

### .bind()

#### bind 1
```js
function add(){
	this.count++;
}

var counter = {
    count: 0
}
var counter2 = {
    count: 0
}
var counter3 = {
    count: 0
}

addToC1 = add.bind(counter);
addToC2 = add.bind(counter2);
addToC3 = add.bind(counter3);

addToC1();
addToC2();
addToC3();

console.log(counter, ' counter')
console.log(counter2, ' counter2')
console.log(counter3, ' counter3')
```

### bind 2
```js
// This example is using a callback, binding a method to it's own object, then passing it into a callback, which calls the function with its bound context.
// create a context object, bind it, then send it along to the callbacks...
function callIt(func) {
	 func();
}

var counter = {
        count: 0,
        // Sloppy-mode method
        inc: function () {
            this.count++;
        }
}

var boundInc = counter.inc.bind(counter);
callIt(boundInc);

console.log(counter.count);
```


### bind 3
```html
<button id="btn" type="button">Get the car's brand</button>
```
```js
var car = {
  brand: 'Nissan',
  getBrand: function(){
    console.log(this.brand);
  }
};

var el = document.getElementById('btn');
el.addEventListener('click', car.getBrand.bind(car));

```


## Implicit
only applies to methods, the context is whatever is left of the dot.
'this' is refering to whatever is left of the dot.
Methods
```js
person.a();

```
**examples**

```js
var o = {
  a: 1,
  b: 2,
  c: 3,
  average: function(){
    return (this.a + this.b + this.c) / 3;
  }
};

console.log(o.average()); //doesn't work
```

```js
function avg(){
    return (this.a, this.b, this.c) / 3;
}

var o = {
  a: 1,
  b: 2,
  c: 3,
  average: avg
};

var p = {
  a: 1,
  b: 2,
  c: 3,
  average: avg
};

console.log(o.average); //doesn't work
console.log(p.average); //doesn't work

```
## default / window
The context is never explicitly set, there's nothing left of the dot, therefore the context is the window itself.
It does not use the regular js scope rules.
Usually a mistake, 
```js
a();

```

## 'new' keyword
It is set explicitly, but behaves differently thatn explicit context rules for call, apply, and bind.
Constructor functions, etc.
Constructor functions should always start with a capital (Pascal case).
If not invoked with the new keyword, a constructor function will apply to the window object(oops).
```js
new a();

```

**examples**
#### 1
```js
function Animal(type, legs, sound){
  
    this.type = type;
    this.legs = legs;
    this.noise = sound;
    this.sound = function(){
      console.log('The ' + this.type + ' says' + this.noise);
    }
    this.run = function(){
      console.log('I am running on ', this.legs, ' legs');
    }
}

var zebra = new Animal('Zebra', 4, 'whoop whoop')
var lion = new Animal('Lion', 4, 'roar')
```

#### 2
```js
//Build a bank constructor Function
//It needs an account number, and owner name, and a balance.
//Give it methods to add and withdraw from the balance
//Give it a method to log the remaining balance for the owner name
//Make 2 accounts and manipulate their balances.
function Bank(accountNumber, ownerName, balance){
  this.accountNumber = accountNumber;
  this.ownerName = ownerName;
  this.balance = balance;
  this.add = function(deposit){
    this.balance = (balance += deposit);
  }
  this.withdraw = function(withdrawAmnt){
    this.balance = (balance -= withdrawAmnt);
  }
  this.log = function(){
    console.log('Your current balance is ', balance, ' dollars');
  };
}

var bob = new Bank(1222, 'bob', 4000);
var susan = new Bank(3333, 'susan', 3000);

susan.log();
bob.log();
bob.add(100);
bob.log();
bob.add(300);
bob.log();

// can't do this, because the context was not provided, so itdefaults to the global object.
var addToBob = bob.add;
addToBob(1000);
// you can use explicit context to solve this problem this way. Using call, we can feed it some context.
// The first parameter in the .call method is the context, the second parameter is the parameter that the function requires.
addToBob.call(bob, 1000);
// you can even borrow bob's method to use on Susan.
addToBob.call(susan, 3000);
// or you can use apply, listing the parameters in an array. If they're already in an arry, apply is the better method. If they are not, just use call.
addToBob.apply(bob, [2200])
// .bind will bind it forever to a context. It does not invoke the function. It creates a new reference to the method, with explicit context being bound. It does not invoke immediatly. It sets up a function to be ran later
var addToSusan = addToBob.bind(susan);
// now we have a permenant addToSusan that we can invoke.
addToSusan(200);
```

## Exercises


```js
//Create a dog object that has hungry, tired, playful
dog = {
  hungry: true,
  tired: true, 
  playful: true,
  play = function(){
    if (this.playful - 30 >= 0){
      this.playful -= 30;
    } else {
      this.playful = 0;
    }
    this.hungry += 20;
    this.tired += 10;
  },
  eat = function(){
    if (this.hungry - 30 >= 0){
      this.hungry -= 30;
    } else {
      this.hungry = 0
    }
    this.tired += 20;
  },
  sleep = function(){
    if (this.tired - 30 >= 0){
      this.tired -= 30;
    } else {
      this.tired = 0
    }
    this.playful += 20;
  }
  log = function(){
    console.log(this.hungry);
    console.log(this.tired);
    console.log(this.this.playful);
  }
}
//Create the methods on that object : play, eat, sleep
//The play method decreases playful by 30 (Never less than 0), and increases hungry by 20 and tired by 10, 
//The eat method decreases hungry by 30 (Never less than 0) and increases tired by 20
//The sleep method decreases tired by 30 (Never less than 0) and increases playful by 20
//Add a method that logs how hungry, tired, and playful your dog is

```

## Jeremy's example
```js
//Create a dog object that has hungry, tired, playful
var dog = {
  hungry: 0,
  tired: 10,
  playful: 20,
//Create the methods on that object : play, eat, sleep
  play: function(){
    // this.playful -= this.playful < 30 ? this.playful: 30;
    this.playful -= 30;
    this.hungry += 20;
    this.tired += 10;
    if(this.playful < 0){
      this.playful = 0
    } 
  },
  eat: function(){
     this.hungry -= 30;
    this.tired += 20;
    if(this.hungry < 0){
      this.hungry = 0
    } 
  },
  sleep: function(){
     this.tired -= 30;
    this.playful += 20;
    if(this.tired < 0){
      this.tired = 0
    } 
  },
  log: function(){
    console.log(this.hungry, this.tired,this.playful)
  }
}

dog.play();
dog.eat();
dog.sleep();
dog.log();
```



# BONUS

```js
// es6 example
// when you make a class, because it's not a function, it doens't have arguments, we want to use it the same way... we need to give it an entry point function, it's called constructor()
// we do not add functions to the constructor itself in a class.
class Animal {
  constructor(name, legs, sound){
    this.anme = name;
    this.legs = legs;
    this.sound = sound;
  }
  makeSound(){
    console.log('The ', this.name, ' says ', this.noise);
  }
}

var zebra = new Animal('Zebra', 4, 'Whoop whoop');
zebra.makeSound();

```