Whats a Block?

In Ruby a block is an anonymous functions that is passed as an argument for a method/function.By using blocks functions and class methods can take on a larger number of use cases. When we call a block we’re essentially writing a piece of code that is writing (repurposing) a function on the fly.

Some people may find this to be debatable but blocks are an example of Meta Programming. They allow us to write a program that redefines the source code of the functions. Blocks prevent programmatic redundancy, rather than writing a million different methods we can write one method and pass it an infinite number of blocks. So how do we use blocks?

If you like learning via video check out the video I made on blocks Here & Here !
Using Blocks

There are two ways of writing a block: with the curly braces syntax or a do/end statement. The curly braces syntax is best used for blocks with one line of code whilst the do/end structure is optimal for longer multi line programs. Just like functions they can also take arguments. Arguments are surrounded by the pipe ( || ) characters and separated by commas.

The each method allows us to iterate over each element in a List object. The each method takes a block with a parameter, in this case it is named word. This parameter will represent the current element being iterated over, during each iteration our block will be called.
```
# One Liner == Braces syntax 
%w[coreys corner podcast].each {|word| puts word}# Multiline == do/end %w[the best podcast around].each do |word|
   puts word 
   puts word.capitalize
   puts "#{word} includes hog!!" if word.include? "hog"
end 
```
Writing Block Based Functions

If writing blocks is a way of passing code to a function why can’t we just pass in code as an argument to a function? Well we could pass in a line of code as an argument to a function. Lets do that and see what happens.
```
def does_not_work(code)
   puts "Inside of function"
   code # does not print in proper order 
   puts "Inside of function
end
does_not_work(puts "hello"
)>> hello
>> Inside of function
>> Inside of function
```
As you can see the program did not as we would’ve expected. Passing lines of code as an argument just does not work, which is one of the reasons why we need to use blocks. Even if we could pass in a line of code as an arguments and it worked that would mean that we would need to create a parameter for each line of code we want to use as an argument. This hypothetical scenario would obviously be very cumbersome, non-dynamic and likely lead to errors. Blocks make it possible to dynamically execute multiple lines of code within a function.

Lets write a function that takes a block as an argument, in this example we’ll do so implicitly, our block is not a named parameter in our function arguments. In the example below we use the yield keyword to run/call/execute the block.
```
# Implicit block with yield as call method 
def takes_a_block
   puts "Inside of function"
   yield
   puts "Inside of function
"endtakes_a_block {puts "Inside of block"}takes_a_block do 
   puts "Hey I'm inside of a block'
   puts "Wow this is so cool" 
end 
```
If we want to explicitly specify a block as a named function parameter we can simply define it with the ampersand symbol (&) as show below.
```
def my_func(&my_block) #Explicitly defined block
   my_block.call # Equivalent to yield 
end
```
Rather than using yield to run the block we use the call method to execute it. There is no difference between using yield or call aside from the fact that we can only use the call method on an explicitly defined block.

The yield keyword can be used as many times as you want in a function. To allow a block to take a parameter all we do is pass is as an argument to yield. Any argument passed to yield will be treated as the parameter(s) we specify in our block. Leaving parameters out or including to many will not cause an error, but an extra argument will be treated as a nil object.
```
def some_data_types(&block)
   yield :symbol
   yield "string"
   yield(1)
   block.call([])
   block.call {}
end # n represents :symbol, "string" etc as our block is calledsome_data_types { |n| puts "#{n} is of type #{n.class}" }some_data_types do |n, x| #extra arg does not cause error
   puts "#{n} responds to .each_byte" if n.respond_to? :each_byte
   puts "#{n}.object_id == #{n.object_id}"
   p x # extra arg treated as nil
end
```
Dynamic Blocks

The `block_given?` method is useful for preventing our program from crashing, especially when dealing with implicit blocks. If a block is passed to our function block_given returns true else it returns false, which allows us to dynamically call yield.
```
def foo
   if block_given? then yield else puts "No block given" end
endfoo 
>> "No Block Given"foo "The Corey's Corner Podcast is crazy" 
>> "The Corey's Corner Podcast is crazy"
```
To summarize all we have learned blocks are a way of writing anonymous functions on the fly and passing them as arguments to functions/methods. They make Ruby language more flexible and dynamic which makes it easier for us to program with a high level of abstraction.
