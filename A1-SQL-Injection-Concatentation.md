# Description

Injection flaws, such as SQL, OS, and LDAP injection, occur when untrusted data is sent to an interpreter as part of a command or query. The attacker’s hostile data can trick the interpreter into executing unintended commands or accessing unauthorized data.

# Bug

This example of SQL Injection also happens to be a form of Insecure Direct Object Reference since it uses user-supplied input to determine the user's profile to update. However, we will discuss the SQL query being used and why it is vulnerable.

Within app/controllers/users_controller.rb

```ruby
def update
  message = false
  user = User.find(:first, :conditions => "user_id = '#{params[:user][:user_id]}'")
  user.skip_user_id_assign = true
  user.update_attributes(params[:user].reject { |k| k == ("password" || "password_confirmation") || "user_id" })
  pass = params[:user][:password]
  user.password = pass if !(pass.blank?)
  message = true if user.save!
  respond_to do |format|
    format.html { redirect_to user_account_settings_path(:user_id => current_user.user_id) }
    format.json { render :json => {:msg => message ? "success" : "false "} }
  end
end
```
#Solution

### SQL Injection - ATTACK

You will need to use an intercepting proxy or otherwise modify the request prior to it being received by the application. Browse to account_settings (top right, drop-down). Once at the account settings page, type in passwords, and click submit. Now modify the request from:

    POST /users/5.json HTTP/1.1
    Host: railsgoat.dev
    User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:19.0) Gecko/20100101 Firefox/19.0
    Accept: */*
    Accept-Language: en-US,en;q=0.5
    Accept-Encoding: gzip, deflate
    Content-Type: application/x-www-form-urlencoded; charset=UTF-8
    X-Requested-With: XMLHttpRequest
    Referer: http://railsgoat.dev/users/5/account_settings
    Content-Length: 294
    Cookie: _railsgoat_session=[redacted]
    Connection: keep-alive
    Pragma: no-cache
    Cache-Control: no-cache
    
    utf8=â&_method=put&authenticity_token=GXhLKKhfBXdFx5i6iqHEd5E32Kebn1+G35eA87RW1tU=&user[user_id]=5&user[email]=ken@metacorp.com&user[first_name]=Ken&user[last_name]=Johnson&user[password]=testtest&user[password_confirmation]=testtest

### SQL Injection - SOLUTION


#Hint