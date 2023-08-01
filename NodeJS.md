NodeJS is a runtime environment for [[Javascript]]
Comes with #NPM

You always have to `npm init`


To be able to use the EMAC's 'import __ from __', you need to 
open package.json 
below "main": "index.js", add "type": "module",

Bodyparser
```
NPM install body-parser
const bodyParser = require("body-parser")
app.use(bodyParser.urlencoded({extended: true}))
```
You must include ^ every time you use body parser 
try console.log(req.body.---)

Body parser will always return text, if you want an int, you must do Number(req.body.num1)
