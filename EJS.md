<<<<<<< HEAD

What ever goes inbetween the tags will be interpreted as [[Javascript]]

There are five different EJS #Tags 
<%= variable %>              //this will send whatever output you assigned, for example a variable
<% console.log("Hello")     %>  Execute Javascript
<%-                   %>                  Render as HTML
<%#            %>                        This is a comment
<%- include("Filename.ejs")%>    This will let you inset a file 
