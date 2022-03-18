### Library Powered By

This library is powered by [Entity Framework Extensions](https://entityframework-extensions.net/?z=github&y=entityframework-plus)

<a href="https://entityframework-extensions.net/?z=github&y=entityframework-plus">
<kbd>
<img src="https://zzzprojects.github.io/images/logo/entityframework-extensions-pub.jpg" alt="Entity Framework Extensions" />
</kbd>
</a>

---

# Entity Framework Plus
Improve Entity Framework performance and overcome limitations with MUST-HAVE features

## Download

Full Version | NuGet | NuGet Install
------------ | :-------------: | :-------------:
Z.EntityFramework.Plus.EFCore | <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EFCore/" target="_blank"><img src="https://zzzprojects.github.io/images/nuget/efcore-full-version-v.svg" alt="download" /></a><a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EFCore/" target="_blank"><img src="https://zzzprojects.github.io/images/nuget/efcore-full-version-d.svg" alt="" /></a> | ```PM> Install-Package Z.EntityFramework.Plus.EFCore```
Z.EntityFramework.Plus.EF6 | <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF6/" target="_blank"><img src="https://zzzprojects.github.io/images/nuget/ef6-full-version-v.svg" alt="download" /></a><a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF6/" target="_blank"><img src="https://zzzprojects.github.io/images/nuget/ef6-full-version-d.svg" alt="" /></a> | ```PM> Install-Package Z.EntityFramework.Plus.EF6```
Z.EntityFramework.Plus.EF5 | <a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF5/" target="_blank"><img src="https://zzzprojects.github.io/images/nuget/ef5-full-version-v.svg" alt="download" /></a><a href="https://www.nuget.org/packages/Z.EntityFramework.Plus.EF5/" target="_blank"><img src="https://zzzprojects.github.io/images/nuget/ef5-full-version-d.svg" alt="" /></a> | ```PM> Install-Package Z.EntityFramework.Plus.EF5```

<a href="https://entityframework-plus.net/download">More download options (Full and Standalone Version)</a>

## Features
- Batch Operations
    - [Batch Delete](https://entityframework-plus.net/ef-core-batch-delete)
    - [Batch Update](https://entityframework-plus.net/ef-core-batch-update)
- LINQ
    - [LINQ Dynamic](https://entityframework-plus.net/ef-core-linq-dynamic)
- Query
    - [Query Cache](https://entityframework-plus.net/ef-core-query-cache)  
    - [Query Deferred](https://entityframework-plus.net/ef-core-query-deferred)
    - [Query DbSetFilter](https://entityframework-plus.net/query-db-set-filter)
    - [Query Filter](https://entityframework-plus.net/ef-core-query-filter)
    - [Query Future](https://entityframework-plus.net/ef-core-query-future)
    - [Query IncludeFilter](https://entityframework-plus.net/ef-core-query-include-filter)
    - [Query IncludeOptimized](https://entityframework-plus.net/ef-core-query-include-optimized)
- [Audit](https://entityframework-plus.net/ef-core-audit)

---

**Bulk Operations only available with [Entity Framework Extensions](http://entityframework-extensions.net/)**
- BulkSaveChanges
- BulkInsert
- BulkUpdate
- BulkDelete
- BulkMerge

---

## Batch Delete
Deletes multiples rows in a single database roundtrip and without loading entities in the context.

```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// DELETE all users which has been inactive for 2 years
ctx.Users.Where(x => x.LastLoginDate < DateTime.Now.AddYears(-2))
         .Delete();

// DELETE using a BatchSize
ctx.Users.Where(x => x.LastLoginDate < DateTime.Now.AddYears(-2))
         .Delete(x => x.BatchSize = 1000);
```

**Support:** EF5, EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-batch-delete)**

## Batch Update
Updates multiples rows using an expression in a single database roundtrip and without loading entities in the context.

```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// UPDATE all users which has been inactive for 2 years
ctx.Users.Where(x => x.LastLoginDate < DateTime.Now.AddYears(-2))
         .Update(x => new User() { IsSoftDeleted = 1 });
```

**Support:** EF5, EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-batch-update)**

## Query Cache
**Query cache is the second level cache for Entity Framework.**

The result of the query is returned from the cache. If the query is not cached yet, the query is materialized and cached before being returned.

You can specify cache policy and cache tag to control CacheItem expiration.

**Support:**

_Cache Policy_

```csharp
// The query is cached using default QueryCacheManager options
var countries = ctx.Countries.Where(x => x.IsActive).FromCache();

// (EF5 | EF6) The query is cached for 2 hours
var states = ctx.States.Where(x => x.IsActive).FromCache(DateTime.Now.AddHours(2));

// (EF7) The query is cached for 2 hours without any activity
var options = new MemoryCacheEntryOptions() { SlidingExpiration = TimeSpan.FromHours(2)};
var states = ctx.States.Where(x => x.IsActive).FromCache(options);
```

_Cache Tags_

```csharp
var states = db.States.Where(x => x.IsActive).FromCache("countries", "states");
var stateCount = db.States.Where(x => x.IsActive).DeferredCount().FromCache("countries", "states");

// Expire all cache entry using the "countries" tag
QueryCacheManager.ExpireTag("countries");
```

**Support:** EF5, EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-batch-update)**

## Query Deferred
**Defer the execution of a query which is normally executed to allow some features like Query Cache and Query Future.**

```csharp
// Oops! The query is already executed, we cannot use Query Cache or Query Future features
var count = ctx.Customers.Count();

// Query Cache
ctx.Customers.DeferredCount().FromCache();

// Query Future
ctx.Customers.DeferredCount().FutureValue();
```
> All LINQ extensions are supported: Count, First, FirstOrDefault, Sum, etc. 

**Support:** EF5, EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-query-deferred)**

## Query Filter
**Filter query with predicate at global, instance or query level.**

**Support:**

_Global Filter_
```csharp
// CREATE global filter
QueryFilterManager.Filter<Customer>(x => x.Where(c => c.IsActive));

var ctx = new EntityContext();

// TIP: Add this line in EntitiesContext constructor instead
QueryFilterManager.InitilizeGlobalFilter(ctx);

// SELECT * FROM Customer WHERE IsActive = true
var customer = ctx.Customers.ToList();
```

_Instance Filter_
```csharp
var ctx = new EntityContext();

// CREATE filter
ctx.Filter<Customer>(x => x.Where(c => c.IsActive));

// SELECT * FROM Customer WHERE IsActive = true
var customer = ctx.Customers.ToList();
```

_Query Filter_
```csharp
var ctx = new EntityContext();

// CREATE filter disabled
ctx.Filter<Customer>(CustomEnum.EnumValue, x => x.Where(c => c.IsActive), false);

// SELECT * FROM Customer WHERE IsActive = true
var customer = ctx.Customers.Filter(CustomEnum.EnumValue).ToList();
```

**Support:** EF5, EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-query-filter)**

## Query Future
**Query Future allow to reduce database roundtrip by batching multiple queries in the same sql command.**

All future query are stored in a pending list. When the first future query require a database roundtrip, all query are resolved in the same sql command instead of making a database roundtrip for every sql command.

**Support:**

_Future_

```csharp
// GET the states & countries list
var futureCountries = db.Countries.Where(x => x.IsActive).Future();
var futureStates = db.States.Where(x => x.IsActive).Future();

// TRIGGER all pending queries (futureCountries & futureStates)
var countries = futureCountries.ToList();
```

_FutureValue_

```csharp
// GET the first active customer and the number of avtive customers
var futureFirstCustomer = db.Customers.Where(x => x.IsActive).DeferredFirstOrDefault().FutureValue();
var futureCustomerCount = db.Customers.Where(x => x.IsActive).DeferredCount().FutureValue();

// TRIGGER all pending queries (futureFirstCustomer & futureCustomerCount)
Customer firstCustomer = futureFirstCustomer.Value;
```

**Support:** EF5, EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-query-future)**

## Query IncludeFilter
Entity Framework already support eager loading however the major drawback is you cannot control what will be included. There is no way to load only active item or load only the first 10 comments.

**EF+ Query Include** make it easy:
```csharp
var ctx = new EntityContext();

// Load only active comments
var posts = ctx.Post.IncludeFilter(x => x.Comments.Where(x => x.IsActive));
```

**Support:** EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-query-include-filter)**

## Query IncludeOptimized
Improve SQL generate by Include and filter child collections at the same times!

```csharp
var ctx = new EntityContext();

// Load only active comments using an optimized include
var posts = ctx.Post.IncludeOptimized(x => x.Comments.Where(x => x.IsActive));
```

**Support:** EF5, EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-query-include-optimized)**

## Audit
Allow to easily track changes, exclude/include entity or property and auto save audit entries in the database.

**Support:**
 - AutoSave Audit
 - Exclude & Include Entity
 - Exclude & Include Property
 - Format Value
 - Ignore Events
 - Property Unchanged
 - Soft Add & Soft Delete

```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

var ctx = new EntityContext();
// ... ctx changes ...

var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
ctx.SaveChanges(audit);

// Access to all auditing information
var entries = audit.Entries;
foreach(var entry in entries)
{
    foreach(var property in entry.Properties)
    {
    }
}
```

AutoSave audit in your database
```csharp
AuditManager.DefaultConfiguration.AutoSavePreAction = (context, audit) =>
    (context as EntityContext).AuditEntries.AddRange(audit.Entries);
```

**Support:** EF5, EF6, EF Core

**[Learn more](https://entityframework-plus.net/ef-core-audit)**

## Useful links

- [Website](https://entityframework-plus.net/)
- [Documentation](https://entityframework-plus.net/batch-delete)
- [Online Examples](https://entityframework-plus.net/online-examples) 
- You can also consult tons of Entity Framework Plus questions on 
[Stack Overflow](https://stackoverflow.com/questions/tagged/entity-framework-plus)

## Contribute

Want to help us? Your donation directly helps us maintain and grow ZZZ Free Projects. 

We can't thank you enough for your support 🙏.

👍 [One-time donation](https://zzzprojects.com/contribute)

❤️ [Become a sponsor](https://github.com/sponsors/zzzprojects) 

### Why should I contribute to this free & open-source library?
We all love free and open-source libraries! But there is a catch... nothing is free in this world.

We NEED your help. Last year alone, we spent over **3000 hours** maintaining all our open source libraries.

Contributions allow us to spend more of our time on: Bug Fix, Development, Documentation, and Support.

### How much should I contribute?
Any amount is much appreciated. All our free libraries together have more than **100 million** downloads.

If everyone could contribute a tiny amount, it would help us make the .NET community a better place to code!

Another great free way to contribute is  **spreading the word** about the library.

A **HUGE THANKS** for your help!

## More Projects

- [EntityFramework Extensions](https://entityframework-extensions.net/)
- [Dapper Plus](https://dapper-plus.net/)
- [C# Eval Expression](https://eval-expression.net/)
- and much more! 

To view all our free and paid projects, visit our [website](https://zzzprojects.com/).
