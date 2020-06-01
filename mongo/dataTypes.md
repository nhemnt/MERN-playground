# Data Types

## Text
validation - 16 mb of document

## Booleans


## Numbers
### Integer(32)
### NumberLong(64)
### Number decima()

## ObjectId

## ISODate

## Timestamp


## Embedded Documents

## Arrays


MongoDB has a couple of hard limits - most importantly, a single document in a collection (including all embedded documents it might have) must be <= 16mb. Additionally, you may only have 100 levels of embedded documents.
 
All limits (in great detail) (here)[https://docs.mongodb.com/manual/reference/limits/]

Detailed overview of data types (here)[https://docs.mongodb.com/manual/reference/bson-types/]

*Important data type limits are:

    * Normal integers (int32) can hold a maximum value of +-2,147,483,647

    * Long integers (int64) can hold a maximum value of +-9,223,372,036,854,775,807

    * Text can be as long as you want - the limit is the 16mb restriction for the overall document

