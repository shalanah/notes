# Complete Intro to DBs - Brian Holt

Course website https://btholt.github.io/complete-intro-to-databases/

## Terms

- **Query** - A command you send to a DB to get it to do something (ie: Query languages)
- **Schema** - Rigid structure used to model data
- **Transactions** - Guarantees many queries to all happen or none of them happen. Also guarantees no other query will happen between this transaction.

### Types of DBs... LOTS!

- **BIG 4: Document, Relational, Graph, Key-Value**
- **Search Engines:** Creates search engines for your DBs (not all dbs come with it, some do)
- Wide Column DBs: 2 dim key-value store (not typical)
- **Message brokers:** RabbitMQ
- **Multi model dbs:** (Azure CosmosDB) Work in diff ways ie relational + non-relational at the same time

### ACID

ACID isn't always necessary... super safe but super slow. Some data should always be handled in an ACID way some data has more give + acceptable loss for speed.

- **Atomicity:** Does this query happen all at once. Is it a single query?... Good for money, and not 1/2 completed transaction. You make sure removing money from one person means you also pay someone else and it is complete when the whole thing happens and not divided into 2. Bad if DB goes down 1/2 way through need to make sure 1 transaction means all these parts are complete and at same time.
- **Consistency:** Making sure all your dbs are up-to-date not just your primary
- **Isolation:** Says that we can have a multi-threaded database but it needs to work the same if a query was ran in parallel or if it was ran sequentially. Otherwise it fails the isolation test. (lifted from bh notes)
- **Durability:** Can you get db back in state before a crash? Has to do with writing to disk and waiting.

# NoSQL DBs

- Doesn't use SQL - marketing term :)
- Non relational
- Confusing because some NoSQL DBs can handle SQL queries
- Schema-less
- Quick to get up and running
- Upside / Can define on the fly... Downside / Overly trusting make sure what you pass it is correct

## MongoDB

- One of the most pop dbs in the world
- Cool for small project + large
- Document based

### Vocab

- **Db:** Group of collections
- **Collections:** Like a table in a relation db
- **Documents:** One entry in the collection

### Up and running

- Start docker
- Create mongo db `docker run --name test-mongo -dit -p 27017:27017 --rm mongo:4.4.1`
- See `docker ps` output

```sh
$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                      NAMES
............   mongo:4.4.1   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   0.0.0.0:27017->27017/tcp   test-mongo
```

- Connect to container and run the mongo cmd `docker exec -it test-mongo mongo`

### Mocking in a bunch of data example

Brian's example

```js
db.pets.insertMany(
  Array.from({ length: 10000 }).map((_, index) => ({
    name: [
      "Luna",
      "Fido",
      "Fluffy",
      "Carina",
      "Spot",
      "Beethoven",
      "Baxter",
      "Dug",
      "Zero",
      "Santa's Little Helper",
      "Snoopy",
    ][index % 9],
    type: ["dog", "cat", "bird", "reptile"][index % 4],
    age: (index % 18) + 1,
    breed: [
      "Havanese",
      "Bichon Frise",
      "Beagle",
      "Cockatoo",
      "African Gray",
      "Tabby",
      "Iguana",
    ][index % 7],
    index: index,
  }))
);
```

### Commands

Can use JS in mongo (ie: `2 + 4`, `Date.now()` etc)

#### Basics

```js
show dbs
// Help
db.stats()
db.help()
db[collection].help()

// Create / switch dbs
use [dbName]

// Insert
db[collection].insertOne(docObj))
db[collection].insertMany(docArray)

// Count
db[collection].count()

// Finds
db[collection].findOne(); // can have nothing in it and return the only opt)
db[collection].findOne({...})
db[collection].find({...}) // an iterator back
db[collection].find({...}).toArray() // all of it back
db[collection].find({...}).limit(num) // use with `skip`
```

#### Query operators (lots more opts)

`$gt`, `$gte`, `$lt`, `$ne`, `$eq` (equals - redundant mostly)

```js
db.pets.count({ type: "cat", age: { $gt: 12 } });
db.pets.count({ type: "cat", age: { $gte: 12 } });
db.pets.find({ name: "Fido", type: { $ne: "dog" } }).count();
```

#### Logical operators

`$and`, `$nor`, `$not`, `$or`

```js
db.pets.count({
  type: "bird",
  $and: [{ age: { $gte: 4 } }, { age: { $lte: 8 } }],
});
```

#### Sort

```js
db.pets.find({ type: "dog" }).sort({ age: -1, breed: 1 }).limit(5);
```

#### Projections

Only ask for what you need (security reasons) and smaller

- Can use 0 to disallow explicitly and the rest comes in

```js
db.pets.find(
  { type: "dog" },
  { name: 1 /* include */, _id: 0 /* do not include */ }
);
```
