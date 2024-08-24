[[tailwind css]]

When you have a container that has a scroll menu, use.
overscroll-behavior: contain

if you want colums. you can use 
.colums {
 colums: 200px 3; //3 colums 200px, is responsive, if it cant do 200px colums it will shrink to fit. looks good
 colums-rule: 1px dotted blue // size, type, color
 gap: 3em;
}

For Headings, make sure you have text-wrap: balanced.
For paragraphs make sure you have text-wrap:balanced

if you want to add a drop shadow to an SVG: 

svg {
&.drop{
filter: drop-shadow: (0 5px 5px black);}
}