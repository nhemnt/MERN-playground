# Mongo queries

use DATABASE_NAME
db.dropDatabase() //drop databse
show collections 
db.stats() //get db stats
db.collection.drop() //drop collection
db.collection_name.find().pretty()
db.collection_name.insertOne({});

db.runCommand({collMod: "collection_name", validator}) //run from shell --collMod - Collection modifier

//Run mongoDB shell in background like service
mongod --fork --logpath (path)

//To shutDown server
use admin
db.shutDownServer()


Basic Commands

show dbs
show collections
show users
show profile
show logs
show log [name]



## CRUD
(Official Getting Started Docs)[https://docs.mongodb.com/manual/tutorial/getting-started/]

### Create
inserOne(data, option)
insertMany(data, option)


### READ
find(filter, option) //find all
db.users.find({ age: {$gt: 20}}); //find all user whose age is greater than 20
db.users.find().pretty() //give cursor ex - "it"  to fetch more data
db.users.find().toArary() // all
db.users.find().forEach((user) => {printjson(user)}) //loop throuoh each entery

findOne(filter, option) //find first

### UPDATE
updateOne(filter, data, option)
ex -
db.users.updateOne({ userName: "nhemnt" }, { $set: {email: "test@test.com"}})
updateMany(filter, data, option)  $set
replaceOne(filter, data, option)
update(filter, data, option)  // will update the whole data
ex -
db.users.updateOne({ userName: "nhemnt" }, {isActive: false})

### DELETE
deleteOne(filter, option)
deleteMany(filter, option)

----


## Projections

Retirive only fields which you need

db.users.find({}, {name:1}).pretty()  // show only names 
db.users.find({}, {name:1, _id: 0}).pretty() // remove fields which are coming by default

## Embeded documents
In mongo we can have upto 100th level of nesting and upto 60mb of data

ex
db.users.updateMany({}, { $set: { lastSeen: {
    location: "office",
    time: timestamp,
    details : {
        withWhom: "negi",
        realtion: "friend"
    }
}}})

db.users.updateOne({userName: "nhemnt"}, {$set: {hobbies: ["coding", "football", "travelling", "music"]}})


## Types of relation

### One to One
### One to Many
### Many to Many

We can use $lookup to get refrece data.
Query example

db.users.aggregate([{$lookup: {from: "collection_name", localField: "local_field_name", foreignField: "_id", as: "alias_to_get_data"}}]);


## Create Collection explicitly with validation

```javascript

db.createCollection("collection_name", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["field_name_1", "field_name_2", "field_name_3"],
      properties:  {
        field_name_1: {
          bsonType: "string",
          description: "must be a string and required!"
        },
        field_name_2: {
          bsonType: "objectId",
          description: "must be a Object id and required!"
        },
        field_name_3: {
          bsonType: "array",
          required: ["sub_field_name_1", "sub_field_name_2"],
          description: "must be a Array id and required!",
          properties: {
            sub_field_name_1: {
              bsonType: "string",
              description: "must be a string and required!"
            },
            sub_field_name_2: {
              bsonType: "objectId",
              description: "must be a objectId and required!"
            }
          }
        }
      }
    },
  },
})

```

### Update collection validations

```javascript

db.runCommand({
  collMod: "collection_name",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["field_name_1", "field_name_2", "field_name_3"],
      properties: {
        field_name_1: {
          bsonType: "string",
          description: "must be a string and required!",
        },
        field_name_2: {
          bsonType: "objectId",
          description: "must be a Object id and required!",
        },
        field_name_3: {
          bsonType: "array",
          required: ["sub_field_name_1", "sub_field_name_2"],
          description: "must be a Array id and required!",
          properties: {
            sub_field_name_1: {
              bsonType: "string",
              description: "must be a string and required!",
            },
            sub_field_name_2: {
              bsonType: "objectId",
              description: "must be a objectId and required!",
            },
          },
        },
      },
    },
  },
  valdationAction: "warn", //by deafult error
});


```

## Insert

### InsertOne
db.users.insertOne({ name: "heamnt", age: 25 })

### InsertMany
db.users.insertMany([{ name: "heamnt", age: 25 }, { name: "negi", age: 22 }])

### Insert
db.users.insert({ name: "heamnt", age: 25 })
db.users.insert([{ name: "heamnt", age: 25 }, { name: "negi", age: 22 }])


## Ordered Insert | Unordered Insert
By default bulk insert ex insertMany use ordered inserts

To achieve multi upload even after error

we can use 2nd parameter in insertMany { ordered: false }

db.users.insertMany([{ name: "heamnt", age: 25 }, { name: "negi", age: 22 }], { ordered: false })


## writeConcer

the w option to request acknowledgment that the write operation has propagated to a specified
j number of mongod instances or to mongod instances with specified tags.
the j option to request acknowledgment that the write operation has been written to the on-disk journal, and
wtimeout - the wtimeout option to specify a time limit to prevent write operations from blocking indefinitely.

ex-  { w:1, j: true, wtimeout:200 }

Query Example
[superFast, less security]
db.users.insertOne({ name: "heamnt" }, { writeConcern: {w: 0} })
just send the req to db server to save and return with { acknowledged: false }


default case
db.users.insertOne({ name: "heamnt" }, { writeConcern: {w: 1} })

journal true - giving response only when you write the data in journal
[take more time than usual, higher security]
db.users.insertOne({ name: "heamnt" }, { writeConcern: { w: 1, j: true } })

wtimeout
db.users.insertOne({ name: "heamnt" }, { writeConcern: { w: 1, j: true, wtimeout: 200 } })

## Atomicity
write operation is atomic on the level of a single document, even if the operation modifies multiple embedded documents within a single document.


## Importing Data

mongoimport file_path -d database_name -c collection_name --jsonArray --drop

--jsonArray  - telling mongoDb json is array
--drop - if collection is there then drop and update all data else by default data is appended



## Operators

### Query and Projection Operators

#### Comparison

$eq -	Matches values that are equal to a specified value.
$gt -	Matches values that are greater than a specified value.
$gte -	Matches values that are greater than or equal to a specified value.
$in -	Matches any of the values specified in an array.
$lt -	Matches values that are less than a specified value.
$lte -	Matches values that are less than or equal to a specified value.
$ne -	Matches all values that are not equal to a specified value.
$nin -	Matches none of the values specified in an array.


#### Logical

$and -	Joins query clauses with a logical AND returns all documents that match the conditions of both clauses.
$not -	Inverts the effect of a query expression and returns documents that do not match the query expression.
$nor -	Joins query clauses with a logical NOR returns all documents that fail to match both clauses.
$or -	Joins query clauses with a logical OR returns all documents that match the conditions of either clause.


#### Element

$exists -	Matches documents that have the specified field.
$type -	Selects documents if a field is of the specified type.

#### Evalution

$expr -	Allows use of aggregation expressions within the query language.
$jsonSchema -	Validate documents against the given JSON Schema.
$mod -	Performs a modulo operation on the value of a field and selects documents with a specified result.
$regex -	Selects documents where values match a specified regular expression.
$text -	Performs text search.
$where -	Matches documents that satisfy a JavaScript expression.


#### Geospatial

$geoIntersects -	Selects geometries that intersect with a GeoJSON geometry. The 2dsphere index supports $geoIntersects.
$geoWithin -	Selects geometries within a bounding GeoJSON geometry. The 2dsphere and 2d indexes support $geoWithin.
$near -	Returns geospatial objects in proximity to a point. Requires a geospatial index. The 2dsphere and 2d indexes support $near.
$nearSphere -	Returns geospatial objects in proximity to a point on a sphere. Requires a geospatial index. The 2dsphere and 2d indexes support $nearSphere.


#### Array

$all -	Matches arrays that contain all elements specified in the query.
$elemMatch -	Selects documents if element in the array field matches all the specified $elemMatch conditions.
$size -	Selects documents if the array field is a specified size.

#### Bitwise

$bitsAllClear -	Matches numeric or binary values in which a set of bit positions all have a value of 0.
$bitsAllSet -	Matches numeric or binary values in which a set of bit positions all have a value of 1.
$bitsAnyClear -	Matches numeric or binary values in which any bit from a set of bit positions has a value of 0.
$bitsAnySet -	Matches numeric or binary values in which any bit from a set of bit positions has a value of 1.


#### Comments

$comment -	Adds a comment to a query predicate.


#### Projection Operators

$	Projects - the first element in an array that matches the query condition.
$elemMatch -	Projects the first element in an array that matches the specified $elemMatch condition.
$meta -	Projects the document’s score assigned during $text operation.
$slice -	Limits the number of elements projected from an array. Supports skip and limit slices.

---
###  Operators Queries example 

db.movies.find({runtime:60}) is Equal to 
db.movies.find({runtime: {$eq: 60}})

db.movies.find({runtime: {$lte: 60}})

Embeded filter
db.movies.find({"rating.average": {$gt: 9}}).pretty()


include element in array
db.movies.find({"genres": "Drama"}).pretty()

exactly one element in array
db.movies.find({"genres": ["Drama"]}).pretty()


db.movies.find({runtime: {$in: [30, 42]}})


Or condition -  (age == 30 || age ==40) || name == "he*" // name starts with he
db.users.find({$or: [{age: {$in: [30, 40]}}, {name: /^he/}]})

nor is opp of or

db.users.find({$nor: [{age: {$in: [30, 40]}}, {name: /^he/}]})


And condition - age == 30 || age ==40) && name == "he*" // name starts with he
db.users.find({$and: [{age: {$in: [30, 40]}}, {name: /^he/}]})
db.users.find({name: "ram", name: /^he/ }) // wrong way as name: "ram" will be overwirtten by name: /^he/ as javascript object can not have two keys





### Element Queries Example

db.users.find({age: {exists: true}}).pretty() // get all the elements which have age fields
db.users.find({age: {exists: false}}).pretty() // get all the elements which dont have age fields

db.users.find({age: {exists: true, type: number}}).pretty() // get all the elements which have age fields and type number



### Evalaution Queries Example
db.users.find({name: {$regex: /hem/}}).pretty();

db.users.find({$expr: {$gt: ["$height", "$length"] }}) //find all users where height are greater than length


#### find all user where user have more marks in maths than science, but if user have more than 250 marks in maths than the marks in maths will be original marks subtract 50.

```javascript

 db.users.find(
    {
      $expr: {
        $gt: [
          {
            $cond: {
              if: {
                $gte: ["$marks.maths", 250]
              },
              then: {
                $subtract: ["$marks.maths", 50]
              },
              else: "$marks.maths"              
            }
          },
          "$marks.science"
        ]
      }
    }
  )

```


db.users.find({"hobbies.title": "sports"})
 
 // if hobbies are array object containg title fields
 ex - {
   hobbies: [
    {
      title: "sports",
      frequency: "12"
    },
    {
      title: "yoga",
      frequency: "12"
    }
   ]
 }


 //get list of all the users which have exactly 3 hobbies.

 db.users.find({hobbies: {$size: 3}});


//The $all operator selects the documents where the value of a field is an array that contains all the specified elements. To specify an $all expression, use the following prototype:
{ <field>: { $all: [ <value1> , <value2> ... ] } }

 db.users.find({hobbies: {$all: ["yoga", "sports"]}})





//find all user where hobbie is sports and number Of year played 2

db.users.find({$and: [{"hobbies.title": "sports", "hobbies.noOfYearPlayed": 2}]}).pretty();

drawback-
it gave back all the document which have title sports and frequency 2 but not necessary both title and  freauency are part of one object.


we can use $elemMatch instead

db.users.find({hobbies: {$elemMatch: {title: "sports", "freqnoOfYearPlayed": 2}}})



### Cursor

const cursor = db.users.find()

cursor.count()  //gives count
cursor.pretty()  //gves pretty count of first 20
cursor.next()  //gices next document ex- 21th element
cursor.forEach(doc => { printjson(doc)}) //print all document from 22 - end;

cursor.hasNext() //true if cursor has data else false(expires)



## Sort

db.users.find().sort({"age": 1})
// 1 -- ASC
// -1 -- DES

//get all user sorted by age and their salleriesin ascending order

db.users.find().sort({"age": 1}, {sallery : 1}).pretty()

## Skip And Limit (pagination)
db.users.find().sort({"age": 1}, {sallery : 1}).skip(10).pretty()

db.users.find().sort({"age": 1}, {sallery : 1}).skip(10).limit(10).pretty()


## Projection - get limited data

db.users.find({}, { name:1, age:1, "hobbies.title": 1}) //get only name, age and hobbies title

## Projection in array

db.users.find({hobbies: "sports"}, {"hobbies.$": 1 })

## Projection Slice 
db.users.find({age: 21}, {"hobbies": { $slice: 2}, name: 1}); //give name and max two hobbies for  each user who have age 21

db.users.find({age: 21}, {"hobbies": { $slice: [1,2]}, name: 1}); //give name and max two hobbies for  each user and skip first hobby also user must have age 21

$slice - [1,2]  skip - 1 element, limit - 2 element limit



# Update


$currentDate --	Sets the value of a field to current date, either as a Date or a Timestamp.

$inc --	Increments the value of the field by the specified amount.

$min --	Only updates the field if the specified value is less than the existing field value.

$max --	Only updates the field if the specified value is greater than the existing field value.

$mul --	Multiplies the value of the field by the specified amount.

$rename --	Renames a field.

$set --	Sets the value of a field in a document.

$setOnInsert --	Sets the value of a field if an update results in an insert of a document. Has no effect on update operations that modify existing documents.

$unset --	Removes the specified field from a document.



## update a field 

db.users.updateOne({_id: ObjectId("5ef0f5a32a9f1315201a030b")}, {$set: {hobbies: [{title: "sports", frequency: 9}]}})

## $set

db.users.updateMany({"hobbies.title": "Sports"}, {$set: { premium: true, age: 21}} )

## $inc

db.users.updateOne({name: "hemant"}, {$inc : {}, $set: {}}) //we can also use $set next to $inc

db.users.updateOne({name: "hemant"}, {$inc : {age: 1})
//Decrement
db.users.updateOne({name: "hemant"}, {$inc : {age: -1})


## $min

//set age of hemant to 30 if updated value is less than current value
ex- 30 < current heamnt's age

db.users.updateOne({"name": "hemant"}, {$min: { age: 30}})

## max

//set age of hemant to 30 if updated value is greater than current value
ex- 30 > current heamnt's age

db.users.updateOne({"name": "hemant"}, {$max: { age: 30}})

## $mul

//set goods of value to 10 times more

db.user.updateOne({"name": "hemant}, {$mul : {goods: 1.1}});



## $unset

remove the fields hobbies of all user which have age greater than 45

db.users.updateMany({$gt: {age: 45}}, {hobbies: null}); // make hobbies: null

db.users.updateMany({$gt: {age: 45}}, {$unset: {hobbies: ""}}); // drop the keys


## $rename

//update all users name to username

db.users.updateMany({}, {$rename: {name: "username"}});


## $upsert - update and insert

by defaul upsert is false
if documnet does not exist while updating, mongodb will create new element when upsert is true

ex-
db.users.updateOne({name: "hemant"}, {age: 24. hobbies: [{"tilte": "movies", frequence: 1}]}, {upsert: true});



## update in array

$ - gives first matching element
$[] - gives all array element
$[el] - give element to each array, then use arrayFilters to apply conditions

db.users.find({$and: [{"hobbies.title": "cooking"}, {hobbies.frequence: {$gte: 4}]})

//add isPaid field to true in all user which have hobbies tilte sports and their frequence is greater than 3 
db.users.updateMany(
  {
    hobbies: {
      $elemMatch: {
        title: "sports",
        frequence: {gte: 3}
      }
    }
  },
  {
  $set: {
    "hobbies.$.isPaid": true
    }
})


db.users.updateMany({
    age: {$gt: 25}
  },
  {
    $inc: {"hobbies.$[].frequency": -1 }
  }
)

## $arrayFilters

db.users.updateMany({
    "hobbies.frequency": {$gt: 3}
  },
  {
    $set: {
      "hobbies.$[el].averageFrequency": true
    }
  },
  {
    arrayFilters: [{"el.frequency": {$gt: 2}}]
  }
)

## $push or $addToSet
// $push and $addToSet do the same things that is they add data in array.
but $push add duplicates and $addToSet does not add duplicates
//use $push instead of $set to push element in array

db.users.updateOne({name: "hemant"}, {$push: { "hobbies": {title: "cooking", frequency: 4}});

insert mutiple data with push using $each

db.users.updateOne({name: "heamnt"}, {$push: {"hobbies": {$each: [{title: "medication", frequency: 3},{title: "yoga", frequency: 4}]}}})

// $each have a similar method $sort - how the new element should be sorted before we push them in the data
// also we have slice to slice array
for example sorting by frequency in descending order
slice to one element
db.users.updateOne(
  {name: "heamnt"},
  {
    $push: {
      "hobbies": {
        $each: [{title: "medication", frequency: 3},{title: "yoga", frequency: 4}],
        $sort: { frequency: -1},
        $slice: 1
      }
    }
  }
)


## $pull
//to pull data from array

// ex- remove all the title with medication  from hobbies of hemant

db.users.updateOne(
  {name: "heamnt"},
  {
    $pull: {
      "hobbies": {
        "title": "medication"
      }
    }
  }
)

## $pop
// remove last element od array
1 - to remove last element
-1 - to remove first element

ex - remove last hobby of user hemant

db.users.updateOne(
  {name: "heamnt"},
  {
    $pop: {
      "hobbies": 1
    }
  }
)


## Delete

delete a user which have a name hemant

db.users.deleteOne({name: "hemant"});

db.users.deleteMany({age: {$gt: 50}, sallery: {$gt: 50000});


## delete collection

db.users.deleteMany({})

db.users.drop();

## delete database

db.dropDatabase();


