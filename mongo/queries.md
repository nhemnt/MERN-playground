# Mongo queries

use DATABASE_NAME
db.dropDatabase() //drop databse
show collections 
db.stats() //get db stats
db.collection.drop() //drop collection
db.collection_name.find().pretty()
db.collection_name.insertOne({});

db.runCommand({collMod: "collection_name", validator}) //run from shell --collMod - Collection modifier


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

db.createCollection("collection_name", {
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
});

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