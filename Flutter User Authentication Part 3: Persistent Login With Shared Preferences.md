In part I of this series I taught you how to build a Customer model and an authentication API and in part II we learned how to use a Cubit to manage login state throughout our application. Now, in the final part of this series we’re going to learn how to maintain persistent login even when our app closes. Let’s get started !

Be sure to checkout my Flutter UI Tutorials !
Shared Preferences

According to the official docs the Shared Preferences is a library that

    Wraps platform-specific persistent storage for simple data (NSUserDefaults on iOS and macOS, SharedPreferences on Android, etc.). Data may be persisted to disk asynchronously, and there is no guarantee that writes will be persisted to disk after returning, so this plugin must not be used for storing critical data.

Essentially this package will allow us to store and retrieve information to/from the disc of the device that our application is running on.

Note that the Shared Preferences package specifically says that critical data should not be stored this way. The application we’re building is only for production and demonstration purposes. If you’d like to maintain persistent login in a production environment I recommend using the flutter_secure_storage package which operates in a very similar manner.
The Game Plan

When a user logs into our application we’re going to store their API Token and ID to disc. Every time the application is opened we’ll check to see if these values are present. If they are present we’ll make an API request, then we’ll take the data to create an instance of our Customer model log our customer in via the Cubit.
Saving Data With Shared Preferences

It’s time to create a function to save the API Token and ID of our customers to disc:
```
../prefs.dart
import 'package:shared_preferences/shared_preferences.dart';/* 
don't forget to add shared_preferences to pubspec.yaml before following along
*/void upDateSharedPreferences(String token, int id) async {
  SharedPreferences _prefs = await SharedPreferences.getInstance();
  _prefs.setString('token', token);
  _prefs.setInt('id', id);
}
```
Shared Preferences operate like a Map or Dictionary that holds holding key/value pairs which are type specific. First we create an instance of Shared Preferences ie. _prefs. Then we use the setString and sentInt methods to save an integer and string to disc. The first argument in each of the set functions is the key we are storing whilst the second is the value associated with each key.
Updating Shared Preferences After Login

Recall how in part 1 we created a login form which makes an API call. Upon a successful API request we use a factory method to create an instance of our customer model from the JSON data returned from our API call. After successfully creating an instance of Customer all we have to do is call the function we just made to update our Shared Preferences with the ID and Token of the Customer whom just logged in. Below is an example of what our sign in and sign up functions would look like:
```
../sign_in.dart
Container(
    width: 400,
    child: customRaisedIconButton("Sign In !", Icons.send, context, () async {
      if(_key.currentState.validate()){
        try{
          var req= await _authAPI.createSession(email, password);
          if(req.statusCode == 202){
            var customer = Customer.fromReqBody(req.body);//checkout part II & II if you're not familiar with BlocProvider or //Cubits            BlocProvider.of<CustomerCubit>(context).login(customer);
            upDateSharedPreferences(customer.token, customer.id);
           Navigator.push(context, MaterialPageRoute(builder: (context) => MyHomePage(
           )));
          } else {
            pushError(context);
          }
        } on Exception catch (e){
          pushError(context);
        }
      }
    })
```
Persistent Login

This is the moment you’ve been waiting for… achieving persistent login. Before rendering the root widget of our application, in this case HomePage, we need to check to see if shared preferences are present.

We call this asynchronous function checkPrefsForUser, once again we create an instance of Shared Preferences. Then we use the getString and getInt methods to retrieve the saved data. If the data is not present no user was logged in and these values will be of null type.

Once we determine if these values are present we make an API call to finish retrieving all of the JSON data about our customer from the backend. Once again we use a factory constructor to create an instance of Customer and manage global login state with the Cubit package. Just to be safe we repopulate our Shared Preferences with the ID & Token from our Customer.

We only run this function before rendering our HomePage Widget if the state of our Customer BlocBuilder is null.
```
class MyHomePage extends StatelessWidget {
  const MyHomePage({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    AuthAPI _authAPI = AuthAPI();
    return BlocBuilder<CustomerCubit, Customer>(
      buildWhen: (previous, current) => previous != current,
        builder: (BuildContext context, Customer state){
          checkPrefsForUser() async {
          SharedPreferences _prefs = await          SharedPreferences.getInstance();
          var _sharedToken = _prefs.getString('token');
          var _sharedId = _prefs.getInt('id');
          if(_sharedToken != null && _sharedId != null){
            try{
              var req = await _authAPI.getUser(_sharedId, _sharedToken);
              if(req.statusCode == 202){
                var user = User.fromReqBody(req.body);
                BlocProvider.of<UserCubit>(context).login(user);
                upDateSharedPreferences(user.token, user.id);
              }
            }on Exception catch (e){}
          }
        }
        if(state == null){
          checkPrefsForUser();
        }
      return Scaffold(
```
Logging Users Out

To log users out we just need to set our Cubit to nil, make our API request and remove the values we stored earlier from our Shared Preferences. Here’s an example of an IconButton that we would use to do that:
```
IconButton(
    icon: Icon(Icons.exit_to_app),
    onPressed: () async {
      SharedPreferences _prefs = await SharedPreferences.getInstance();
      _prefs.remove('id');
      _prefs.remove('token');
      try {
        var req = await _authAPI.destroySession(
            state.id, state.token); /* note state refers to the 
Customer from the cubit of our bloc builder
*/        if (req.statusCode == 202) {
          BlocProvider.of<CustomerCubit>(context).logout();
        } else {
          pushError(context);
        }
      } on Exception catch (e) {
        print(e);
      }
    })
```
