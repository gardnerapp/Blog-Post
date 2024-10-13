Active Storage is a tool which allows us to attach files to our Rails Models. These attachments are dynamic, they can be anything we want form PDFs to JPG files. In this tutorial I’ll teach you the basics of uploading files with Active Storage and how to use attachments in an API setting.


Installing Active Storage

First add this to your GemFile

```
gem 'activestorage'
```

Then from the console run

```
rails active_storage:install
```

followed by

```
rails db:migrate
```

Active Storage uses three tables in your application’s database named active_storage_blobs, active_storage_variant_records and active_storage_attachments

In other words active storage creates a series of tables in our database to associate uploaded files and our models.
Creating Our Scaffold

Lets create a User Scaffold

```
rails g scaffold User name
```

Next we’re going to want to attach an avatar image to our use from in models/user.rb add

```
has_one_attached :avatar
```

This line of code simply attaches one of our active storage blobs which we’ll call avatar_image to our User model.
Uploading Avatars

To add an Avatar when creating our user we need to make some changes to our User form and its controller parameters.

Add the following to your _form file:

```
<div class=“field”>
    <%= form.label :avatar %>
    <%= form.file_field :avatar %>
</div>
```

Lets modify our controller params so our avatar uploads don’t get filtered out.

```
def user_params
     params.require(:user).permit %i[name avatar]
end 
```

Using Avatars Inside Of Views

Having User avatars isn’t any good if we can’t see them ! Here is how we render images inside of our views.

```
<%= if @user.avatar.attached? %> 
        <%= image_tag url_for(@user.avatar) %>
<% end %>
```

We use the attached? method to determine if our avatar is attached, this way we don’t run into any errors by calling a nil object. If there is an image all we have to do is call the url_for method on the avatar and pass that into an image_tag helper.
API Mode

In this scenario our Rails app is acting as the API for a mobile application, our mobile app needs to be able to show our User avatars on the front end. Mobile frameworks can show images in the same way that HTML does, all they need is the url where the image is located.

First lets create our API controller by running

```
rails g controller api/v1/users
```

V1 just stands for version 1, versioning our API allows it to service multiple versions of the mobile application simultaneously.

We need to specify that our controller is running in API only mode by changing

```
class API::V1::UsersController < ApplicationController
```

To

```
class API::V1::UsersController < ActionController::API
```

Check out the full controller below

```
class API::V1::UsersController < ActionController::API     def index
        @users = User.all.joins(:avatar_attachment)
        render json: @users.map { |user| 
           user.as_json(only: %i[name]).merge(
           avatar_path: url_for(user.avatar) }  
     end     def show
       @user = User.find(params[:id])
       if @user.avatar.attached? 
          render json: @user.as_json(only: %i[name]).merge(
                      avatar_path: url_for(@user.avatar)
       else 
         render json: @user.as_json only: :name
      end 
   end
 end 
```
In our index action we use an SQL JOIN Statement to grab all of our users that have avatar attachments. Then we map over our users, rendering each as a JSON object. We omit by rendering unnecessary data such as created_at and updated_at by passing the as_json only parameter with a list of symbols for the properties we want to grab. Then we use the merge method to create a key/value pair of the url for our avatar.

In our show method we simply find our user, if there is an avatar attached we render the url of the avatar by using the merge statement. If no image is attached we continue.

If in our index we wanted to render all users regardless of if there is or isn’t an avatar attached we could do something like this.

```
def index 
   
   @users = User.all 
   render json: @user.map{ |user| 
                 if user.avatar.attached? 
                   user.as_json(only: :name).merge(
                        avatar_path: url_for(user.avatar)
                 else 
                    user.as_json only: :name
                 end
end 
```

Because Active Storage doesn’t currently support validations it is very important that we use the attached? method to determine if files are attached, this way we avoid errors. Thanks for Reading !
