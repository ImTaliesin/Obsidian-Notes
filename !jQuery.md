

```<script src="https:ajax.googleapis.com/ajax/libs/jquery/3.7.0/jquery.min.js"></script>```

[[Event Listeners]]

| Mouse Events | Keyboard Events | Form Events | Documents/Window Event |
| ------------ | --------------- | ----------- | ---------------------- |
| Click        | keypress        | Submit      | Load                   |
| dblclick     | keydown         | change      | resize                 |
| mouseleave   | keyup           | focus       | scroll                 |
| mouseenter   |                 | blur        | unload                 |

<mark style="background: #ADCCFFA6;">click()</mark>
To assign a click event to all paragraphs on a page, you can do this
$("p").click();

<mark style="background: #ADCCFFA6;">mouseenter()</mark>
The mouseenter() method attaches an event handler function to an HTML element.

The function is executed when the mouse pointer enters the HTML element:
Example
$("#p1").mouseenter(function(){
  alert("You entered p1!");});

**<mark style="background: #ADCCFFA6;">hover()</mark>**
The hover() method takes two functions and is a combination of the mouseenter() and mouseleave() methods.

The first function is executed when the mouse enters the HTML element, and the second function is executed when the mouse leaves the HTML element:
Example
$("#p1").hover(function(){
  alert("You entered p1!");
},
function(){
  alert("Bye! You now leave p1!");});


<mark style="background: #ADCCFFA6;">Focus()</mark>
The `focus()` method attaches an event handler function to an HTML form field.

The function is executed when the form field gets focus:

Example
$("input").focus(function(){  
  $(this).css("background-color", "#cccccc");  });

<mark style="background: #ADCCFFA6;">blur()</mark>
The `blur()` method attaches an event handler function to an HTML form field.

The function is executed when the form field loses focus:

 Example
$("input").blur(function(){  
  $(this).css("background-color", "#ffffff");  });


### <mark style="background: #ADCCFFA6;">The on() Method</mark>
The `on()` method attaches one or more event handlers for the selected elements.

Attach a click event to a `<p>` element:
### Example

$("p").on("click", function(){ 
$(this).hide(); });


Attach multiple event handlers to a `<p>` element:
### <mark style="background: #D2B3FFA6;">Example</mark>
```$("p").on({  
  mouseenter: function(){  
    $(this).css("background-color", "lightgray");  
  },  
  mouseleave: function(){  
    $(this).css("background-color", "lightblue");  
  },  
  click: function(){  
    $(this).css("background-color", "yellow");  
  }  
});
```

<mark style="background: #ADCCFFA6;">'this'</mark>
'this' usually refers to the DOM element that triggered the event.

`var userChosenColor = $(this).attr("id");`
'this' is likely used within an event handler, and it refers to the DOM element that fired the event. `$(this)` wraps the DOM element in a jQuery object, allowing you to call jQuery methods on it.

