Check out the video for this post here https://www.youtube.com/watch?v=bFSl8OQym0k&t=3s

Over the lifecycle of application development people tend to procrastinate or blow off writing test. I’m sure that many new engineers, like my former self, feel that writing test is not important and that it can even be cumbersome to actually getting a product shipped. On top of all that testing is easy to forget about because it can be very boring and repetitive. Luckily in this series we’re going to challenge ourselves and make writing test fun by Meta Programming them.

In a professional environment you need to have test in order to properly refactor your code and ensure that your application does break in the event that you need to update the language or framework that it runs on.

Imagine if you have to upgrade your app to Rails 7 and Ruby 3.0. If you haven’t written any test than only way for you to ensure that your application is working properly is testing it by hand, tediously going through every page and searching through every link and input form. This would be awful, luckily we can write test which automates the entire process for us.

The process of editing code is known as refactoring. Often we want to change the form of our code (what it looks like) without changing its function (what it does).

There’s a lot of different ways to do the same thing, you might write some decent code and then come back with some fantastic code the next day and decide to change things up. Testing is perfect in this type of scenario because it ensures that our program achieves the desired output no matter what it looks like.

Luckily with the Ruby language we can Meta Program our test ie. write code that writes test. This rapidly decreases the amount of time we need to spend on writing test and makes testing our applications a whole lot more interesting. On top of all that our test take up far fewer lines of code when we choose to utilize Meta Programming. Alright, lets get to the fun part !
The Lame Way Of Testing Model Validations

Lets assume we have a model called Restaurant which has a few attributes: name, address, phone number, category, and closing time. We want all of these attributes to be required for our model to be valid. Typically we’d do something like this:
```
#... /models/restaurant.rb
class Restaurant < ApplicationRecordvalidates :name, presence: true
validates :address, presence: true
validates :phone_number, presence: true
validates :category, presence: true
validates :closing_time, presence: trueend # 5 lines of code 
```
Our model test file would look like this:
```
#... test/models/restaurant_test.rb
class RestaurantTest < ActiveSupport::TestCase
   def setup
      @restaurant = restaurants :first
   end    test 'Name should be present' do
      @restaurant.name = nil 
      assert_not @restaurant.valid?
   end   test 'Address should be present' do
      @restaurant.address = nil 
      assert_not @restaurant.valid?
   end   test 'Phone Number should be present' do
      @restaurant.phone_number = nil 
      assert_not @restaurant.valid?
   end   test 'Category should be present' do
      @restaurant.category = nil 
      assert_not @restaurant.valid?
   end   test 'Closing Time should be present' do
      @restaurant.closing_time = nil 
      assert_not @restaurant.valid?
   end
end # 4 lines of code per attribute test, 5 attributes 5*4 = 20 lines
```
To write and test a simple presence validation we have to write 5 lines of code, 1 line for the validation and another 4 for testing it. Presence validations are one of the easiest things to write & test but they still require a lot of code, and they are very boring to write.

If you had 5 models with 5 attributed each you’d have to write 125 lines of code 😤 🤕 😠. Who in their right mind wants to write or look through all that boring code and repetitive code? If your running a startup do you really want to be using your engineers time and attention on this?

So how can we make our lives easier, use our time more wisely, write less code, make more money and make testing more interesting all in one swoop.
TDD + Meta Programming: A Dynamic Duo

We’ll take a Test Driven Development approach by writing failing test and refactoring our application code until our test are green. If you noticed before all of our test followed a similar pattern or formula (summarized below). Every test name is prefixed with the name of the attribute, the attribute is then set to nil on our instance variable and lastly we assert that our variable is not valid with it’s null attribute.
```
test 'attribute is present' do
   @restaurant.attribute = nil 
   assert_not @restaurant.valid?
end
```
Let’s start by creating a list of all the attributes that must be present. We’ll then use the each method to loop over each attribute, passing in a block which is where the actual testing happens. Naming the test is simple enough all we have to do is use string interpolation.

So, how do we set the attribute(s) on our instance variable to nil ? Originally I thought of using the send method but that made zero sense. I was about to give up but then I realized that the Ruby Kernel method eval will evaluate a string as if it were a piece Ruby code, thus we’re able to treat data as code.

My final test(s) looked like this:
```
#... test/models/restaurant_test.rb%
i[name phone_number address category closing_time].each do |attr|
   test "#{attr} must be present" do 
      eval "@restaurant.#{attr} = nil"
      assert_not @restaurant.valid?
   end
end # 6 lines of code 70% reduction in code volume ;)
```
At this point our test should be red. Because model validations are so repetitive they are a clear candidate for being meta programming. Once again we’ll loop over a list of symbols, inside of the block we’ll use eval and the validates_presence_of helper to run the validations. The code is as follows:
```
#... /models/restaurant.rb
%i[name phone_number address category closing_time].each do |attr|
     eval "validates_presence_of #{attr}"
end # 3 lines of code 60% reduction in code volume
```
Conclusion

Meta Programming is a practice where you write code that writes code, essentially we’re automating programming. Any time that we’re writing repetitive or pseudo similar code is a sign that we can use Meta Programming to keep things DRY.

Meta Programming allows us to write more code at a faster rate with less lines of code. This makes our programs easier to read, write and debug. In a professional developer that can utilize Meta Programming properly will be able to get more done in less time. By Meta Programming my model validations and their test I was able to achieve a 60–70% reduction in code volume.

To learn more about Meta Programming be sure to check out my Beginners Ruby Meta Programming Tutorial, the Advanced Meta Programming Tutorial and the Meta Programming a Ruby on Rails E-Commerce Application. And don’t forget to listen to Corey’s Corner, Thanks !
