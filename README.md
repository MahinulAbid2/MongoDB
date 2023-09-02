# Before Starting:
* Finish `Node.js` basic.
* This note requires installing:
    - express `to full application in nodejs`
    - dotenv `to extract environment variables and connect to MongoDB`



<br>
<br>
<br>

# MongoDB
* MongoDB uses BSON data format.

<br>

### What is BSON?
<ins>**Answer:**</ins> BSON is the format used both for data storage and network transfer in MongoDB. After storing data in MongoDB, you may want to get your data back as JSON, among many other formats. When you want to export your files as JSON, you can use MongoDB's database tool, bsondump , to convert BSON documents to JSON.

<br>


### What is the difference between BSON and JSON?
<ins>**Answer:**</ins> It is a binary file format type. JSON contains some basic data types like string, numbers, Boolean, null. BSON contains some additional data types like date, timestamp, etc. Databases like AnyDB, redis, etc.


# MONGOOSE
==============================================================================

* `Mongoose` is not a built in program in node.js
* I have to `install` mongoose `as a library` in node.js project.

```console
npm install mongoose

or

npm i mongoose
```

Now require it in the project:
```javascript
const mongoose= require("mongoose");
```

<br>

#### Why should I use Mongoose?
Answer: Mongoose makes many works easier such as: 

* Connecting to database/MONGODB
* Doing CRUD operation with online database like MONGODB

<br>
<br>
<br>

### Connection with MONGODB
How to connect to MongoDB using Mongoose?
* You can connect to MongoDB with Mongoose using `mongoose.connect()` method.
```javascript
//BASIC
mongoose.connect(uri);
mongoose.connect(mongodb+srv://himahinulabid:Password123@cluster0.kgeats8.mongodb.net/test); //reallife example

// This method also takes option argument
mongoose.connect(uri, option)
mongoose.connect(mongodb+srv://himahinulabid:Password123@cluster0.kgeats8.mongodb.net/test, {
    useNewUrlParser:true,
    useCreateIndex:true,
    useFindAndModify:false
    //this object, no need to research it for now. Just use it as always
});


// The connect() function also accepts a callback parameter and returns a promise.
mongoose.connect(uri, options).then(
  () => { /** ready to use. The `mongoose.connect()` promise resolves to mongoose instance. */ },
  err => { /** handle initial connection error */ }
);
```


<br>

Here is a real life situtation: <br>
I'm currently creating a `config.env` file
```javascript
PORT=3000
DATABASE=mongodb+srv://himahinulabid:<password>@cluster0.kgeats8.mongodb.net/test
DATABASE_PASSWORD=someRandomPassw0rD
```

<br>

I'm currently in NODE PROJECT's index.js

```javascript
// this code is after requiring all the element needed for this program
// note that I can extract environment variable from env.process object.

// I'm trying to store two value at once.
const Port= env.process.PORT || 8000;
// If environment variable named PORT doesn't exist then store 8000 as Port Value

// Next thing is to store DATABASE LINK
// Here is the value of DATABSE env.variable mongodb+srv://himahinulabid:<password>@cluster0.kgeats8.mongodb.net/test
// notice that I left <password> field as that because I'm gonna replace it here.
const DB=process.env.DATABASE.replace('<password>', process.env.PASSWORD);


//connect to the DATABASE
mongoose.connect(DB, {
    //for now I won't research about this part. If I keep this part this way then there won't be much of a problem.
    newUrlParser:true,
    useCreateIndex:true,
    useFindAndModify: false
}).then((con)=>{
    // promises then is not mandatory though but it gives us certain object to test many things
    console.log(con.connection);
    console.log("connection is successful");
})
```

<br>
<br>
<br>

### Why use Mongoose?
* Mongoose is an `Object Data Modeling(ODM)` library for MongoDB and Node.js which means a higher level of `abstraction`.
* Mongoose allows for `simple, rapid` development of MongoDB database interactions.
* **Features**: 
    - Schemas to model data and relationships
    - Esy data validation
    - Simple query API
    - Middleware etc.

* Mongoose Schema: 
    - Where to model our data, by describing the structure of the data, default values and validation.
    
* Mongoose Model:
    - A wrapper for the schema, providing an interface to the database for CRUD operations.


<br>
<br>
<br>

## What is Mongoose Schema?
In PhpMyAdmin,
* I used to create database and after that I create `database table`.
* In `database table` I used to specify all `column`'s data `type` and it's specific characters of those column.

<br>
<br>

* Just like that, `mongoose` schemas job is to set data `type and characters` for the column of data set. For example, name, rating, price etc.
* <ins> The Schema takes place after connecting to the database in node.js code using mongoose </ins>
* Here is the code:
```javascript
const tourSchema= new mongoose.Schema({
// first I created a new object of SCHEMA
// And then called the Schema() function which takes an object argument 
// in the object argument I will specify the data type & characteristics that data set will take.
    name:{
        type:String, //this name set will only take string data type and `String`'s needs to be uppercase
        required:[true, "A tour must have a name"]
        /** By this I'm mentioning that, whenever API tries to do operation for example create,
        it must have to fill the "name" dataset which I did by throwing an array which index 0== true means the field
        needs to be fullfilled. 
        
        Otherwise it will show an error which will say "A tour must have a name" 
        And that is the second argument of the array
        
        It is called validator**/
        
        unique: true // the name field must be uqniue
        /** that means when a user will want to duplicate the name 
            it will throw an duplicate error.
        **/
    },
    rating:{
        type: Number,
        default: 4.5
        /** if a user doesn't put any data in this specific data set, then
        set the default value to 4.5. That's it. SIMPLE **/
    },
    price:{
        type:Number,
        required:[true, "A tour must have a price"]
    }

});


// Now I have created a "schema"
// One thing left is that I have to create a "schema model".
const Tour = mongoose.model('Tour', tourSchema);
/**
The first argument is the singular name of the collection your model is for.
The second argument is the mongoose schema that I specified each dataset data type and characteristics.
There are more function to this model function but I should focus on the basics of the function for now.
NOW I'M READY TO DO CRUD OPERATION WITH MONGODB
**/
```


<br>
<br>
<br>

# Put data in database table/collection
=============================================================================

* Now I've wrote code that define a databasea table/collection I can put documents in the table and here is how:
```javascript
const tourSchema= new mongoose.Schema({
    name:{
        type:String,
        required:[true, "A tour must have a name"]
        unique: true // the name field must be uqniue
    },
    rating:{
        type: Number,
        default: 4.5
    },
    price:{
        type:Number,
        required:[true, "A tour must have a price"]
    }
});

const Tour = mongoose.model('Tour', tourSchema);

// let's just know the rhythm
// just creating a data before putting it into the database
const testTour= new Tour({ // this Tour is the Tour Schema model prototype
    name:'The Forest hiker',
    rating: 4.7,
    price: 497
});


//now sending it to the database to save in the database
testTour.save().then((document)=>{
    console.log(documents)
}).catch((error)=>{
    console.log("ErrOR", error);
});

// testTour.save() is the main part that lets me save documents.
// the then() and catch() async functions lets us play with the feedback and the document argument.
```

