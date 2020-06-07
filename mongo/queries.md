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

w - the w option to request acknowledgment that the write operation has propagated to a specified
j - number of mongod instances or to mongod instances with specified tags.
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