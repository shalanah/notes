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

### Up and running

- Start docker
-
