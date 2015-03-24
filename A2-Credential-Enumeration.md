# Description

Overly verbose error messages that indicate whether or not a user exists can assist an attacker with brute-forcing accounts. In attempting to harvest valid usernames for a password-guessing campaign, these messages can prove very useful.

# Bug

Username and Password Enumeration

Within /app/models/user.rb:

```ruby
def self.authenticate(email, password)
  auth = nil
  user = find_by_email(email)
  # I heard something about hashing, dunno, why bother really. Nobody will get access to my stuff!
  if user
    if user.password == password
      auth = user
    else
     raise "Incorrect Password!"
    end
  else
     raise "#{email} doesn't exist!"
  end
  return auth
end
```
On lines 9 and 12 you'll notice that the application generates two error messages.

Within /app/controllers/sessions_controller.rb:

```ruby
def create
  begin
    user = User.authenticate(params[:email], params[:password])
  rescue Exception => e
  end
  
  if user
    session[:user_id] = user.user_id if User.where(:user_id => user.user_id).exists?
    redirect_to home_dashboard_index_path
  else
    flash[:error] = e.message
    render "new"
  end
end
```
On line 5 you see the exception message object "e" is created. On line 11, the message is displayed.

One of these messages indicates the email address (username) doesn't exist on the system. The other indicates that the password is incorrect. Although the application will render both error messages, either one of the error messages would be harmful by itself. This type of information can be used by an attacker to harvest email addresses or usernames. Once that list is gathered, passwords can be guessed for each account. If the username being enumerated is actually an email address, a phishing campaign could ensue with emails made to look like they are originating from the vulnerable site.

# Solution

### Username and Password Enumeration - SOLUTION

Within /app/controllers/sessions_controller.rb

```ruby
def create
  begin
    user = User.authenticate(params[:email], params[:password])
  rescue Exception => e
  end
  
  if user
    session[:user_id] = user.user_id if User.where(:user_id => user.user_id).exists?
    redirect_to home_dashboard_index_path
  else
    flash[:error] =  "Either your username and password is incorrect" #e.message
    render "new"
  end
end
```

Although this fix is neither systemic nor does it address the problematic code at its core (within the user model), it does provide a quick solution. On line 12, we comment out the "e.message code" and instead provide a very generic error message that lacks specificity on what credential was incorrectly entered.

# Hint
Enter an email address that wouldn't likely exist into the login form. Analyze the result.

Can you leverage this to gain unauthorized access?
