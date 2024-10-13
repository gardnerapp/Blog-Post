Signing user up, in and out are nearly universal features for every type of app. In this series I’ll teach you how to build a simple authentication system. Part 1 will cover the basics of API calls and models. In part 2 I’ll teach you how to store authenticated users inside your app with the Cubit package and in part 3 we’ll be learning how to maintain sign-in after our app closes by using the shared preferences package.


Be sure to checkout my Flutter UI Tutorials !
Creating a Base API class:

Our first step is to build a BaseAPI class to hold all of the URL’s of our API. In my How To Make Flutter API Calls Easy I taught you how to use class inheritance as a means of simplifying and organizing your API calls. This class isn’t to complex it just stores the routes we will be requesting, check out the code below.

```
Class BaseAPI{    static String base = "http://localhost:3000"; 
    static var api = base + "/api/v1";
    var customersPath = api + "/customers";
    var authPath = api + "/auth"; 
   // more routes   Map<String,String> headers = {                           
       "Content-Type": "application/json; charset=UTF-8" };                                      
              
}
```
Ultimately creating our Base class makes it easier for us to manage our API endpoints.
Creating A Customer API Class

Next we’re going to create a class to store all of the API calls for customer authentication.

We’ll make request using darts HTTP library, any data we send will be encoded in JSON format. Each request will return a Future of type HTTP response. Inside of our widgets we’ll be using the Response’s statusCode attribute to determine if our calls were successful.
```
import 'dart:convert';

import 'package:resteraunt_starter/api/BaseAPI.dart';
import 'package:http/http.dart' as http;

class AuthAPI extends BaseAPI {  Future<http.Response> signUp(String name, String email, String phone,
      String password, String passwordConfirmation) async {
    var body = jsonEncode({
      'customer': {
        'name': name,
        'email': email,
        'phone': phone,
        'password': password,
        'password_confirmation': passwordConfirmation
      }
    });

    http.Response response =
        await http.post(super.customersPath, headers: super.headers, body: body);
    return response;
  }

  Future<http.Response> login(String email, String password) async {
    var body = jsonEncode({'email': email, 'password': password});

    http.Response response =
        await http.post(super.authPath, headers: super.headers, body: body);

    return response;
  }


  Future<http.Response> logout(int id, String token) async {
    var body = jsonEncode({'id': id, 'token': token});

    http.Response response = await http.post(super.logoutPath,
        headers: super.headers, body: body);

    return response;
  }

}
```
Now it’s time to create our Customer class!
Creating a Customer Object

When we create an object we are creating our own data type, we’re creating a blue print that outlines all the properties that each of our customers will have.
```
import 'dart:convert';

class Customer{
  int id;
  String email;
  String phone;
  String name;
  String token;

  User({this.id, this.email, this.phone, this.name, this.token});

  factory Customer.fromReqBody(String body) {
    Map<String, dynamic> json = jsonDecode(body);

    return Customer(
      id: json['id'],
      email: json['email'],
      name: json['name'],
      phone: json['phone'],
      token: json['token'],
    );

  }

  void printAttributes() {
    print("id: ${this.id}\n");
    print("email: ${this.email}\n");
    print("phone: ${this.phone}\n");
    print("name: ${this.name}\n");
    print("token: ${this.token}\n");
  }

}
```
The first thing we did was create class constructors to initialize new instances of our Customer objects. We use the factory constructor because there might be a time when we don’t need to create an entirely new instance of our class. Our factory method will receive a JSON object, from our API call request body, which we will decode into a Map of type(s) String & dynamic. From their it’s only a matter of setting our Customer attributes to their corresponding keys in the map. Lastly the printAttributes() helper method will print out all of the attributes and their values, this is very useful for debugging.
In Our Widgets
../authentication/auth.dart
```
class Auth extends StatefulWidget {
  @override
  _AuthState createState() => _AuthState();
}

class _AuthState extends State<Auth> {
  bool showSignUp = true;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
            title: Text(
              "Corey's Corner",
            ),
          elevation: 16.0,
          actions: [
            IconButton(
                icon: Icon(Icons.swap_horiz),
                onPressed: () {
                  setState(() {                    showSignUp = !showSignUp;
                  });
                })
          ],
        ),
        // ternary operator 
      body: Container(child: showSignUp ? SignUp() : SignIn()));
  }
}
```
In this widget we are setting up a container to hold both our Signup() & SignIn() widgets. We use a boolean to toggle back and forth between the different pages, this prevents use from having to write push functions to get to different pages.
../authentication/sign_in.dart

For the sake of brevity I’m going to leave all form, text and button styling out of the picture and this tutorial will only cover the signIn page.
```
class SignIn extends StatefulWidget {

  @override
  _SignInState createState() => _SignInState();
}

class _SignInState extends State<SignIn> {
  AuthAPI _authAPI = AuthAPI();
  final _key =  GlobalKey<FormState>();
  String email;
  String password;
  @override
  Widget build(BuildContext context) {

    return  Container(
        padding: EdgeInsets.symmetric(vertical: 20.0, horizontal: 25.0),
        child: Form(
            key: _key,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.start,
              children: <Widget>[
                SizedBox(height: 70),
                Text("Sign In", style: formTitleStyle(),),
                SizedBox(height: 30),
                Container(
                    width: 400,
                    child: TextFormField(
                      decoration: textInputDecoration("Email", context),
             onChanged: (val) => setState(() => email = val),
                    )
                ),
                SizedBox(height: 30),
                Container(
                  width: 400,
                  child: TextFormField(
                    obscureText: true,
                    decoration: textInputDecoration("Password", context),
                    onChanged: (val) => setState(() => password = val),
                  ),
                ),
                SizedBox(height: 25),
                GestureDetector(
                  child: Text("Forgot Password ?", style: TextStyle(
                      fontSize: 18.0,
                      decoration: TextDecoration.underline
                  ),),
                  onTap: (){
                  // todo 
                  },
                ),
                SizedBox(height: 25),
                Container(
                    width: 400,
                    child: customRaisedIconButton("Sign In !", Icons.send, context, () async {
                      if(_key.currentState.validate()){
                        try{
                          var req = await 
                       _authAPI.login(email,  password);
                          if(req.statusCode == 200){print(req.body);
var customer = 
                              Customer.fromReqBody(req.body);customer.printAttributes();
                        Navigator.push(context, MaterialPageRoute(
                         builder: (context) => MyHomePage(customer:                 customer)));
                          } else {
                            pushError(context);
                          }
                        } on Exception catch (e){
                        print(e.toString());
                        pushError(context);
                        }
                      }
                    })
                )
              ],
            )
        )
    );
  }
void PushError(){
    Navigator.push(context, MaterialPageRoute(
        builder: (context) => Error()
    ));
}
```
The first thing we do is create to Strings email & password within state. In side of our text forms we call setState to set the stateful fields to the values our customer types in. Before our API call we’ll use validators to ensure that our email and password aren’t bank so we don’t make any necessary API calls.
```
AuthAPI _authAPI = AuthAPI();
```
In this line of code we initialized an instance of our AuthAPI object and store it in a variable.

Our API call is asynchronous because we have to wait for our data. We use the await statement to wait for our request. Asynchronous programming allows our code to execute non-linearly. We wrap our call in a try statement to catch any errors and we call our login function and pass it the objects stored in state with line of code:
```
var req = await _authAPI.login(email,  password);
```
Once we receive our request we use its statusCode attribute to decide what to do next. If our code reads 200 we pass the request body attribute, which is of type JSON to the factory constructor of our Customer model. From there we print out our users new attributes, the request body and push to home. If we don’t receive the proper statusCode or we catch an exception we push to an error page.

Thanks for reading! in the next post we’ll discuss how to use Cubits to store our customer in our app making it available to all of our widgets.

Be sure to checkout Part II and III !
