# Description

JQuery Snippet contains at least one DOM-Based XSS vulnerability that can be confirmed in IE11. Unknowingly, the Railsgoat development team used this library. Credit for vulnerability discovery as well as submission to [@raesene](https://www.twitter.com/raesene). This was unintentional but goes to show how easily vulnerabilities can creep in when using third-party libraries.

# Bug

Within the file app/assets/javascripts/jquery.snippet.js:

```javascript
// snippet new window popup function
function snippetPopup(content) {
   top.consoleRef=window.open('','myconsole',
    'width=600,height=300'
     +',left=50,top=50'
     +',menubar=0'
     +',toolbar=0'
     +',location=0'
     +',status=0'
     +',scrollbars=1'
     +',resizable=1');
   top.consoleRef.document.writeln(
    '<html><head><title>Snippet :: Code View :: '+location.href+'</title></head>'
     +'<body bgcolor=white onLoad="self.focus()">'
     +'<pre>'+content+'</pre>'
     +'</body></html>'
   );
   top.consoleRef.document.close();
}
```

We can see that the location.href DOM property is used to dynamically generate a title for the text box pop-up. This value is string concatenated directly from the DOM without first performing some escaping routine or HTML encoding.

# Solution

### Using Components with Known Vulnerabilities (DOM XSS) - ATTACK

### Using Components with Known Vulnerabilities (DOM XSS) - SOLUTION

# Hint