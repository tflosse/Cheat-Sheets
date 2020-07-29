# Ruby Classes

**OOP:** organizes software design around data, or objects, rather than functions and logic. An object can be defined as a data field that has unique attributes and behavior.

![](https://i.imgur.com/Rsol5Kv.png)
> An imperative approach (HOW): “I see that table located under the Gone Fishin’ sign is empty. My husband and I are going to walk over there and sit down.”

> A declarative approach (WHAT): “Table for two, please.”

```js 
// Imperative for afunction called double which takes in an array of numbers and returns a new array after doubling every item in that array.
function double (arr) {
  let results = []
  for (let i = 0; i < arr.length; i++){
    results.push(arr[i] * 2)
  }
  return results
}
// Declarative
function double (arr) {
  return arr.map((item) => item * 2)
}

// Imperative for a function called add which takes in an array and returns the result of adding up every item in the array.
function add (arr) {
  let result = 0
  for (let i = 0; i < arr.length; i++){
    result += arr[i]
  }
  return result
}
// Declarative
function add (arr) {
  return arr.reduce((prev, current) => prev + current, 0)
}

// Imperative for add a click event handler to the element which has an id of btn (vanilla or jQuery).
// When clicked, toggle (add or remove) the highlight class as well as change the text to Add Highlight or Remove Highlight depending on the current state of the element.
$("#btn").click(function() {
  $(this).toggleClass("highlight")
  $(this).text() === 'Add Highlight'
    ? $(this).text('Remove Highlight')
    : $(this).text('Add Highlight')
})
// Declarative: 
<Btn
  onToggleHighlight={this.handleToggleHighlight}
  highlight={this.state.highlight}>
    {this.state.buttonText}
</Btn>
```

**Objects** store and manage any state or data relevant to the object itself internally and we use methods to initialize, modify, or act on an objects' internal state. We define a single ***class*** with all of this behavior described in one place.

Objects have or are associated with:
- a container for data and also controls access to the data. 
- a set of attributes or variables belonging to the object. 
- a set of functions that provide an interface to the functionality of the object, or *methods*.

Object oriented programming gives use three major benefits:

- **Encapsulation:** The complexity of our objects is hidden and a user can interact with a well defined set of methods. We can redefine internal behavior without changing anything externally - a huge benefit for refactoring.
- **Modularity:** Because the interface (set of methods) for an object is well defined, it is easy to exchange one object for another and reorganize objects without breaking them.
- **Inheritance:** Object Oriented design lets certain classes inherit from, or borrow the behavior of another, more general class. The inheriting class can add and overwrite inherited methods.

#### Object Classes

1. Create an object
2. Give it methods
3. Instantiate
4. Create ***instance variable***: instance variables are available to a particular instance, use the @ sign to define a variable. It has the advantge of being accessible in any instance method in a particular instance of a class.
5. Create a **constructor** or ***initialize method***: that will automatically be invoked when an object of that class type is created using `ClassName.new` - Ruby also calls them initializers because they always have the name initialize (In Java and C++, a constructor is a method with the same name as the class name.) 

```ruby
class House
  # instance variables; unique data for each object: casey and suresh
  @this_owner_name = ''
  @this_address = ''

# constructor
  def initialize
    # do something
  end

# other methods
  def set_address(address)
    @this_address = address
  end

  def set_owner(owner)
    @this_owner = owner
  end

  def get_owner
    @this_owner
  end

  def get_address
    @this_address
  end
end

# instance
my_house = House.new
p my_house
# => #<House:0x0000562ae8188f08>

casey_house = House.new
casey_house.set_address("111, New York, NY")
p casey_house.get_address
# => "111, New York, NY"
```

###### Variables

- **Local** variables: local scope or context. If defined within a method, its scope is kept within that method. Local variable names must begin with either a lowercase letter or an underscore (_), and must not be prefixed with punctuation reserved for other variables.
> y = "Yes"  # y is a String

- **Instance** variables: belongs to a particular instance of a class, hence the name. It can only be accessed from outside that instance via an accessor (helper) method. Instance variables are always prefixed with a single at sign (@), as in @hello.

- **Class** variables: is shared among all instances of a class. Only one copy of a class variable exists for a given class. It is prefixed by two at signs (@@), such as @@times. It needs to be initialized to be used.

- **Global** variables: are globally available to a program, inside any structure. Their scope is the whole program. They are prefixed by a dollar sign ($), such as $amount.

- **Constants** variables: begin with a capital letter (Tam) and by convention frequently use all capitals (TAM) and must have a value assigned to them to exist.. They are not expected to have their values changed after initial assignment. Ruby constants are called mutable because although a constant is only expected to refer to a single object throughout the program, the value of that object may vary.

- **Parallel Variable Assignment**: several values to several variables in a single expression, based on order. A list of variables, separated by commas, can be placed to the left of the equals sign, with the list of values to assign them on the right.
> name, grade, gpa = 'Suresh', 89, 3.6

#### Inheritance

Moves the common methods to a single class, and allows us to specify that other classes inherit from this class. **Subclasses inherit methods from a **superclass**.

> The Vehicle class is called the superclass of the other three classes. Car, Truck, and Motorcycle are called subclasses of Vehicle

```ruby
class Vehicle

    # Global variable
    @@factory = "Tesla"

  # This is our constructor with instance variables
  def initialize(odometer, gas_used)
    @this_odometer = odometer
    @this_gas_used = gas_used
  end

  def sound_horn
    puts "Beep! Beep!"
  end

  def accelerate
    puts "Vroom ... Vroom!!"
  end

  def check_mileage
    puts @this_odometer / @this_gas_used
  end

  def print_info
    puts "This vehicle is made by #{@@factory}."
  end
end

# Car class inherit from Vehicle class
class Car < Vehicle

end

# Instance of a Vehicle
my_car = Car.new(10000, 45)
my_car.accelerate
# => Vroom ... Vroom!!
my_car.print_info
# => This car was made by Tesla

# Truck class inherit from Vehicle class
class Truck < Vehicle

end

# Another instance
your_truck = Truck.new(22964, 726)
your_truck.check_mileage
# => 31
```

Car class does not inherit the `@this_odometer` and `@this_gas_used` instance variables, but it does inherit `odometer=` and `gas_used=` instance methods.

