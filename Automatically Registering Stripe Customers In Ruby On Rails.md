Checkout the Video for this post here:

https://www.youtube.com/watch?v=N-4GuzvfHlA&list=PLl8zUt1K8heVjKBGEWvHSb_P1jBxylbrv&index=2

Before we accept payments or subscriptions in Stripe we need register our users as customers in the stripe API. For this tutorial I’m already assuming that you have a user model and already have Stripe setup within your project.

Every time we create a user we’re going to create a corresponding Stripe Customer object and save the customer id to our user model. First lets add a stripe_id to our user model:

```
rails g migration AddStripeIdToUsers stripe_id:string
rails db:migrate
```

Next we’re going to create a method that makes a call to the Stripe API to create an instance of Customer. Once we get our response back we’ll update our stripe_id field.

```
def create_stripe_customer
     customer = Stripe::Customer.create({
     email: self.email })
     
    update_attribute :stripe_id, customer['id']
end

```
To run this method every time we create a new User we can use the after_create callback like this:

```
after_create :create_stripe_customer
```
