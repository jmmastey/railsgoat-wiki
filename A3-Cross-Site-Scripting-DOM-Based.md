# Description

DOM Based XSS (or as it is called in some texts, “type-0 XSS”) is an XSS attack wherein the attack payload is executed as a result of modifying the DOM “environment” in the victim’s browser used by the original client side script, so that the client side code runs in an “unexpected” manner. That is, the page itself (the HTTP response that is) does not change, but the client side code contained in the page executes differently due to the malicious modifications that have occurred in the DOM environment.

# Bug

The following code was taken from app/views/sessions/new.html.erb:

```javascript
<script>
 //document.write("<select style="width: 100px;">");
 //document.write("<OPTION value=1>English</OPTION>");
 //document.write("<OPTION value=2>Spanish</OPTION>");
 try {
     var hashParam = location.hash.split("#")[1];
     var paramName = hashParam.split('=')[0];
     var paramValue = hashParam.split('=')[1];
     document.write("<OPTION value=3>" +  paramValue  + "</OPTION>");
 } catch(err) {
 }
 //document.write("</select>");
</script>
```

# Solution

# Hint