# Description

XSS flaws occur whenever an application takes untrusted data and sends it to a web browser without proper validation and escaping. XSS allows attackers to execute scripts in the victim’s browser which can hijack user sessions, deface web sites, or redirect the user to malicious sites.

# Bug

Stored Cross-Site Scripting - The following code was taken from app/views/layouts/shared/_header.html.erb

```erb
<li style="color: #FFFFFF">
  <!--
  I'm going to use HTML safe because we had some weird stuff
  going on with funny chars and jquery, plus it says safe so I'm guessing
  nothing bad will happen
  -->
  Welcome, <%= current_user.first_name.html_safe %>
</li>
```

Coincidentally, HTML safe is not safe from HTML Injection or "XSS" attacks. The name is deceiving. 

```ruby
# Psuedo-code to help conceptualize
def raw(dirty_string)
  dirty_string.to_s.html_safe
end
```
# Solution

# Hint