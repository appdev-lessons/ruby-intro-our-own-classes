# Defining our own classes

## Modeling real world things

We can *model* a person pretty well with an `Array`:

```ruby
a = Array.new # This is the longhand for a = []
a.push("Raghu")
a.push("Betina")
a.push("Instructor")

pp a
pp a.at(1)
pp a.class
```
{: .repl #person_array title="Person Array" points="1"}

And we can model a person even better with a `Hash`:

```ruby
h = Hash.new # This is the longhand for h = {}
h.store(:first_name, "Raghu")
h.store(:last_name, "Betina")
h.store(:role, "Instructor")

pp h
pp h.fetch(:last_name)
pp h.class
```
{: .repl #person_hash title="Person Array" points="1"}

But we can do even better than a `Hash`. We can define *our own class* to represent people with the `class` keyword:

```ruby
class Person
end

pp Person.new
```
{: .repl #person_class_1 title="Person Class 1" points="1"}

Remember, the only time we use capital letters in Ruby is when we're referring to classes — and this goes for when we're naming our own classes, too. So `class person` will not work. If you have a multi-word name, then `CamelCase` it — `class VeryImportantPerson`.

And we can declare what attributes a person can have with the `attr_accessor` keyword:

```ruby
class Person
  attr_accessor :first_name
  attr_accessor :last_name
  attr_accessor :role
end

pp Person.new
pp Person.new.first_name
```
{: .repl #person_class_2 title="Person Class 2" points="1"}

And now the `Person` class is a first-class citizen in the language, just like `Array` and `Hash`. Compare the code below to the code above for creating `Array`s and `Hash`es to store information:

```ruby
class Person
  attr_accessor :first_name
  attr_accessor :last_name
  attr_accessor :role
end

c = Person.new
c.first_name = "Raghu"
c.last_name = "Betina"
c.role = "Instructor"

pp c.last_name
pp c.role
pp c.class
```
{: .repl #person_class_3 title="Person Class 3" points="1"}

For each attribute that we declared, we get methods that we can call to assign and retrieve values.

- Choose all that are true of our `class Person` example above. Feel free to use the runnable code block to experiment!
- We can add a new `birthdate` method to `Person` by writing `c.birthdate`
    - No, we need to define the new method _in_ the class, not on an instance _of_ the class
- We can define a new `.birthdate` method to `Person` by adding `attr_accessor "birthdate"` to the class definition
    - Not quite, `"birthdate"` is a string
- We can define a new `.birthdate` method to `Person` by adding `attr_accessor :birthdate` to the class definition
    - Yes!
- `Person` is a special object, and NOT a class like `Hash` or `Array`
    - No! `Person` is just a class that we defined
- We can reuse our new `Person` class to create as many new people as we want (stored in separate variables) 
    - Yes!
- `:role` is a symbol in the class definition, and it is available as a method when we instantiate a new `Person`
    - Yes!
{: .choose_all #person_class title="class Person" points="3" answer="[3,5,6]" }

### Defining instance methods 

There are a few reasons I like using classes more than `Hash`es to model things, but here is the big one: in addition to just storing a list of attributes about a thing, we can also _define our own methods_ with the `def` keyword. 

In Ruby, we define new methods with the construction:

```ruby
# define a method named 'my_method'
def my_method
  # do stuff
  return "stuff"
end
```

And then we can call our new method later in our code by just typing the name we put after `def`:

```ruby
def my_method
  return "stuff"
end

# invoke/call method
pp my_method
```
{: .repl #define_a_method title="Define a method" points="1"}

And the output is whatever we `return`ed from the method in our definition! Easy as that.

We can even provide arguments to a method we define by including parentheses:

```ruby
def square_root(a_number)
  answer = a_number ** 0.5
  return answer
end

pp square_root(9)
pp square_root(16)
pp square_root(144)
```
{: .repl #square_root title="square root" points="1" }

----

Okay, back to our `Person` class. Let's add the following `full_name` method to the class we defined in the runnable code block above. 

```ruby
  def full_name
    return self.first_name + " " + self.last_name
  end
```

Now, in addition to being able to store data (first and last names), I can ask any `Person` to compute its full name:

```ruby
class Person
  attr_accessor :first_name
  attr_accessor :last_name
  attr_accessor :role

  def full_name
    return self.first_name + " " + self.last_name
  end
end

hs = Person.new
hs.first_name = "Homer"
hs.last_name = "Simpson"

pp "Hello, " + hs.full_name + "!"
```
{: .repl #homer_simpson title="Hello, Homer Simpson" points="1" }

Two keywords to note:

 - I used the `return` keyword to signify what value I wanted to replace `hs.full_name` in the original expression after it's been evaluated.
 - I used the `self` keyword to refer to the object who was asked to calculate its full name, since I can't know in advance what (if any) variable name will be used.

Here's a slightly more involved example. Now every `Person` that we create will have the ability to compute their age based on their own `birthdate` attribute (translated into a `dob` by `Date`):

```ruby
class Person
  require "date"  # Pull in the Date class (not loaded by default)
  attr_accessor :birthdate
  attr_accessor :first_name
  attr_accessor :last_name
  attr_accessor :role

  def full_name
    return self.first_name + " " + self.last_name
  end

  def age
    dob = Date.parse(self.birthdate)
    now = Date.today
    age_in_days = now - dob
    age_in_years = age_in_days / 365

    return age_in_years.to_i
  end
end

hs = Person.new
hs.first_name = "Homer"
hs.last_name = "Simpson"

pp "Hello, " + hs.full_name + "!"

hs.birthdate = "April 19, 1987"
pp hs.age
```
{: .repl #homer_simpson_age title="Age, Homer Simpson" points="1" }

Note that we had to `require "date"` in order to load the `Date` class into the program; Ruby doesn't load this class into every program by default, like it does with the core classes (`String`, `Integer`, etc).

<aside markdown="1">
We could have written `require("date")`, but recall that we typically drop the `()` parentheses for `require` statements.
</aside>

So, rather than using a `Hash` to model real world things, it's a good idea to create classes, and then empower them with *behavior* (methods) in addition to information.

### Defining class methods 

The methods `full_name` and `age` above are known as _instance methods_, because we call them on individual **instances** of the `Person` class (Homer, Mickey, Minnie, etc). 

We can also define **class**-level methods, that we call directly on `Person` itself. This can be handy if we want to define re-usable utility methods that don't really belong to any one individual person.

<aside markdown="1">
In writing text (e.g. manuals, technical references, comments) We indicate *instance* methods by `Object#method` with the `#` symbol. So we have `Person#full_name` and `Person#age` defined in the previous section. We still call them in our code with the `.` symbol like `hs.full_name`.

On the other hand, we always indicate *class* methods (like `Date.parse`) by `Object.method` with the `.` symbol. 
</aside>

Here's an example similar to `Date.parse` — what if we wanted users of the `Person` class to quickly be able to create new instances of the class like this:

```ruby
Person.parse("Betina, Raghu")

# => should return a new person with first
#    and last name attributes already populated
```

Then, we can define the **class-level** method `parse`, called directly on `Person`, like this:

```ruby
class Person
  attr_accessor :first_name
  attr_accessor :last_name

  def Person.parse(last_comma_first)
    last_first_array = last_comma_first.split(",")
    the_last_name = last_first_array.at(0).strip
    the_first_name = last_first_array.at(1).strip
    
    a_new_person = Person.new
    a_new_person.first_name = the_first_name
    a_new_person.last_name = the_last_name
    
    return a_new_person
  end
end

a_person = Person.parse("Betina, Raghu")
pp a_person.first_name
pp a_person.last_name
```
{: .repl #person_parse title="Person.parse" points="1" }

The new things to note in the code above:
    
  - When defining the method, we do `def Person.parse` rather than just `def parse` to make it a **class method** rather than an **instance method**. That way, we call the method directly on capital-`P` `Person`.
  - We give the method the ability to accept an argument by adding parentheses and choosing a name for the argument when defining the method. Then we can use the input within the method definition, sort of like how we use a block variable.

Answer this quiz question to check your understanding.

- Select all that are true:
- The two methods we define on a class are called instance and class 
    - Yes!
- Instance methods act on the class itself, and class methods act on an instance of the class
    - No, please re-read the previous section
- Instance methods act on an instance of the class, and class methods act on the class itself
    - Yes!
- When you see `Object#method` notation, you can be sure they are talking about a class method
    - No, please re-read the previous section
- The `self` keyword refers to the class it is inside of and can be used to reference all the attributes of that class
    - Yes!
{: .choose_all #class_quiz title="Class quiz" points="3" answer="[1,3,5]" }

### Practice building your own class

Let's get some practice in the graded code block below. Get the tests to pass before you move on.

Define a class `User`:

```ruby
class User
  # your code here
end
```

with:
 - An attribute `username`
 - An attribute `email`
 - An attribute `bio`
 - An attribute `birthdate`
 - An instance method `age`, that calculates the number of years between today and `birthdate`
 - An instance method, `about_me`, that puts `username`, `age`, `bio`, and `email` together into a string, with the `username` downcased: `"username (age): bio. Reach me at: email"`

The instance methods should **return** a value; they shouldn't print anything to the terminal. (Hint: You can use the `age` method, _within_ the `about_me` method.)

You can test your code by adding this to the end of the editor (below the `end`):

```ruby
new_user = User.new
new_user.username = "JOE"
new_user.email = "joe@example.com"
new_user.bio = "Ruby enthusiast"
new_user.birthdate = "1987-04-19"

pp new_user.about_me
```

which should output (if the date today was in June 2023):

```
"joe (36): Ruby enthusiast. Reach me at: joe@example.com"
```
{: .bleed-full }

```ruby
# write your class here
```
{: .repl #user_class title="User class" }

```ruby
describe "User class" do
  it "has an attribute username" do
    u = User.new
    u.username = "Alice"
    expect(u.username).to eq("Alice"), "Expected User class to have a username attribute, but it did not."
  end
end
```
{: .repl-test #user_class_test_1 for="user_class" title="User class has an attribute username" points="1"}

```ruby
describe "User class" do
  it "has an attribute email" do
    u = User.new
    u.email = "alice@example.com"
    expect(u.email).to eq("alice@example.com"), "Expected User class to have a email attribute, but it did not."
  end
end
```
{: .repl-test #user_class_test_2 for="user_class" title="User class has an attribute email" points="1"}

```ruby
describe "User class" do
  it "outputs the correct about_me string" do
    u = User.new
    u.username = "Alice"
    u.email = "alice@example.com"
    u.bio = "A bit about me"
    u.birthdate = "1981-01-01"

    # Set today's date to 2020-07-01
    allow(Date).to receive(:today).and_return Date.new(2020,07,01)

    expect(u.about_me).to eq("alice (39): A bit about me. Reach me at: alice@example.com")
  end
end
```
{: .repl-test #user_class_test_3 for="user_class" title="User class outputs the correct about_me string" points="1"}

## Inheritance

Congrats on defining your first `User` class 🎉! 

When you define new classes, you can choose to **inherit** all the power of a "parent" class, and then add some custom behavior:

```ruby
class Person
  attr_accessor :first_name
  attr_accessor :last_name

  def full_name
    return self.first_name + " " + self.last_name
  end
end

class Instructor < Person
  attr_accessor :role
end

class Student < Person
  attr_accessor :grade
end
```

Using the `<` notation, `Instructor`s and `Student`s can do everything `Person` can (they _inherit_ from the `Person` class), and a little bit more.

Creating the first individual **instance** of the `Instructor` class:

```ruby
person1 = Instructor.new
person1.first_name = "Raghu"
person1.last_name = "Betina"
person1.role = "Lecturer"
```

Creating the second individual instance of the `Instructor` class:

```ruby
person2 = Instructor.new
person2.first_name = "Arjun"
person2.last_name = "Venkataswamy"
person2.role = "Faculty Coach"
```

Creating the first individual instance of the `Student` class:

```ruby
person3 = Student.new
person3.first_name = "Trenton"
person3.last_name = "Arthur"
person3.grade = "A"
```

Creating the second individual instance of the `Student` class:

```ruby
person4 = Student.new
person4.first_name = "Tom"
person4.last_name = "Besio"
person4.grade = "Incomplete"
```

Now we can use them:

```ruby
pp person1.full_name # => "Raghu Betina"
pp person1.role # => "Lecturer"
pp person2.full_name # => "Arjun Venkataswamy"
pp person2.role # => "Faculty Coach"
pp person3.full_name # => "Trenton Arthur"
pp person3.grade # => "A"
pp person4.full_name # => "Tom Besio"
pp person4.grade # => "Incomplete"
```

What would happen if I tried doing `pp person4.role`? How about `pp person1.grade`? What would the error message be? Why? Try defining all of the above and give it a shot in a runnable code block:

```ruby
class Person
end

class Instructor
end

class Student
end
```
{: .repl #inherit_from_person title="Person inheritance" points="1" }

Test your understanding of inheritance by considering this code:

```ruby
class Animal
  attr_accessor :eats

  def hungry_for
    return "Wants to eat " + self.eats
  end
end

class Bear < Animal
end
```

- Select all that are true.
- `eats` is a class method
    - No, we use it on an _instance_ of the class
- `self.` refers to `hungry_for`
    - No, it refers to `Animal` (and `Bear` because we inherit from `Animal`)
- We cannot call `.eats` on an instance of `Bear` because we didn't define it there
    - No, please re-read the previous section
- `<` above means "less than"
    - No, please re-read the previous section
- None of these
    - Yes!
{: .choose_all #inherited title="Inheritance" points="1" answer="[5]" }

### Practice inheritance

Use the answer from the previous graded code block where you built the `User` class to complete this next graded code block.

Create a new `Admin` class, inherit _everything_ from the `User` class, and add the attribute `password` to the new `Admin` class:

```ruby
class User
  # code from your previous answer
end

# add an Admin class here
```
{: .repl #admin_class title="Admin class" }

```ruby
describe "Admin class" do
  it "has an attribute username" do
    u = Admin.new
    u.username = "Alice"
    expect(u.username).to eq("Alice"), "Expected Admin class to have a username attribute, but it did not."
  end
end
```
{: .repl-test #admin_class_test_1 for="admin_class" title="Admin class has an attribute username" points="1"}

```ruby
describe "Admin class" do
  it "outputs the correct about_me string" do
    u = Admin.new
    u.username = "Alice"
    u.email = "alice@example.com"
    u.bio = "A bit about me"
    u.birthdate = "1981-01-01"

    # Set today's date to 2020-07-01
    allow(Date).to receive(:today).and_return Date.new(2020,07,01)

    expect(u.about_me).to eq("alice (39): A bit about me. Reach me at: alice@example.com")
  end
end
```
{: .repl-test #admin_class_test_2 for="admin_class" title="Admin class outputs the correct about_me string" points="1"}

```ruby
describe "Admin class" do
  it "has an attribute password" do
    u = Admin.new
    u.password = "password"
    expect(u.password).to eq("password"), "Expected Admin class to have a password attribute, but it did not."
  end
end
```
{: .repl-test #admin_class_test_3 for="admin_class" title="Admin class has an attribute password" points="1"}

## Conclusions

That's it for the basics of Ruby! There's just a few other lessons coming up, including the Ruby Gym to get some more practice. Then we'll get to using Ruby to interact with the internet!

- Approximately how long (in minutes) did this lesson take you to complete?
{: .free_text_number #time_taken title="Time taken" points="1" answer="any" }

<span style="font-size: large">**When you are done here, close the window and return to Canvas for the next lesson in the series.**</span>

----
