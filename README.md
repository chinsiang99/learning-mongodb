This is a repo for me to learn mongodb

# About Documents and Collections

here, we will try to learn to use mongo shell to do the operations

## creating a document
> doc = {"title": "Tacos"}
> db.tacos.insertOne(doc)

note that tacos here mean collection in a db, if it does not exist, it will be automatically created

## finding document
1. find all documents for recipes collection
> db.recipes.find()

2. find documents related to the field
> db.recipes.find({"title": "Tacos"})

3. omit some fields or take some fields only, 1 means want to include, otherwise omit
> db.recipes.find({}, {"title": 1})

4. regex finding document (we can use regex because it is currently using javascript interaction)
> db.recipes.find({"title": { $regex: /taco/i }})

## Collections
In MongoDB, a collection is a grouping of MongoDB documents. It's analogous to a table in relational databases but with a few key differences. Here’s an overview:

### Key Concepts of Collections in MongoDB:
1. Structure:

- Collections store documents, which are records consisting of field-value pairs.
- Unlike tables in relational databases, collections in MongoDB do not enforce a schema. This means that documents within the same collection can have different fields or data types.

2. Documents:

- Each document in a MongoDB collection is a BSON (Binary JSON) object. BSON is a binary representation of JSON-like documents that includes field names, types, and values.
- Documents in a collection are similar to rows in a relational table, but they are more flexible because they can store complex and nested data structures.

3. Flexibility:

- Collections in MongoDB are schema-less, allowing for a high degree of flexibility. You can store documents with varying structures in the same collection.

4. Indexes:

- You can create indexes on collections to improve query performance. Indexes in MongoDB work similarly to those in relational databases.

5. Creation:

- Collections are typically created automatically when you insert the first document. You can also create collections explicitly with specific options like capped collections or pre-defined validation rules.

6. Operations:

- Common operations on collections include inserting documents (insertOne, insertMany), querying documents (find, findOne), updating documents (updateOne, updateMany), and deleting documents (deleteOne, deleteMany).

# Querying in Mongodb

## limiting
> db.recipes.find().count()
> db.recipes.find({}, {"title": 1}).limit(2)

## sorting
note that 1 meaning ascending, -1 meaning dscending
> db.recipes.find({}, {"title": 1}).sort({"title": 1})
> db.recipes.find({}, {"title": 1}).sort({"title": -1})

## skipping
skip is equivalent to offset
> db.recipes.find({}, {"title": 1}).skip(1)

## Comparison Operators

1. $gt - greater than
2. $lt - lesser than
3. $lte - lesser than equal
4. $or - or operator

examples:
> db.recipes.find({"cook_time": {$lte: 30}}, {"title": 1})
> db.recipes.find({$or: [{"lte: 30"}, {"prep_time": {$lte: 10}}]}, {"title": 1})
> db.recipes.find({$or: [{"lte: 30"}, {"prep_time": {$lte: 10}}]}, {"title": 1})

## Updating documents
> db.examples.find({}, {"title": 1})
> db.examples.updateOne({"title": "Pizza"}, {$set: {"title": "Thin crust pizza"}})
> db.examples.updateOne({"title": "Thin crust pizza"}, {$set: {"vegan": false}})
> db.examples.updateOne({"title": "Thin crust pizza"}, {$unset: {"vegan": 1}})

### increment operator
> db.examples.findOne({"title": "Tacos"})
> db.examples.updateOne({"title": "Tacos"}, {$inc: {"likes_count": 2}})
> db.examples.updateOne({"title": "Tacos"}, {$inc: {"likes_count": -2}})

## Updating arrays
> db.examples.findOne({"title": "Tacos"})
> db.examples.updateOne({"title": "Tacos"}, {$push: {"likes": 60}})
> db.examples.updateOne({"title": "Tacos"}, {$pull: {"likes": 60}})

## deleting documents
> db.examples.find({}, {"title": 1})
> db.examples.deleteOne({"_id": ObjectId("5ee69e393260aab97ea0d58e")})

# Data and Schema Modeling (Strategies for data modeling)
- Quote from MongoDB, "Data that is accessed together should be stored together"

## Basic Index
> db.recipes.find({"cook_time": 30}, {"title": 1}).explain("executionStats")
> db.recipes.find({"title": "Tacos"}, {"title": 1}).explain("executionStats")

note that title has index, so the totaldocsexamined will be very low

in order to show what indexes, we can use following command:
> db.recipes.getIndexes()

to create index, we can use following commands, we can have different types of indexes, such as compound indexes, geospatial indexes and etc: (note here if it is 1, it means ascending, if it is -1, it means descending)
> db.recipes.createIndex({"title": 1})

to delete index, we can use the following commands
> db.recipes.dropIndex("title_1")

## Using Different collection types

### How GridFS works

GridFS (Grid File System) is a specification for storing and retrieving large files, such as images, videos, and other binary data, in MongoDB. Since MongoDB's BSON document size is limited to 16 MB, GridFS is used to store files that exceed this size limit by splitting the file into smaller chunks and storing each chunk as a separate document.

#### How GridFS Works:
- Chunks: The file is divided into chunks (default size: 255 KB) and each chunk is stored in a separate document within a chunks collection.
- Metadata: Metadata about the file, including its filename, size, and the order of chunks, is stored in a files collection.
- Retrieval: When retrieving the file, GridFS reassembles the chunks in the correct order to reconstruct the original file.

<div align="center">
  <img src="./gridfs.png.png" alt="how gridfs works" />
</div>

#### When to Use GridFS:
- Large Files: If you need to store files larger than 16 MB.
Streaming: For streaming video or audio files where you need to access data in chunks rather than loading the entire file into memory.
- Partial Retrieval: If you need to retrieve specific portions of a file without loading the entire file into memory.

#### GridFS Collections:
- fs.files: Contains metadata about each file, such as the filename, length, and upload date.
- fs.chunks: Contains the file's chunks, each associated with a files_id that ties it back to its corresponding document in fs.files.

#### Use Cases:
Storing large media files, backups, or any other large binary data.
Applications that require efficient storage and retrieval of large amounts of data without exceeding MongoDB's document size limit.
GridFS is typically used with MongoDB drivers, which provide built-in methods for storing and retrieving files using GridFS.

### There are two types of other collections:
1. capped collections
2. time series collections

#### Capped Collections
- it ensures insertion order
- it is limited by disk size or document count
- it provides automatic deletion:
    1. first-in first-out
    2. based off guaranteed order

#### Example of Creating a Capped Collection:
> db.createCollection("logCollection", { capped: true, size: 5242880, max: 5000 })

1. capped: true: Specifies that this is a capped collection.
2. size: 5242880: Sets the maximum size of the collection in bytes (here, 5 MB).
3. max: 5000: Optionally limits the number of documents in the collection (in this case, to 5000 documents).

#### Limitations of capped collections
- no index by default (note if you need to update document often)
- can't shard a capped collection
