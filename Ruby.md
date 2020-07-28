## Installs

> Will update -- started with a collection of methods below.

## Getting Started

`In terminal` for Ruby CLI
> pry

or `in VS code terminal` 
> ruby file_name.rb

****

`split`, `p`, and `puts`
```ruby
pry(main)> sentence = "Learning Ruby is fun"
=> "Learning Ruby is fun"
pry(main)> array = sentence.split(" ")
=> ["Learning", "Ruby", "is", "fun"]
pry(main)> p array
["Learning", "Ruby", "is", "fun"]
=> ["Learning", "Ruby", "is", "fun"]
pry(main)> puts array
Learning
Ruby
is
fun
=> nil
```
`bang` operator
```ruby
pry(main)> word = "learn"
=> "learn"
pry(main)> word.upcase
=> "LEARN"
pry(main)> word.upcase!
=> "LEARN"
pry(main)> p word
"LEARN"
=> "LEARN"
```
`methods`
```ruby
pry(main)> puts "hello".methods
encode
encode!
unpack
unpack1
pretty_print
include?
%
*
+
count
shellsplit
shellescape
partition
to_c
sum
next
casecmp
casecmp?
insert
bytesize
match
match?
succ!
<=>
next!
upto
index
replace
==
===
chr
=~
rindex
[]
[]=
byteslice
getbyte
setbyte
clear
scrub
empty?
eql?
-@
downcase
scrub!
dump
undump
upcase
+@
capitalize
swapcase
upcase!
downcase!
capitalize!
swapcase!
hex
oct
freeze
inspect
bytes
chars
codepoints
lines
reverse
reverse!
concat
split
crypt
ord
length
size
grapheme_clusters
succ
start_with?
center
prepend
strip
rjust
rstrip
ljust
chomp
delete_suffix
sub
to_str
to_sym
intern
sub!
lstrip
<<
to_s
to_i
to_f
gsub!
chop!
chomp!
delete_prefix
gsub
chop
end_with?
scan
tr
strip!
lstrip!
rstrip!
delete_prefix!
delete_suffix!
delete!
tr_s
delete
squeeze
tr!
tr_s!
each_grapheme_cluster
squeeze!
each_line
each_byte
each_char
each_codepoint
b
slice
slice!
hash
encoding
force_encoding
unicode_normalize
valid_encoding?
ascii_only?
rpartition
unicode_normalize!
unicode_normalized?
to_r
between?
<=
>=
clamp
<
>
__binding__
pry
pretty_print_instance_variables
pretty_print_cycle
pretty_print_inspect
instance_variable_defined?
remove_instance_variable
instance_of?
kind_of?
is_a?
tap
instance_variable_get
instance_variable_set
instance_variables
singleton_method
method
public_send
define_singleton_method
public_method
pretty_inspect
extend
to_enum
enum_for
!~
respond_to?
object_id
send
display
nil?
class
singleton_class
clone
dup
itself
yield_self
then
taint
tainted?
untaint
untrust
untrusted?
trust
frozen?
methods
singleton_methods
protected_methods
private_methods
public_methods
equal?
!
__id__
instance_exec
!=
instance_eval
__send__
```

`uniq`
```ruby 
pry(main)> ary = [1,2,3,4,4,4,5,6,6,6]
=> [1, 2, 3, 4, 4, 4, 5, 6, 6, 6]
pry(main)> ary.uniq
=> [1, 2, 3, 4, 5, 6]
pry(main)> ary.uniq!
=> [1, 2, 3, 4, 5, 6]
pry(main)> 
```

`concat`, `shovel` operator, `+` and `to`
```ruby
pry(main)> s = "hello"
=> "hello"
pry(main)> s.concat(" world")
=> "hello world"
pry(main)> s << "! It's me, Nick"
=> "hello world! It's me, Nick"
pry(main)> s + ". How's it going?"
=> "hello world! It's me, Nick. How's it going?"
pry(main)> s + 4.0.to_s
=> "hello world! It's me, Nick4.0"
```

`length of a string`
```ruby 
s = "hello"
s.length # => 5: counts characters in 1.9, bytes in 1.8
s.size # => 5: size is a synonym
s.bytesize # => 5: length in bytes; Ruby 1.9 only
s.empty? # => false
"".empty? # => true
```

`position in a string`
```ruby
s = "hello"
s.index('l') # => 2: index of first l in string
s.index('l',3) # => 3: index of first l in string at or after position 3
s.index('Ruby') # => nil: search string not found
s.rindex('l') # => 3: index of rightmost l in string
s.rindex('l',2) # => 2: index of rightmost l in string at or before 2
```

`template literal`
```ruby
pry(main)> variable = "value"
=> "value"
pry(main)> puts "Template literal : #{variable}"
Template literal : value
=> nil
```

`other`
```ruby
s = "hello"
s.concat(" world") # Synonym for <<. Mutating append to s. Returns new s
s.insert(5, " there") # Same as s[5] = " there". Alters s. Returns new s
s.slice(0,5) # Same as s[0,5]. Returns a substring
s.slice!(5,6) # Deletion. Same as s[5,6]="". Returns deleted substring
s.eql?("hello world") # True. Same as ==
```

`interpolation`
```ruby
"I have #{13 * 4} cards" # => "I have 52 cards"
'I have #{13 * 4} cards' # => 'I have #{13 * 4} cards'
```

`booleans`
Only false and nil are falsy in Ruby. Everything else is truthy.
```ruby
1 == 1 # => true
1 == '1' # => false
1 == 1.0 # => true
[1, 2] == [1, 2] # => true
[1, 2] == [2, 1] # => false
{a: 1, b: 2} == {b: 2, a: 1} # => true
```

`truthy and falsy`
```ruby
!! false # => false
!! nil # => false

!! 0 # => true
!! '' # => true
```

#### Flow Control

`conditionals`
```ruby
if name == "Jason"
  puts "It's Jason"
elsif name == "Lauren"
  puts "It's Lauren"
else
  puts "Not Jason or Lauren"
end
```

```ruby
if !(name == "Jason")
  puts "Not Jason!"
end
# can be
unless name == "Jason"
  puts "Not Jason!"
end
# or single-line
puts 'you are wise!' if age >= 100
puts 'you are wise!' unless age < 100
```

`ternary`
```ruby
num.even? ? "#{num} is even!" : "#{num} is odd!"
```

`loops`
```ruby
# loop do
i = 0
loop do
  puts "i is number #{i}"
  i += 1
  # loop do needs a break condition
  break if i == 10
# loops (and methods) also need an end statement
end
```
```ruby
# while
# plus the shovel operator to store value sin array
array = []
i = 5
while i <= 20
  array << i #or .push()
  puts i += 2
end
p array

#console shows:
[5, 7, 9, 11, 13, 15, 17, 19]
```

```ruby
print "Do you like wine?"
  while gets.chomp != "yes" do
  puts "Do you like wine? "
end
```
Note: `do ... end` is known as a block

```ruby
1.upto(max) do |i|
  # code to execute in loop
end

# in ruby.rb file:
1.upto(10) do |i|
  puts i
end
```

```ruby
for i in 0..5
  puts "#{i} pieces of pie for Tam"
end
# ..5 will include 5

for i in 0...5
  puts "#{i} pieces of pie for Tam"
end
# ...5 will stop at 4
```

```ruby
8.upto(12) {|num| print "#{num} "}
# => 8 9 10 11 12
# |num| where num is a placeholder similar to the use of element in array methods

3.times do |i| 
 puts i
end
# => 0
# 2
# 3
```

```ruby
names = ['Bob', 'Joe', 'Steve', 'Janice', 'Susan', 'Helen']
names.each { |name| puts name }
```

#### Defining methods:

To define a method, use: 
```ruby
def square (num)
  Math.sqrt(num).to_i**2 == num
end

# can also use question mark if we want the method to return a Boolean:
def square? (num)
  Math.sqrt(num).to_i**2 == num
end

def printer(str)
    print str
end
```

#### FizzBuzz exercise

```ruby
i =1
while i < 16 
  if (i%3==0) and (i%5==0)
    puts "fizzbuzz"
  elsif (i%3==0)
    puts "fizz"
  elsif (i%5==0)
    puts "buzz"
  else puts "#{i}"
  end
  i +=1
end

# 1
# 2
# fizz
# 4
# buzz
# fizz
# 7
# 8
# fizz
# buzz
# 11
# fizz
# 13
# 14
# fizzbuzz
```
#### Arrays
```ruby
array = ["a","b","c"]
other_array = ["f", "g", "h"]

array[4] = "e"
p array
# => ["a", "b", "c", nil, "e"]
# inserts "nil where array[3] should have been"

p combined = array + other_array
# => ["a", "b", "c", nil, "e", "f", "g", "h"]

p combined[-2]
# => "g"
# gives second before last [-1] would be last
p combined.first
# => "a"
# anything outside the range will return nil

p combined[2,4]
# => ["c", nil, "e", "f"]
# will return the objects in that range

p combined[-3,2]
# => ["f", "g"]
# can also start from the end of the array to get a range
```

```ruby
developers = %w[Caleb Joel Julia Adam]
# => ["Caleb", "Joel", "Julia", "Adam"]

developers << 'Kira' << 'Kosta'
# => ['Caleb', 'Joel', 'Julia', 'Adam', 'Kira', 'Kosta']

developers.push 'Rick'
# => ['Caleb', 'Joel', 'Julia', 'Adam', 'Kira', 'Kosta', 'Rick']

developers << 'Johnathan' << 'Peter'
# => ['Caleb',
#   'Joel',
#   'Julia',
#   'Adam',
#   'Kira',
#   'Kosta',
#   'Rick',
#   'Johnathan',
#   'Peter']

developers.pop
# 'Peter'

developers.shift 4
# => ['Caleb', 'Joel', 'Julia', 'Adam']

developers
# => ['Kira', 'Kosta', 'Rick', 'Johnathan', 'Peter']

developers.unshift 'Guillermo'
# => ['Guillermo', 'Kira', 'Kosta', 'Rick', 'Johnathan', 'Peter']
```
#### Hashes

In Ruby, "A Hash is a dictionary-like collection of unique keys and their values". In sharp contrast to JavaScript, Ruby Hashes are not the most general object in the language, but are instances of a specialized class for key/value storage.
```ruby
nums = {
  "odds"  => [1, 3, 5],
  "evens" => [2, 4, 6]
}

p nums['odds']
# => [1, 3, 5]
```
A preferrable way to write it would be using symbols:
```ruby
nums = {
  :odds => [1, 3, 5],
  :evens => [2, 4, 6]
}
# symbols will point to one object_id every time it is called as a symbol. 
# In other words "bob" is different from another instance of "bob" but :bob and :bob are the same.
```

```ruby
apartment = {}
# => {}

apartment[:address] = { street: '255 Long Road', city: 'Awesomeville' }
# => {:street=>"255 Long Road", :city=>"Awesomeville"}

apartment[:bedrooms] = 3
# => 3

priced_apartment = apartment.merge(rent: 1000)
# => {:address=>{:street=>"255 Long Road", :city=>"Awesomeville"}, :bedrooms=>3, :rent=>1000}

priced_apartment[:occupants] = []
# => []

lee = { name: 'Lee', age: 24, pet: 'Fluffy' }
# => {:name=>"Lee", :age=>24, :pet=>"Fluffy"}

adrian = { name: 'Adrian', age: 25, pet: 'Scratchy' }
# => {:name=>"Adrian", :age=>25, :pet=>"Scratchy"}

priced_apartment[:occupants].push(lee, adrian)
# => [{:name=>"Lee", :age=>24, :dog=>"Fluffy"},
# {:name=>"Adrian", :age=>25, :cat=>"Scratchy"}]

priced_apartment[:occupants][1].delete(:cat)
# => "Scratchy"

priced_apartment[:rent] += 150
# => 1150
```
Get an Array of the keys that have been set in a hash:
```ruby
priced_apartment.keys
# => [:address, :bedrooms, :occupants, :rent]
```

FizzBuzz revisit with Hashes:
```ruby
def fizzbuzz(num)
hash = {}
hash[:fizzbuzz] = []
hash[:fizz] = []
hash[:buzz] = []
hash[:other] = []
  1.upto(num).each do |e|
    if (e % 5 == 0) && (e % 3 == 0)
      hash[:fizzbuzz].push(e)
    elsif e % 5 == 0
      hash[:buzz].push(e)
    elsif e % 3 == 0
      hash[:fizz].push(e)
    else
      hash[:other].push(e)
    end
  end
  p hash
end
fizzbuzz(30)

# => {:fizzbuzz=>[15, 30], :fizz=>[3, 6, 9, 12, 18, 21, 24, 27], :buzz=>[5, 10, 20, 25], :other=>[1, 2, 4, 7, 8, 11, 13, 14, 16, 17, 19, 22, 23, 26, 28, 29]}
```


## Ruby blocks

`forEach`
```js
const myArray = ['one', 'two', 'three'];
myArray.forEach((str)=>{
	console.log('the value is ' + str);
});
```
```ruby
['one', 'two', 'three'].each do |str|
	puts 'the value is ' + str
end
# becomes
['one', 'two', 'three'].each { |str| puts 'the value is ' + str } #short form
```
`map`
```js
const basicArray = [1,2,3];
const timesTwo = basicArray.map((num)=>{
  return num * 2;
});

console.log(timesTwo);
```
```ruby
times_two = [1,2,3].map do |num|
	num * 2
end
p times_two
# becomes
p [1,2,3].map {|num| num * 2}
# brackets replace "do ... end"
```
`Named blocks`
```js
const each = (array, callback)=>{
	for(let i = 0; i < array.length; i++){
		callback(array[i]);
	}
}

each([1,2,3,4], (currentNum)=>{
	console.log(currentNum);
});
```
```ruby
def each(arr, &blk) #&blk must always be the last parameter
# creates a names block
	for elem in arr
    blk.call(elem)
    # .call is used in Ruby
	end
end

each 0...5 do |currentNum|
	puts currentNum
end
```
`implicitly`
```ruby
def each(arr)
	for elem in arr
		yield(elem) #use this instead of &blk.call
	end
end

each 0...5 do |currentNum|
	puts currentNum
end
```

## Procs (procedures)
[Procs in Ruby](https://ruby-doc.org/core-2.6/Proc.html)

```js
const foo = ()=>{
	console.log('hi');
}

const bar = (callback)=>{
	callback();
}
bar(foo)
```
```ruby
foo = Proc.new do
    puts 'hi'
end
# or
foo = Proc.new {puts "hi"}
# then
def bar(callback)
    callback.call()
end

bar(foo)

# Procs can also take variable
log = Proc.new do |el|
	puts el
end
log.call(5)

# short version:
log = Proc.new {|el| puts el}
log.call(5)
```

## Ruby Enumarables

`Select, detect, reject`

```ruby
p [1,2,3,4,5].select { |i| i > 3 } #returns [4,5]
# compares to filter in Js
p [1,2,3,4,5].detect { |i| i > 3 } #returns [4]
# compares to find in JS
p [1,2,3,4,5].reject { |i| i > 3 } #returns [1,2,3] 
# also analogous as filter (opposite select)
```

`Grep`
Returns an array of every element that match a pattern
```ruby
p [1,3,5,10,15].grep (1..5) #returns [1,3,5]
p [0.3, "three", :three, "thirty-three"].grep /three/ #returns ["three", :three, "thirty-three"]

# If an optional block is supplied, each matching element is passed to it
p [1,3,5,10,15].grep (1..5) {|i| i * 3} #returns [3,9,15]
```

`sort`
```ruby
p ['squirtle', 'mew', 'charmeleon', 'pikachu'].sort
p ['squirtle', 'mew', 'charmeleon', 'pikachu'].sort_by { |word| word.length}
p [2, 5, 1, 3].sort #returns [1, 2, 3, 5]

# with different data types
p [2, "hello", 1, "what"].sort #ArgumentError: comparison of Fixnum with String failed
p [2, "hello", 1, "what"].sort_by(&:to_i) #["hello", "what", 1, 2]
p [2, "hello", 1, "what"].sort_by(&:to_s) #[1, 2, "hello", "what"]
```

`any, all`
```ruby
# any
p %w(mew pikachu).any? { |word| word.length <= 3 } #returns true

#all
p %w(mew pikachu).all? { |word| word.length <= 3 } #returns false
p %w(mew cat her the).all? { |word| word.length <= 3 } #returns true
```

`reduce`
Reduce takes a collection and reduces it down to a single element. It applies an operation to each element, maintaining a running “total.”
```ruby
p (5..10).reduce(:+) #returns 45
p (1..4).reduce(:*) #returns 24
p [5, 6, 7].reduce(5, :+) #returns 23
```