# Auth in Rails with HTTP cookies

2 ways to buildout authentication:
- HTTP cookies
- JWT tokens (randomized encrypted tokens): requires back and front end storage and monitoring. Might not be the best fit for Rails.

Rails allows for storing that information by using `sessions` in the same way we would in an MVC, but in an API. **Note:** to use the session, it might be best to set up the server as an app rather than api, rather than start an api and then reinstall dependencies.

[Video series](https://www.youtube.com/watch?v=z18zLCAg7UU&list=PLgYiyoyNPrv_yNp5Pzsx0A3gQ8-tfg66j)

### Configuration: 

`rails db:create`

Then get the proper version of gems from [here](https://rubygems.org/): 
- `bcrypt` (currently: `gem 'bcrypt', '~> 3.1', '>= 3.1.15'`)
- `rack-cors` (currently `gem 'rack-cors', '~> 1.1', '>= 1.1.1'`)
```rb
gem 'bcrypt', '~> 3.1', '>= 3.1.15'
gem 'rack-cors', :require => 'rack/cors'
```

run `bundle`

`touch config/initializers/cors.rb`
`touch config/initializers/session_store.rb`

In `cors.rb`, we want to allow access and authorize specific domains.
We are inserting middlware with cors, and defining what we be allowed:
- where the client requests come from (origin)
- which resources are allowed,
- which headers can be used,
- which methods are allowed (will vary based on app needs) in an array
- whether we require credentials (will allow passing cookies between the front and back-end)

```rb
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins "http://localhost:3001"
    resource "*", headers: :any, methods: [:get, :post, :put, :patch, :delete, :options, :head], credentials: true
  end
  allow do
    origins "https://srg-react-app.herokuapp.com"
    resource "*", headers: :any, methods: [:get, :post, :put, :patch, :delete, :options, :head],credentials: true
  end
end
```

In `session_store.rb`,
```rb
Rails.application.config.session_store :cookie_store, key: "_auth_app", domain: "srg-react-app-api.herokuapp.com"
```

In `routes.rb`
```rb
Rails.application.routes.draw do
  root to: "static#home"
end
```

`touch app/controllers/static_controller.rb`
To make sure it works:
```rb
class StaticController < ApplicationController
    def home
        render json: {status: "It's working"}
    end
end
```

### User Model

`rails g model User email password_digest`
**password_digest* gives us two password fields:
- password
- and password confirmation 

In the generated migration file:
```rb
class CreateUsers < ActiveRecord::Migration[6.0]
  def change
    create_table :users do |t|
      t.string :email
      t.string :password_digest
      t.string :username

      t.timestamps
    end
  end
end
```

`rails db:migrate`

In `user.rb`
```rb
class User < ApplicationRecord
    has_secure_password

    validates_presence_of :email
    validates_uniqueness_of :email
end
```

In Rails console: 
```rb
irb(main):001:0> User.create!(email: "tflosse@gmail.com", password: "testpassword", password_confirmation: "testpassword")
   (0.2ms)  BEGIN
  User Exists? (10.9ms)  SELECT 1 AS one FROM "users" WHERE "users"."email" = $1 LIMIT $2  [["email", "tflosse@gmail.com"], ["LIMIT", 1]]
  User Create (1.2ms)  INSERT INTO "users" ("email", "password_digest", "created_at", "updated_at") VALUES ($1, $2, $3, $4) RETURNING "id"  [["email", "tflosse@gmail.com"], ["password_digest", "$2a$12$0xa0lP7oKY6Y/71dOPOYguJn9ynmYtlQ0UtVQt1LdW.9AJ7McqsuC"], ["created_at", "2020-08-11 03:18:21.329833"], ["updated_at", "2020-08-11 03:18:21.329833"]]
   (19.7ms)  COMMIT
=> #<User id: 1, email: "tflosse@gmail.com", password_digest: [FILTERED], username: nil, created_at: "2020-08-11 03:18:21", updated_at: "2020-08-11 03:18:21">
irb(main):002:0> 
```

Notice it takes an input for password and password_confirmation, but outputs only: password_digest: [FILTERED].


In `routes.rb`:
```rb
Rails.application.routes.draw do
  resources :sessions, only: [:create]
  root to: "static#home"
end
```

`touch sessions_controller.rb`
We call the user model to find a user by email,
then use the built-in `authenticate` method to check user password.
If the user is authenticated, render a success status, otherwise render unauthorized access status 401.
```rb
class SessionsController < ApplicationController
    def create
        user = User
            .find_by(email: params["user"]["email"])
            .try(:authenticate, params["user"]["password"])
        if user
            session[:user_id] = user.id 
            render json: {
                status: :created,
                logged_in: true,
                user: user
            }
        else
            render json: { status: 401 }
        end 
    end
end
```

### Creating an account

`routes.rb`
```rb
Rails.application.routes.draw do
  resources :sessions, only: [:create]
  resources :registrations, only: [:create]
  root to: "static#home"
end
```

`touch registration_controller.rb`
Here, we create a new user:
- "!" is used to retrn errors if any
- we can also choose to require password confirmation upon registration, if not the third element in creating a user will simply be `password_confirmation: params["user"]["email"]`
- if the user is successfully created, we start a session with this user ID and render a success status, if not we might render an unprocessable entity status.
```rb
class RegistrationsController < ApplicationController
    def create
        user = User.create!(
            email: params["user"]["email"],
            password: params["user"]["email"],
            password_confirmation: params["user"]["email_confirmation"]
        )
        if user
            session[:user_id] = user.id
            render json: {
                status: :created,
                user: user
            }
        else
            render json: { status: 500 }
        end
    end
end
```

In `application_controller.rb`:
```rb
class ApplicationController < ActionController::Base
    skip_before_action :verify_authenticity_token
end
```
Rails has a set of rules when communicating with the routes, it uses a system-generated CSRF token with the secret-key value and checks that the user is authenticated. Because we are building it as an API, we can skip that process.

We can test it with curl:
```rb
tflosse@Tamaras-MBP serge-auth-app % curl --header "Content-Type: application/json" --request POST --data '{"user": {"email": "tflosse@gmail.com", "password": "testpassword"}}' http://localhost:3000/sessions

{"status":"created","logged_in":true,"user":{"id":1,"email":"tflosse@gmail.com","password_digest":"$2a$12$0xa0lP7oKY6Y/71dOPOYguJn9ynmYtlQ0UtVQt1LdW.9AJ7McqsuC","username":null,"created_at":"2020-08-11T03:18:21.329Z","updated_at":"2020-08-11T03:18:21.329Z"}}% 
```
##### Adding additional Routes

`Routes.rb` - adding routes to log out and verify that the user is still logged in.
```rb
Rails.application.routes.draw do
  resources :sessions, only: [:create]
  resources :registrations, only: [:create]
  delete :logout, to: "sessions#logout"
  get :logged_in, to: "sessions#logged_in"
  root to: "static#home"
end
```

Before we create the corresponding route in `session_controller.rb`, we want to define the current user concern (method that will define how we check for the current user).

`touch current_user_concern.rb` 
in app > controllers > concerns

```rb
module CurrentUserConcern
    extend ActiveSupport::Concern
    
    included do
        before_action :set_current_user
    end

    def set_current_user
        if session[:user_id]
            @current_user = User.find(session[:user_id])
        end
    end
end
```

Now, in `sessions_controller.rb`:

```rb
class SessionsController < ApplicationController
    # import the concern
    include CurrentUserConcern

    # def create
    #     user = User
    #         .find_by(email: params["user"]["email"])
    #         .try(:authenticate, params["user"]["password"])

    #     if user
    #         session[:user_id] = user.id 
    #         render json: {
    #             status: :created,
    #             logged_in: true,
    #             user: user
    #         } 
    #     else
    #         render json: { status: 401 }
    #     end
    # end

    #checks to see if current user is available
    def logged_in
        if @current_user
            render json: {
                logged_in: true,
                user: @current_user
            }
        else
            render json: { 
                lofgged_in: false 
            }
            # no status needed becaus eit is a get request
        end
    end

    def logout
        reset_session
        render json: { status: 200, logged_out: true}
    end
end
```

#### Session initializer

Need to add one last piece to be able to have auth working in both production and development on local servers.

In `session_store.rb`:
```rb
if Rails.env == "production"
Rails.application.config.session_store :cookie_store, key: "_auth_app", domain: "srg-react-app-api.herokuapp.com"
else
    Rails.application.config.session_store :cookie_store, key: "_auth_app"
end
```

### Connecting to React













