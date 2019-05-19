Ref: http://learnmongodbthehardway.com/schema/schemadesign/

**Schema Basics**

Before exploring the more advanced schemas in this book it's important
to revisit schema basics. In this chapter we will explore the basic
relationships from traditional relational databases and how they relate
to the document model in MongoDB.

We will start with a look at the One-To-One (1:1) relationship then
moving on to the One-To-Many (1:N) and finally the Many-To-Many (N:M).

One-To-One (1:1)

The *1:1* relationship describes a relationship between two entities. In
this case the *Author* has a single *Address* relationship where an
*Author* lives at a single *Address* and an *Address* only contains a
single *Author*.

![](https://github.com/samirsahoo007/SQL/blob/master/images/one-to-one.png){width="6.688888888888889in"
height="2.229630358705162in"}

The *1:1* relationship can be modeled in two ways using MongoDB. The
first is to embed the relationship as a document, the second is as a
link to a document in a separate collection. Let's look at both ways of
modeling the one to one relationship using the following two documents:

Model

{

name: **\"Peter Wilkinson\"**,

age: 27

}

*Figure: An example User document.*

{

street: **\"100 some road\"**,

city: **\"Nevermore\"**

}

*Figure: An example Address document*

Embedding

The first approach is simply to embed the *Address* document as an
embedded document in the *User* document.

{

name: **\"Peter Wilkinson\"**,

age: 27,

address: {

street: **\"100 some road\"**,

city: **\"Nevermore\"**

}

}

*Figure: An example User document with Embedded Address*

The strength of embedding the *Address* document directly in the *User*
document is that we can retrieve the user and its addresses in a single
read operation versus having to first read the user document and then
the address documents for that specific user. Since addresses have a
strong affinity to the user document the embedding makes sense here.

Linking

The second approach is to link the address and user document using a
foreign key.

{

\_id: 1,

name: **\"Peter Wilkinson\"**,

age: 27

}

*Figure: An example User document*

{

user\_id: 1,

street: **\"100 some road\"**,

city: **\"Nevermore\"**

}

*Figure: An example Address document with Foreign Key*

This is similar to how traditional relational databases would store the
data. It is important to note that MongoDB does not enforce any foreign
key constraints so the relation only exists as part of the application
level schema.

IMPORTANT

Embedding Preferred

In the one to one relationship Embedding is the preferred way to model
the relationship as it's more efficient to retrieve the document.

One-To-Many (1:N)

The *1:N* relationship describes a relationship where one side can have
more than one relationship while the reverse relationship can only be
single sided. An example is a *Blog* where a blog might have many
*Comments* but a *Comment* is only related to a single *Blog*.

![](https://github.com/samirsahoo007/SQL/blob/master/images/one-to-many.png){width="6.688888888888889in"
height="2.229630358705162in"}

The *1:N* relationship can be modeled in several different ways using
MongoDB. In this chapter we will explore three different ways of
modeling the *1:N* relationship. The first is embedding, the second is
linking and the third is a bucketing strategy that is useful for cases
like time series. Let's use the model of a Blog Post and its Comments.

Model

{

title: **\"An awesome blog\"**,

url: **\"http://awesomeblog.com\"**,

text: **\"This is an awesome blog we have just started\"**

}

*Figure: An example Blog Post document*

A Blog Post is a single document that describes one specific blog post.

{

name: **\"Peter Critic\"**,

created\_on: ISODate(**\"2014-01-01T10:01:22Z\"**),

comment: **\"Awesome blog post\"**

}

{

name: **\"John Page\"**,

created\_on: ISODate(**\"2014-01-01T11:01:22Z\"**),

comment: **\"Not so awesome blog\"**

}

*Figure: Some example Comment documents*

For each Blog Post we can have one or more Comments.

Embedding

The first approach is to embed the Comments in the Blog Post.

{

title: **\"An awesome blog\"**,

url: **\"http://awesomeblog.com\"**,

text: **\"This is an awesome blog we have just started\"**,

comments: \[{

name: **\"Peter Critic\"**,

created\_on: ISODate(**\"2014-01-01T10:01:22Z\"**),

comment: **\"Awesome blog post\"**

}, {

name: **\"John Page\"**,

created\_on: ISODate(**\"2014-01-01T11:01:22Z\"**),

comment: **\"Not so awesome blog\"**

}\]

}

*Figure: A Blog Post with Embedded documents*

The embedding of the comments in the *Blog* post means we can easily
retrieve all the comments belong to a particular *Blog* post. Adding new
comments is as simple as appending the new comment document to the end
of the comments array.

However, there are three potential problems associated with this
approach that one should be aware off.

• The first is that the comments array might grow larger than the
maximum document size of 16 MB.

• The second aspects relates to write performance. As comments get added
to Blog Post over time, it becomes hard for MongoDB to predict the
correct document padding to apply when a new document is created.
MongoDB would need to allocate new space for the growing document. In
addition, it would have to copy the document to the new memory location
and update all indexes. This could cause a lot more IO load and could
impact overall write performance.

IMPORTANT

It's important to note that this only matters for *high write* traffic
and might not be a problem for smaller applications. What might not be
acceptable for a *high write* volume application might be tolerable for
an application with *low write* load.

• The third problem is exposed when one tries to perform pagination of
the comments. There is no way to limit the comments returned from the
Blog Post using normal finds so we will have to retrieve the whole blog
document and filter the comments in our application.

Linking

The second approach is to link comments to the Blog Post using a more
traditional foreign key.

{

\_id: 1,

title: **\"An awesome blog\"**,

url: **\"http://awesomeblog.com\"**,

text: **\"This is an awesome blog we have just started\"**

}

*Figure: An example Blog Post document*

{

blog\_entry\_id: 1,

name: **\"Peter Critic\"**,

created\_on: ISODate(**\"2014-01-01T10:01:22Z\"**),

comment: **\"Awesome blog post\"**

}

{

blog\_entry\_id: 1,

name: **\"John Page\"**,

created\_on: ISODate(**\"2014-01-01T11:01:22Z\"**),

comment: **\"Not so awesome blog\"**

}

*Figure: Some example Comment documents with Foreign Keys*

An advantage this model has is that additional comments will not grow
the original Blog Post document, making it less likely that the
applications will run in the maximum document size of 16 MB. It's also
much easier to return paginated comments as the application can slice
and dice the comments more easily. On the downside if we have 1000
comments on a blog post, we would need to retrieve all 1000 documents
causing a lot of reads from the database.

Bucketing

The third approach is a hybrid of the two above. Basically, it tries to
balance the rigidity of the embedding strategy with the flexibility of
the linking strategy. For this example, we will split the comments into
buckets with a maximum of 50 comments in each bucket.

{

\_id: 1,

title: **\"An awesome blog\"**,

url: **\"http://awesomeblog.com\"**,

text: **\"This is an awesome blog we have just started\"**

}

*Figure: An example Blog Post document*

{

blog\_entry\_id: 1,

page: 1,

count: 50,

comments: \[{

name: **\"Peter Critic\"**,

created\_on: ISODate(**\"2014-01-01T10:01:22Z\"**),

comment: **\"Awesome blog post\"**

}, \...\]

}

{

blog\_entry\_id: 1,

page: 2,

count: 1,

comments: \[{

name: **\"John Page\"**,

created\_on: ISODate(**\"2014-01-01T11:01:22Z\"**),

comment: **\"Not so awesome blog\"**

}\]

}

*Figure: Some example Comment buckets*

The main benefit of using buckets in this case is that we can perform a
single read to fetch 50 comments at a time, allowing for efficient
pagination.

IMPORTANT

When to use bucketing

When you have the possibility of splitting up your documents into
discreet batches, it makes sense to consider bucketing to speed up
document retrieval.

Typical cases where bucketing is appropriate are ones such as bucketing
data by hours, days or number of entries on a page (like comments
pagination).

Many-To-Many (N:M)

An *N:M* relationship is an example of a relationship between two
entities where they both might have many relationships between each
other. An example might be a *Book* that was written by many *Authors*.
At the same time an *Author* might have written many *Books*.

![](https://github.com/samirsahoo007/SQL/blob/master/images/many-to-many.png){width="6.688888888888889in"
height="2.229630358705162in"}

*N:M* relationships are modeled in the relational database by using a
join table. A good example is the relationship between books and
authors.

• An author might have authored multiple books (1:N).

• A book might have multiple authors (1:M).

This leads to an *N:M* relationship between authors of books. Let's look
at how this can be modeled.

Two Way Embedding

Embedding the books in an Author document

Model

In Two Way Embedding we will include the *Book* foreign keys under the
books field.

{

\_id: 1,

name: **\"Peter Standford\"**,

books: \[1, 2\]

}

{

\_id: 2,

name: **\"Georg Peterson\"**,

books: \[2\]

}

*Figure: Some example Author documents*

Mirroring the *Author* document, for each *Book* we include the *Author*
foreign keys under the *Author* field.

{

\_id: 1,

title: **\"A tale of two people\"**,

categories: \[**\"drama\"**\],

authors: \[1, 2\]

}

{

\_id: 2,

title: **\"A tale of two space ships\"**,

categories: \[**\"scifi\"**\],

authors: \[1\]

}

*Figure: Some example Book documents*

Queries

**var** db = db.getSisterDB(**\"library\"**);

**var** booksCollection = db.books;

**var** authorsCollection = db.authors;

**var** author = authorsCollection.findOne({name: **\"Peter
Standford\"**});

**var** books = booksCollection.find({\_id: {\$**in**:
author.books}}).toArray();

*Example 1: Fetch books by a specific author*

**var** db = db.getSisterDB(**\"library\"**);

**var** booksCollection = db.books;

**var** authorsCollection = db.authors;

**var** book = booksCollection.findOne({title: **\"A tale of two space
ships\"**});

**var** authors = authorsCollection.find({\_id: {\$**in**:
book.authors}}).toArray();

*Example 2: Fetch authors by a specific book*

As can be seen, we have to perform two queries in both directions. The
first is to find either the author or the book and the second is to
perform a \$in query to find the books or authors.

IMPORTANT

Uneven n:m relationships

Let's take the category drama that might have thousands of books in it
and with many new books consistently being added and removed. This makes
it impracticable to embed all the books in a category document. Each
book, however, can easily have categories embedded within it, as the
rate of change of categories for a specific book might be very low.

In this case we should consider One way embedding as a strategy.

One Way Embedding

The One Way Embedding strategy chooses to optimize the read performance
of a *N:M* relationship by embedding the references in one side of the
relationship. An example might be where several books belong to a few
categories but a couple categories have many books. Let's look at an
example, pulling the categories out into a separate document.

Model

{

\_id: 1,

name: **\"drama\"**

}

*Figure: A Category document*

{

\_id: 1,

title: **\"A tale of two people\"**,

categories: \[1\],

authors: \[1, 2\]

}

*Figure: A Book with Foreign Keys for Categories*

The reason we choose to embed all the references to categories in the
books is due to there being lot more books in the drama category than
categories in a book. If one embeds the books in the category document
it's easy to foresee that one could break the 16MB max document size for
certain broad categories.

Queries

**var** db = db.getSisterDB(**\"library\"**);

**var** booksCol = db.books;

**var** categoriesCol = db.categories;

**var** book = booksCol.findOne({title: **\"A tale of two space
ships\"**});

**var** categories = categoriesCol.find({\_id: {\$**in**:
book.categories}}).toArray();

*Example 3: Fetch categories for a specific book*

**var** db = db.getSisterDB(**\"library\"**);

**var** booksCollection = db.books;

**var** categoriesCollection = db.categories;

**var** category = categoriesCollection.findOne({name: **\"drama\"**});

**var** books = booksCollection.find({categories:
category.id}).toArray();

*Example 4: Fetch books for a specific category*

TIP

Establish Relationship Balance

Establish the maximum size of N and the size of M. For example if N is a
maximum of 3 categories for a book and M is a maximum of 500000 books in
a category you should pick One Way Embedding. If N is a maximum of 3 and
M is a maximum of 5 then Two Way Embedding might work well.

**Schema Design**

![](https://github.com/samirsahoo007/SQL/blob/master/images/ER_Diagram_MMORPG.png){width="6.688888888888889in"
height="5.957292213473316in"}

There are several things to keep in mind when designing a schema for
your application.

Read Ratio to Write Ratio

Determining if your application is read heavy or write heavy will lead
to how you design your schema. If your application is read heavy, you
might want to choose a schema that minimizes the amount of reads from
MongoDB.

As an example consider an auction type website. As most operations are
read operations caused by people browsing the catalog, it might makes
sense to use a denormalized schema for the product including as much
relevant information as needed to render the entire product page.

Similarly if your application is write heavy, you might want to ensure
that you use a schema that maximizes MongoDB write throughput.

Avoid Application Joins

MongoDB does not support server side joins. All joins have to be
performed in the application itself. The performance can suffer if you
are pulling back and joining a lot of data due to all the round-trips
required to bring back all the data and the time it takes to perform the
in application join. If you find your schema is depending on a lot of
joins, it might make sense to denormalize the schema in order to reduce
the number of joins.

Pre-aggregate Data

Additionally, if you find you are aggregating data in a lot of
application queries, you might want to consider pre-aggregating. One
example might be a page view counter. Instead of summing up the number
of views for a particular page on request, we can increment a view
counter for that page each time the page is viewed and use this counter
to show the number of page views.

Avoid Growing Documents (MMAP)

If, you find that your schema design creates documents that are
constantly growing in size, it will have impact on your disk IO and
database performance. Using document buckets and document pre-allocation
will help address the issues for the MMAP storage engine.

Avoid Updating Whole Documents (MMAP)

MongoDB provides for atomic operators that let you modify fields in an
existing document, and in most cases will cause an in-place update when
using the MMAP storage engine. This ensures we spend as little time as
possible re-allocating documents in memory and improves write
performance.

Pre-allocated Documents (MMAP)

If your schema grows to a known size you can avoid document moves by
pre-allocating the maximum size of the document causing all operations
on the document to be in-place updates.

Field Names Take up Space (MMAP)

In some cases documents can contain more space allocated for the field
names than the actual data stored. For this case you may want to
consider compressing your field names if you are using MMAP or switch to
using WiredTiger that supports compression using snappy or zlib.

Over Eager Indexing

You might get tempted to add all kinds of indexes to your schema. You
have to keep in mind, that each index will impact performance, as they
will need to be updated when documents change and the more indexes you
have on a collection the more overhead there will be for each write
operation. Each index also takes up space and memory so keep in mind
that over eager indexing can cause your storage size to balloon.

Custom \_id Field

You can save some space and additional indexes by overriding the meaning
of the \_id field. The only requirement is that \_id is a unique field
for the collection. For example you might have a structure that contains
a timestamp, userid and machineid allowing you to use the \_id index to
query for those fields without having to create an additional index.

Covered Indexes

If your application can leverage covered indexes, it might help
performance given that a query might be completely answerable using the
data stored in the index without materializing the underlying documents.
