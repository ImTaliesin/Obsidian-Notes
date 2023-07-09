ExpressJS is a server framework for [[Javascript]] / [[NodeJS]]

To set up express JS for your node server. at the top of  your code, add 

`npm i express`

```
import express from 'express';

const app = express();

app.listen(3000, () => {
    console.log('Server is running on port 3000');
    });
```
The 3000 in the code is whatever port you want
```
app.get('/', (req, res) => {
    res.send('<h1>Sup Bitch</h1>');
    });
```

You can send [[html]] files as well

```
app.get('/', (req, res) => {
    res.sendFile(__dirname + 'index.html');
    });
```

