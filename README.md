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