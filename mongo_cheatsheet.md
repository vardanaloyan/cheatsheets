# MongoDB introduction

These files are meant to be used with the `mongoimport` tool, to import the files use the tool as follows:

```shell
mongoimport  --authenticationDatabase admin --username root --password rootpassword --db=cooker --jsonArray recipes.json
```

Enter mongo cli
```shell
mongosh admin -u root -p rootpassword
```

Create a database named 'test'
```shell
use test
```

Create a user with permissions
```shell
db.createUser(
  {
        user: "vardan", 
        pwd: "123456", 
        roles: [
            { 
              role: "readWrite", 
              db: "test" 
            }, 
            { 
              role: "read", 
              db: "new" 
            }
        ]
    }
)
```

create a document. It creates collection 'sample' if not exists
```shell
doc = {"title": "Python"}
db.sample.insertOne(doc)
```

Find a document
```shell
db.sample.find()
```

Find a document, return specific fields
```shell
# 1 -> enable, 0 -> disable
db.sample.find({}, {title: 1})
```

Create sub collection 'other' (helpful for querying)
```shell
doc = {"title": "Python"}
db.sample.other.insertOne(doc)
```

Count items in collection
```shell
db.recipes.estimatedDocumentCount()

# or in python
db.sample.estimated_document_count()
```

Limit the results
```shell
db.recipes.find({}, {title: 1}).limit(2)
```

Sort the results in 1 accenting order and -1 for descending order
```shell
db.recipes.find({}, {title: 1}).sort({title: -1})
```

Filter Using less then equal operator
```shell
db.recipes.find({cook_time: {$lte: 30}}, {title: 1})
```

Filter using combination (AND) of conditions
```shell
db.recipes.find({cook_time: {$lte: 30}, prep_time: {$lte: 10}}, {title: 1})
```

Filter using OR
```shell
db.recipes.find({$or: [{cook_time: {$lte: 30}}, {prep_time: {$lte: 10}}]}, {title: 1})
```

Filter array
```shell
 db.recipes.find({tags: "easy"}, {title: 1, tags: 1})
```

Filter array AND
```shell
db.recipes.find({tags: {$all: ["easy", "quick"]}}, {title: 1, tags: 1})
```

Filter array OR
```shell
db.recipes.find({tags: {$in: ["easy", "quick"]}}, {title: 1, tags: 1})
```

Filter by dot notation
```shell
 db.recipes.find({"ingredients.name": "egg"})
```

Update one document, change field
```shell
db.examples.updateOne({"title": "Pizza"}, {"$set": {"title": "modified Pizza"}})
```

Update one document, add new field (vegan)
```shell
db.examples.updateOne({"title": "modified Pizza"}, {"$set": {"vegan": true}})
```

Update one document, remove a field (vegan)
```shell
db.examples.updateOne({"title": "modified Pizza"}, {"$unset": {"vegan": 1}})
```

Increment a field (by 1), without having to know the value of the field (Eventual Consistency)
```shell
db.examples.updateOne({"title": "Tacos"}, {"$inc": {"likes_count": 1}})
```

Add an item to an array ($push)
```shell
db.examples.updateOne({ title: "Tacos" }, { $push: { likes: 60 } })
```

Remove an item from an array ($pull)
```shell
db.examples.updateOne({ title: "Tacos" }, { $pull: { likes: 60 } })
```

Delete a document
```shell
db.examples.deleteOne({_id: ObjectId("5ee69e393260aab97ea0d58e")})
```

Use javascript in mongosh
```shell
const res = db.recipes.find({}, {title: 1, prep_time: 1, _id:0}).toArray()
console.table(res)
```

To see execution details (executionStats.totalDocsExamined)
```shell
db.recipes.find({"title": "Tacos"}, {title: 1}).explain("executionStats")
```

List indexes
```shell
db.recipes.getIndexes()
```

Create an index (Direction of the index descending (-1) or ascending (1))
```shell
db.recipes.createIndex({cook_time: -1})
```

Drop an index
```shell
db.recipes.dropIndex("cook_time_-1")
```

Capped Collections

Keep N documents, and delete last when it reaches. 

- First In First Out
- Guaranteed order
- Deletes when max size is reached

Limitations

- No indexes
- Can't shard

size: Maximum size in bytes
max: Maximum number of documents

```shell
db.createCollection("error_log", {capped: true, max: 5})
```

Convert an existing Collection into Capped
```shell
db.runCommand({"convertToCapped": "error_log2", size: 1000})
```

Change cappedSize
```shell
db.runCommand( { collMod: "error_log", cappedSize: 100000 } )
```

Change cappedMax
```shell
db.runCommand( { collMod: "error_log", cappedMax: 5 } )
```

Create a unique index
```shell
db.error_log.createIndex({title: 1}, {unique: 1})
```

Shutdown the server
```shell
db.adminCommand({shutdown: 1})
```

Lock any writes to mongo for backuo
```shell
db.fsyncLock()
```

For creating a backup. This will dump in the folder called "dump"
```shell
mongodump --authenticationDatabase admin --username root --password rootpassword
```

For restoring a backup (by default from the "dump" folder)
```shell
mongorestore --authenticationDatabase admin --username root --password rootpassword
```
