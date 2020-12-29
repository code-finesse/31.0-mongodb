# 31.0-mongodb

- Databases
    - a database is an organized collection of data, stored and accessed electronically
    - a common type is a RDBMS (relational database management system)
        - relational databases are frequently based on SQL (Structured Query Language) and store data in tables and rows, much like an Excel spreadsheet/Google sheet
    - we will be using MongoDB which is a database type called NoSQL which doesn't store data in rows and columns (useful when we need to store a lot of data) to build web apps
    - MongoDB is a specific type of NoSQL database system called a ***document database***
        - it stores data in a format called BSON, which is a JSON-like format, which makes it easy to work with using Javascript because closely resembles objects
    - Bonus Info

        MongoDB is an application that runs as a **server** and can have many separate databases within it.

        Each MongoDB **database** consists of collections.

        Each **collection** is a set of documents.

        Each **document** contains a set of data and attributes, known as fields.

        This is very abstract. Let's use a simplified real world example of an address book.

        Here is one document:

        ```jsx
        { 
        firstName: "Jennifer", 
        lastName: "Juniper", 
        address: "Upon the Hill", 
        state: "California", 
        telephone: "867-5309", 
        birthday: "June 8, 1968", 
        email: "jenny.juniper@juno.net"
        }
        ```

        A collection would be many documents: In our case, many contacts.

        Remember: having a collection of documents sounds quite reasonable. But having a document of collections is ... kind of odd.

        > If you're coming from a background where you are used to thinking of data in terms of columns and rows, then you can correlate a table (or worksheet) to the collections, a row to the documents and columns to the fields in MongoDB.

- Create/Connect to a Database Steps

    mongo is its own shell (everything in the computer can be referenced through "bash") so we need to go to that shell

    ****all of this is done in the terminal***

    1. in terminal type mongo
    2. in terminal type show dbs to see what databases are available
    3. to create and use a database type ***use "database name"***
        - it will create a database with the name you enter and connect to it
    4. make a collection with inside that database with db.createCollection("name of collection")
        - you should get an ok message if done correctly
    5. we can see what collections we have by typing show collections

CRUD

all of these are methods that can be changed that are all structured basically the same

***db.nameofcollection.method(argument)***

- Create

    db.nameofcollection.insert( ) will insert whatever is in the parenthesis

    1. use the insert method and chain it to name of collection
        - this example has an object as the first argument with data that is being put inside the collection of contacts

        ```jsx
        db.contacts.insert({
          name: "Jennifer",
          phone: 8675309,
          state: "California"
        });
        ```

    2. if more than one object (usually structured as an array of objects) look for ***"nInserted"*** so that you know how many successful creations there were
        - if inserting new data sometimes it is not consistent and sometimes records can be duplicated because mongo is designed to be flexible
        - e.g.

            ```jsx
            db.contacts.insert([
              {
                name: "Jennifer",
                phone: 8675309,
                state: "California"
              },
              {
                name: "Claire",
                phone: 6060842
              },
              {
                name: "Morris",
                phone: 7779311,
                state: "Minnesota"
              },
              {
                firstName: "Alicia",
                lastName: "Keys",
                phone: 4894608,
                state: "New York"
              },
              {
                name: "Etta",
                phone: "842-3089",
                state: "California"
              }
            ]);
            // We may notice that our data wasn't consistent.

            // Jennifer has a duplicate record
            // Claire, doesn't have a state
            // Alicia's key's are different for her name than others, she also has an extra field for her last name, compared to others.
            // Etta's phone number is a string with a hyphen instead of a number
            // Mongo is designed to be this flexible. Later, we'll learn how to validate our data with an npm package called mongoose.
            ```

- Read
    1. db.nameofcollection.find( ).pretty( ) will show everything thats in the collection
        - e.g.

            ```jsx
            db.contacts.find({ state: "California" }, { name: 1, _id: 0 }).pretty();
            ```

            - arguments can be put inside the find parenthesis as search queries
            - id with 0 means don't show the _id field because id will always show up
            - 1s and 0s are like binary for on and off (or display and don't display)
    2. db.nameofcollection.find( ).count( ) will show how many entries are in the collection
- Update
    1. [d](http://db.name)b.namofcollection.update( ) will update information that is already in the collection
    2. update overwrites data and gets rid of other fields unless told otherwise
    3. in order to add one field you need to use $set operator with
        1. eg.

            ```jsx
            db.contacts.update(
              { name: "Jennifer" },
              {
                $set: { name: "Jenny" }
              }
            );
            ```

        2. first argument is the search query (name or object id)
        3. second argument is what we want to change without touching anything else
    4. by default, update will only update one record

        ```jsx
        db.contacts.update({}, { $set: { bestFriend: true } });
        ```

    5. in order to update all entries set update with first argument of empty object second argument with $set argument of whatever you want to put in and third argument with multi: true

        ```jsx
        db.contacts.update({}, { $set: { bff: true } }, { multi: true });
        ```

- Delete
    1. db.nameofcollection.remove()
        - it is possible to delete multiple entries (or all if not careful)
    2. multiple entries that fulfill the search query can be deleted from the same command
        - a second argument with an object that has a key value pair of {justOne: true} will ensure that only one entry is deleted
    3. you can remove specific entries by using their specific ids
