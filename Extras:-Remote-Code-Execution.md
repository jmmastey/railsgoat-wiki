## Remote Code Execution

Railsgoat includes a remote code execution vulnerability through Ruby's Marshal.load vulnerability. Ruby's Marshal.load also the deserialization of arbitrary objects. The password reset controller includes a deserialization vulnerability. During the forgot password flow, after the user clicks on the reset email link, the application verifies the token then adds a Marshaled user object which is posted during the password reset. 

Within reset_password.html.erb
```
          <div class="content">
            <%= hidden_field_tag 'user', Base64.encode64(Marshal.dump(@user)) %>
            <%= label_tag "Enter Password" %>
            <%= password_field_tag :password, params[:password], {:class => "input input-block-level"} %>
            <%= label_tag "Confirm Password" %>
            <%= password_field_tag :confirm_password, params[:confirm_password], {:class => "input input-block-level"} %>
          </div>
```
Within password_resets_controller.rb
```
  def reset_password
    user = Marshal.load(Base64.decode64(params[:user])) unless params[:user].nil?

    if user && params[:password] && params[:confirm_password] && params[:password] == params[:confirm_password]
      user.password = params[:password]
      user.save!
      flash[:success] = "Your password has been reset please login"
      redirect_to :login
    else
      flash[:error] = "Error resetting your password. Please try again."
      redirect_to :login
    end
  end
```
