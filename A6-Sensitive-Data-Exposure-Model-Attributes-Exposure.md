# Description

The application's API returns a model object (user or users). Using respond_with, the API returns the full model object. It is simple but exposes information such as the user's password and other user attributes that you may wish to keep invisible.

# Bug

Within app/controllers/api/v1/users_controller.rb:

```ruby
def index
  # We removed the .as_json code from the model, just seemed like extra work.
  # dunno, maybe useful at a later time?
  #respond_with @user.admin ? User.all.as_json : @user.as_json
  
  respond_with @user.admin ? User.all : @user
end

def show
  respond_with @user.as_json
end
```

The as_json method referenced in the comments section of the index action exists within the user model in order to override and safely protect our model from only rendering certain attributes. It is unused (commented out), app/models/user.rb:

```ruby
# Instead of the entire user object being returned, we can use this to filter.
def as_json
  super(only: [:user_id, :email, :first_name, :last_name])
end
```

The as_json method referenced in the comments section of the index action exists within the user model in order to override and safely protect our model from only rendering certain attributes. It is unused (commented out), app/models/user.rb:

    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    X-UA-Compatible: IE=Edge
    ETag: "6b4caf343a20865de174b2b530b945dd"
    Cache-Control: max-age=0, private, must-revalidate
    X-Request-Id: c3b0a57861087c0b827aab231747ef0c
    X-Runtime: 0.051734
    Connection: close
    
    {"admin":false,"created_at":"2014-01-23T16:17:10Z","email":
    "jack@metacorp.com","first_name":"Jack","id":2,"last_name":"Mannino","password":
    "b46dd2888a0904972649cc880a93f4dd","updated_at":"2014-01-23T16:17:10Z","user_id":2}

Note that all attributes associated with this user are returned via the API.

# Solution

# Hint