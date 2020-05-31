# Mongo queries

use DATABASE_NAME
show collections
db.collection_name.find().pretty()
db.collection_name.insertOne({});


## CRUD
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




