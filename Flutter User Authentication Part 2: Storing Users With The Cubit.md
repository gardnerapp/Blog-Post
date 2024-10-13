In the last tutorial I taught you how to build a Customer model and API for authentication. To make our Customer objects available in our widgets we’ll be using Cubit’s from the flutter_bloc package (be sure to install this package before following along). Let’s get started !

What is a Cubit?

Cubits are a subset of the BLOC architecture. I think of Cubits as a form of state that exist throughout the application. Cubits are a lot easier to work with than the Blocs because they rely on methods rather than streams to process state changes, ultimately this amounts to writing a lot less code.
Creating Our Cubit

In regard to authentication we’ll need to log users in and out. Within our cubit class we’ll create methods to handle these events and ultimately change the state of our Cubit.

```
class CustomerCubit extends Cubit<Customer>{ 
 CustomerCubit(Customer state) : super(state); 

  void login(Customer customer) => emit(customer);
  
  void logout() =>  emit(null);
  
}
```

In the first line we’re creating our own cubit object with a state of type Customer. The second line basically says that if our Cubit isn’t storing a user it will inherit the state of its parent, which is null.

We create two methods login and logout. In each method we set the state of our cubit by calling emit, which is essentially a version of setState() for the Cubit class. Any class that inherits (extends) from Cubit will have access to emit. To log a Customer in we pass emit an instance of Customer and to log a user out we pass null.

In less than 5 lines of code we’ve laid the foundation for storing our Customer object in the application.
Working With The Cubit

To make our Cubit available throughout our app we’ll have to wrap our Material App Widget in a BlocProvider:

```
void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MultiBlocProvider(
      providers:  [
        BlocProvider<CustomerCubit>(
        create: (BuildContext context) => CustomerCubit(null)
        ),
      ],
      child: MaterialApp(
        title: 'Corey's Corner',
        theme: ThemeData(
          splashColor: Colors.orange,
          primarySwatch: Colors.orange,
          appBarTheme: AppBarTheme(elevation: 16.0),
        ),
        home: MyHomePage(),
      ),
    );
  }
}
```

I like to start off with a MultiBlocProvider in case I ever need to add more Blocs or Cubits in later.

We’re basically just telling our provider what type of class it’ll be looking for and telling it what the initial state should be, which in this case would be null because no user is initially logged in.
Using The Cubit After Our API Call

After our API request we’re going to need to call the login method we built earlier, check out the code below:

```
if(req.statusCode == 202){
    var customer = Customer.fromReqBody(req.body);
    BlocProvider.of<CustomerCubit>(context).login(customer); 
// a lot more stuff
}
```

Assuming we get the correct response code from our back end request we’ll access the provider of the cubit we’d like to use and call the login method with the Customer we created from our decoded JSON response as an argument. Congrats you just successfully used a Cubit !
Using BlocBuilder Widgets

We’re going to want to build widgets based on the State our Cubit is emitting. For example suppose we had a navbar with some buttons. Ultimately our application layout will depend on the state of the cubit.

```
class MyHomePage extends StatelessWidget {
  const MyHomePage({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    AuthAPI _authAPI = AuthAPI();
    return BlocBuilder<CustomerCubit, User>(
      buildWhen: (previous, current) => previous != current,
        builder: (BuildContext context, Customer state){
            return Scaffold(
                   appBar: AppBar(
                    title: Text("Corey\'s Corner"),
            leading: state == null ?
            IconButton(icon: Icon(Icons.login),
              onPressed: () {
                Navigator.push(context,
                    MaterialPageRoute(builder: (context) =>     Auth()));
              },
            ) : null, 
//more stuff
```

Inside of our widgets build method we return a bloc builder which will always rebuild whenever our state changes. The builder parameter is passed a function which takes the current BuildContext and state of our cubit as arguments.

Now we can call the state function variable to access the data inside of our cubit. In the code above we use the ternary operator to build an IconButton that routes to the login page if our state is null (no Customer logged in). If our state isn’t null (logged in user) we obviously don’t build anything.

That’s it for this tutorial ! Stay tuned for the next release because we’re going to be using the shared preferences package to maintain user login even when our app closes. Thanks for reading and be sure to checkout Part III.
