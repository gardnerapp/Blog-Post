Checkout the video here -> https://www.youtube.com/watch?v=kYjukGyZChU&t=124s

I’m the type of person who gets a thrill writing clean, idiomatic and DRY code. Recently I’ve been working on a project that required me to write a ton of API calls, this was a very exciting for me because I’ve never really used Ruby to network.

Unfortunately the native networking Modules & Classes of Ruby are very verbose and difficult to use on the fly. On top of all that they require writing a lot of code which slows down development time and makes code more difficult to refactor.

Luckily Rubyist are very intuitive, there are a ton of networking gems out their. Within five minutes of reading the official website of the Faraday Gem I fell in love. Utilizing Faraday to make HTTP request allowed me to reduce 10–15 lines of code all the way down to ~3 lines. Here is how to make GET and POST request with Faraday:
```
require 'faraday'response = Faraday.get "https://www.youtube.com/channel/UCfd8A1xfzqk7veapUhe8hLQ"puts "Status => #{response.status}"
puts "Headers => #{response.headers}"
puts "Body => #{response.body}"params = {book: {title: "foo", author: "bar"}} 
headers = {Content-Type: 'application/json'}response = Faraday.post "localhost:3000/books", params, headers puts "Status => #{response.status}"
puts "Headers => #{response.headers}"
puts "Body => #{response.body}"
```
As you can see the Faraday gem is extremely intuitive, all of the methods are exactly what you’d think they would be. By using Faraday we no longer need to deal with initializing multiple objects from the NET and HTTP classes. For more info on Faraday check out the official usage documentation. As always thanks for reading and please be sure to subscribe to my podcast and YouTube channel for more Ruby content.
