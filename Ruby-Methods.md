# Other Ruby Methods

## String

`strip`
- `strip` Returns a copy of the receiver with leading and trailing whitespace removed. *Note:* **Whitespace** is defined as any of the following characters: null, horizontal tab, line feed, vertical tab, form feed, carriage return, space. 
- `rstrip` Returns a copy of the receiver with trailing whitespace removed. 
- `rstrip!` Removes trailing whitespace from the receiver. Returns the altered receiver, or nil if no change was made.
- `lstrip` and `lstrip!` similar to `rstrip` but applies to the leading whitespace. 
*Think r for right and l for left of the string.*

```ruby 
"    hello    ".strip   #=> "hello"
"\tgoodbye\r\n".strip   #=> "goodbye"
"  hello  ".rstrip   #=> "  hello"
"  hello  ".rstrip!  #=> "  hello"
"  hello".rstrip!    #=> nil
"  hello  ".lstrip   #=> "hello  "
```

`reverse /reverse!`
Returns a new string with the characters from str in reverse order. 
`reverse!` Reverses str in place.
```ruby 
"stressed".reverse   #=> "desserts"
```

`gsub / gsub!`
Returns a copy of str with all occurrences of pattern substituted for the second argument, where the pattern is typically a Regexp.
- `gsub(pattern, replacement)` → new_str
- `gsub(pattern, hash)` → new_str
- `gsub(pattern) {|match| block }` → new_str
- `gsub(pattern)` → enumerator

```ruby 
"hello".gsub(/[aeiou]/, '*')                  #=> "h*ll*"
"hello".gsub(/([aeiou])/, '<\1>')             #=> "h<e>ll<o>"
"hello".gsub(/./) {|s| s.ord.to_s + ' '}      #=> "104 101 108 108 111 "
"hello".gsub(/(?<foo>[aeiou])/, '{\k<foo>}')  #=> "h{e}ll{o}"
'hello'.gsub(/[eo]/, 'e' => 3, 'o' => '*')    #=> "h3ll*"
```

`capitalize / capitalize! / upcase / upcase!`

- `capitalize` → new_str
- `capitalize!` → str or nil
- `capitalize([options])` → new_str
Returns a copy of str with the first character converted to uppercase and the remainder to lowercase.

- `upcase` → new_str
- `upcase!` → str or nil
- `upcase([options])` → new_str
Returns a copy of str with all lowercase letters replaced with their uppercase counterparts.

```ruby 
"hello".capitalize    #=> "Hello"
"HELLO".capitalize    #=> "Hello"
"hEllO".upcase   #=> "HELLO"
```

`downcase / downcase! / swapcase / swapcase!`

- `downcase` → new_str
- `downcase([options])` → new_str
Returns a copy of str with all uppercase letters replaced with their lowercase counterparts. Which letters exactly are replaced, and by which other letters, depends on the presence or absence of options, and on the encoding of the string.

- `swapcase` → new_str
- `swapcase([options])` → new_str
Returns a copy of str with uppercase alphabetic characters converted to lowercase and lowercase characters converted to uppercase.
```ruby 
"hEllO".downcase   #=> "hello"
"Hello".swapcase          #=> "hELLO"
"cYbEr_PuNk11".swapcase   #=> "CyBeR_pUnK11"
```

`chomp / chomp!`
Returns a new String with the given record separator removed from the end of str (if present).
- `chomp(separator=$/)` → new_str
- **Will remove `\n`, `\r`, and `\r\n`, 
but not `\n` when followed by `\r`.**
> *Note:* The `\r` character is a carriage-return (CR). The `\n` character is a line-feed (LF). In most terminals \r moves the cursor to the beginning of the line without advancing lines, so you can re-write text. \n moves to the beginning and advances the line.
```ruby 
"hello\n".chomp              #=> "hello"
"hello\r\n".chomp            #=> "hello"
"hello\n\r".chomp            #=> "hello\n"
"hello".chomp("llo")         #=> "he"
"hello\r\n\r\n".chomp('')    #=> "hello"
"hello\r\n\r\r\n".chomp('')  #=> "hello\r\n\r"
```

`to_f / to_i`
> For the following, Extraneous characters past the end of a valid number are ignored.
- `to_f` → float
Returns the result of interpreting leading characters in str as a floating point number.
- `to_i(base=10)` → integer
Returns the result of interpreting leading characters in str as an integer *base* base *(between 2 and 36)*. 
- `to_r` → rational
Returns the result of interpreting leading characters in str as a rational.

```ruby 
"123.45e1".to_f        #=> 1234.5
"45.67 degrees".to_f   #=> 45.67
"thx1138".to_f         #=> 0.0
"99 red balloons".to_i   #=> 99
"0a".to_i                #=> 0
"0a".to_i(16)            #=> 10
'300/2'.to_r       #=> (150/1)
'-9.2'.to_r        #=> (-46/5)
'-9.2e2'.to_r      #=> (-920/1)
```

`to_str/ to_sym / to_a`

- `to_s` → str
- `to_str` → str
Returns self.
- `to_sym` → symbol
Returns the Symbol corresponding to str, creating the symbol if it did not previously exist.
- `to_a` → symbol
Returns the Array corresponding to a str or range.

```ruby 
"Koala".intern         #=> :Koala
s = 'cat'.to_sym       #=> :cat
s == :cat              #=> true
(1..5).to_a            #=> [1, 2, 3, 4, 5]
```

## Array

[Docs here](https://ruby-doc.org/core-2.7.1/Array.html#)
`compact / compact!`
Returns a copy of self with all nil elements removed.
```ruby 
[ "a", nil, "b", nil, "c", nil ].compact
 #=> [ "a", "b", "c" ]
```

`delete_at /delete_if`
- `delete_at(index)` → obj or nil. Deletes the element at the specified index, returning that element, or nil if the index is out of range.
- `delete_if {|item| block}` → ary
- `delete_if` → Enumerator
Deletes every element of self for which block evaluates to true.
> *Note:* The array is changed instantly every time the block is called, not after the iteration is over.
```ruby 
a = ["ant", "bat", "cat", "dog"]
a.delete_at(2)    #=> "cat"
a                 #=> ["ant", "bat", "dog"]

scores = [ 97, 42, 75 ]
scores.delete_if {|score| score < 80 }   #=> [97]
```

See also:
- `slice!(index)` → obj or nil
- `slice!(start, length)` → new_ary or nil
- `slice!(range)` → new_ary or nil
Deletes the element(s) given by an index (optionally up to length elements) or by a range.
Returns the deleted object (or objects), or nil if the index is out of range.

`fetch`
- `fetch(index)` → obj
- `fetch(index, default)` → obj
- `fetch(index) {|index| block}` → obj
Tries to return the element at position index, but throws an IndexError exception if the referenced index lies outside of the array bounds. This error can be prevented by supplying a second argument, which will act as a default value. (*See "cat" example below.*)
```ruby 
a = [ 11, 22, 33, 44 ]
a.fetch(-1)              #=> 44
a.fetch(4, 'cat')        #=> "cat"
a.fetch(100) {|i| puts "#{i} is out of bounds"}
#=> "100 is out of bounds"
```

`flatten / flatten!`
- `flatten` → new_ary
- `flatten(level)` → new_ary
Returns a new array that is a one-dimensional flattening of self (recursively). That is, for every element that is an array, extract its elements into the new array.
```ruby 
a = [ s, t, 9, 10 ]       
#=> [[1, 2, 3], [4, 5, 6, [7, 8]], 9, 10]
a.flatten                 
#=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
a = [ 1, 2, [3, [4, 5] ] ]
a.flatten(1)              
#=> [1, 2, 3, [4, 5]]
```

`sample`
- `sample` → obj
- `sample(random: rng)` → obj
- `sample(n)` → new_ary
- `sample(n, random: rng)` → new_ary
Choose a random element or n random elements from the array, with random and unique indices so  an element doesn't repeat itself unless the array already contained duplicate elements.
Could also use **`rng`**.
```ruby 
a = [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
a.sample(4)      #=> [6, 4, 2, 5]
```

`zip`
- `zip(arg, ...)` → new_ary
- `zip(arg, ...) {|arr| block}` → nil
Converts any arguments to arrays, then merges corresponding elements from each argument, resulting in a sequence of ary.size n-element arrays. If the size of any argument is less than the size of the initial array, nil values are supplied.
```ruby 
a = [ 4, 5, 6 ]
b = [ 7, 8, 9 ]
[1, 2, 3].zip(a, b)   
#=> [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
[1, 2].zip(a, b)      
#=> [[1, 4, 7], [2, 5, 8]]
a.zip([1, 2], [8])    
#=> [[4, 1, 8], [5, 2, nil], [6, nil, nil]]
```

`<<` or "shovel" operator
- `ary << obj` → ary
Append—Pushes the given object on to the end of this array. This expression returns the array itself.
```ruby
a = [ 1, 2 ]
a << "c" << "d" << [ 3, 4 ]
#=>  [ 1, 2, "c", "d", [ 3, 4 ] ]
```

`first / last`
- `first` → obj or nil
- `first(n)` → new_ary
Returns the first element, or the first n elements, of the array.
- `last` → obj or nil
- `last(n)` → new_ary
Returns the last element(s) of self.
If the array is empty, the first form returns nil or an empty array.
```ruby 
a = [ "q", "r", "s", "t" ]
a.first     #=> "q"
a.first(2)  #=> ["q", "r"]
a.last     #=> "t"
a.last(2)  #=> ["s", "t"]
```

`join`
- `join(separator=$,)` → str
Returns a string created by converting each element of the array to a string, separated by the given separator. *Note:* For nested arrays, `join` is applied recursively.
```ruby 
[ "a", "b", "c" ].join        #=> "abc"
[ "a", "b", "c" ].join("-")   #=> "a-b-c"
```


## Hash

`has_key?`
- `has_key?(key)` → true or false
Returns true if the given key is present in hsh.
```ruby
h = { "a" => 100, "b" => 200 }
h.has_key?("a")   #=> true
h.has_key?("z")   #=> false
```

`keys`
- `keys` → array
Returns a new array populated with the keys from this hash. See also #values.
- `key(value)` → key
Returns the key of an occurrence of a given value. If the value is not found, returns nil.
```ruby 
h = { "a" => 100, "b" => 200, "c" => 300, "d" => 400 }
h.keys   
#=> ["a", "b", "c", "d"]
h = { "a" => 100, "b" => 200, "c" => 300, "d" => 300 }
h.key(200)   #=> "b"
```

`merge / merge!`
- `merge(other_hash1, other_hash2, ...)` → new_hash
`merge(other_hash1, other_hash2, ...) {|key, oldval, newval| block}` → new_hash
Returns a new hash that combines the contents of the receiver and the contents of the given hashes.
```ruby 
h1 = { "a" => 100, "b" => 200 }
h2 = { "b" => 246, "c" => 300 }
h3 = { "b" => 357, "d" => 400 }
h1.merge(h2, h3)  #=> {"a"=>100, "b"=>357, "c"=>300, "d"=>400}
h1.merge(h2) {|key, oldval, newval| newval - oldval}
#=> {"a"=>100, "b"=>46,  "c"=>300}
```

`invert`
- `invert` → new_hash
Returns a new hash created by using hsh's values as keys, and the keys as values. If a key with the same value already exists in the hsh, then the last one defined will be used, the earlier value(s) will be discarded.
```ruby 
h = { "n" => 100, "m" => 100, "y" => 300, "d" => 200, "a" => 0 }
h.invert   
#=> {0=>"a", 100=>"m", 200=>"d", 300=>"y"}
h = { a: 1, b: 3, c: 4 }
h.invert.invert == h #=> true
# If invert is involutive (inverts back to the same), then no two keys have the same value. This can also be tested with h.invert.size
```

## Integer

`odd? / even?`
- `odd?` → true or false
Returns true if int is an odd number.
- `even?` → true or false
Returns true if int is an even number.


`floor / next`
- `floor([ndigits])` → integer or float
Returns the largest number less than or equal to int with a precision of ndigits decimal digits (default: 0).
- `next` → integer
Returns the successor of int, i.e. the Integer equal to int+1.
```ruby 
1.floor(2)        #=> 1
18.floor(-1)      #=> 10
(-18).floor(-1)   #=> -20
1.next      #=> 2
(-1).next   #=> 0
1.succ      #=> 2
```

`times`
- `times {|i| block }` → self
- `times` → an_enumerator
Iterates the given block int times, passing in values from zero to int - 1.
*Note:* Also referred to as `each()`
```ruby 
5.times {|i| print i, " " }   #=> 0 1 2 3 4
```

## Method
[Docs here](https://ruby-doc.org/core-2.7.1/Method.html)
###### Default values in parameters:
There are 3 types of parameters in Ruby: 
- **Required**: have to be provided with an argument or a value during method call. When calling a method, if no argument is passed it throws an error.
- **Default**: set a default value in case none is provided. Arguments with default values do not need to appear in any given order, but need to be grouped together. 
```ruby 
#method with default parameter
def chocolate(number_of_milk_packets=1) 
 print "This will be good with #{number_of_milk_packets} milk packets."
end
chocolate() #calling the method with no argument
#=> This will be good with 1 milk packets
chocolate(7) #calling the method with an argument
#=> This will be good with 7 milk packets
```
- **Optional**: aren’t always limited to a specific number of arguments. Once an optional parameter is declared on a method definition, it can use an unlimited number of arguments during a method call. All the values provided during method call are placed into single array.
```ruby
#with optional parameter
def chocolate(number_of_milk_packets*) 
chocolate(5,9,2) #method call with three arguments for optional parameter
#=> “This will be good with [5,9,2] packets”
```
*Note:* There is a conventional parameter type precedence: required parameters are the most favored, followed by default parameters then lastly optional parameters.

Referenced article [here](https://medium.com/podiihq/ruby-parameters-c178fdcd1f4e)
Other examples in [this](https://skorks.com/2009/08/method-arguments-in-ruby/) article.

## Enumerables
[Docs here](https://ruby-doc.org/core-2.7.1/Enumerable.html)
The Enumerable module provides a set of methods to traverse, search, sort and manipulate collections

`reduce` (typically for enumerables)
Combines all elements of enum by applying a binary operation, specified by a block or a symbol that names a method or operator.
```ruby 
(5..10).reduce(:+)                             
#=> 45
(5..10).reduce(1, :*)                          
#=> 151200
```

`step` (typically on a range)
[Detailed docs here](https://ruby-doc.org/core-2.6.2/Range.html#method-i-step)
- `step(n=1) {| obj | block }` → rng
- `step(n=1)` → an_enumerator
- `step(n=1)` → an_arithmetic_sequence
- `rng % n` → an_enumerator
- `rng % n` → an_arithmetic_sequence
Iterates over the range, passing each nth element to the block. If begin and end are numeric, n is added for each iteration. Otherwise step invokes succ to iterate through range elements.
```ruby 
range = Xs.new(1)..Xs.new(10)
range.step(3) {|x| puts x}
# =>
#  1 x
#  4 xxxx
#  7 xxxxxxx
# 10 xxxxxxxxxx
```

`each do`
- `each {|item| block}` → ary
Where {|item| block} could be replaced with `do block end`. Calls the given block once for each element in self, passing that element as a parameter. Returns the enumerable itself (in this example, an array.)
```ruby 
a = [ "a", "b", "c" ]
a.each {|x| print x, " -- " }
# => a -- b -- c --
```

`for in`
- Similar to `each do` above, with differences in syntax.
```ruby 
for x in a 
  print x, " -- "
end
```

`each with index`
- `each_with_index(*args) { |obj, i| block }` → enum
- `each_with_index(*args)` → an_enumerator
Calls block with two arguments, the item and its index, for each item in enum. Given arguments are passed through to each().
```ruby 
hash = Hash.new
%w(cat dog wombat).each_with_index { |item, index|
  hash[item] = index
  # Turns the input array into a hash where each item becomes a key and is attributed a value equal to its index
}
hash   
#=> {"cat"=>0, "dog"=>1, "wombat"=>2}
```

`group_by`
- `group_by { |obj| block }` → a_hash
- `group_by` → an_enumerator
Groups the collection by result of the block. Returns a hash where the keys are the evaluated result from the block and the values are arrays of elements in the collection that correspond to the key.
```ruby
(1..6).group_by { |i| i%3 }   #=> {0=>[3, 6], 1=>[1, 4], 2=>[2, 5]}
```

`map`
- `map { |obj| block }` → array
- `map` → an_enumerator
Returns a new array with the results of running block once for every element in enum. If no block is given, an enumerator is returned instead.
```ruby 
(1..4).map { |i| i*i }      #=> [1, 4, 9, 16]
(1..4).collect { "cat"  }   #=> ["cat", "cat", "cat", "cat"]
```

`select / select!`
- `select { |obj| block }` → array
- `select` → an_enumerator
Returns an array containing all elements of enum for which the given block returns a true value. 
`find_all` can also be used.
```ruby 
(1..10).find_all { |i|  i % 3 == 0 }   
#=> [3, 6, 9]
[1,2,3,4,5].select { |num|  num.even?  }   
#=> [2, 4]
[:foo, :bar].filter { |x| x == :foo }   
#=> [:foo]
```