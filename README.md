# Entity Framework Text Templating Improvements

TextTemplating files to use with Entity Framework as Code Generation items, avoiding Circular Reference when serialized (json) + DB methods (InsertALotsOfEntity)

The purpose here was to serialise any entity from the framework as json.
Two problems were happening:
* Circular Reference Exception (due to navigation properties)
* Proxy class name instead of declared

Also, I took this opportunity to add a DBUtils code generation to add the most basic methods (Get, AddOrUpdate, etc...) but also a InsertALotsOfEntity method to insert more than a thousand entity in a row and very quickly.

## Prerequisites

* Software
	* Visual Studio 2012 - 2017
	* Entity Framework 5
	* Compatible with EF6

* Components
	* Applicable under .edmx file

![.edmx basic structure](/readme.assets/edmx_structure.png)

## Getting Started

Copy/paste the content of [ModelName].Context.tt and [ModelName].tt, add new Code generation item with the last text templating : [ModelName].tt (with the edmx file open)
(I am replacing [ModelName] by the entity model name)

## How to use

__1. Get an entity from DB__

``` CSharp
    Bid proxyBid = null;
    using (BidderModelContainer context = new BidderModelContainer())
    {
        proxyBid = context.Bids.FirstOrDefault(_ => _.Id == bidId);
    }
    //proxyBid will result as Bid_5BB20962C2C78D24A9CCF63D57FEDDDA5244F675D04549D61FFE37CF20C46439 instance

    Bid bidToSerialize = new Bid(proxyBid);
    //bidToSerialize will result as Bid instance

```

__2. Use DBUtils__

  * Use same DB context for multiple instructions
  ``` CSharp
    IEnumerable<Bid> bids = null;
    using (BidderModelContainer context = new BidderModelContainer())
    {
        bids = BidDBUtils.GetBids(context); //Get all Bid list

        Bid bidToUpdate = bids.FirstOrDefault();
        bidToUpdate.Name = "New Bid Name";
        BidDBUtils.AddOrUpdateEntity(bidToUpdate, context); // updating one Bid instance Name property

        List<ValuesHit> values = new List<ValuesHit>();
        for (int i = 0; i < 100; i++)
        {
            values.Add(new ValuesHit() { Value = i.ToString() });
        }
        ValuesHitDBUtils.AddOrUpdateEntities(values, context); //Adding 100 new ValuesHit instances

        context.SaveChanges();
    }
  ```

  * Improve Massive Insert by using __InsertALotsOf__ method
  Using the following code, I compare two methods to insert massive amount of instance within SQL Server DB.
  
    A _ValuesHit_ object is a quite simple structure with only 2 properties (Id, Value),  since the Id property will be defined at the insert time (Primary key), I only populate the Value property.
  ``` CSharp
    List<ValuesHit> values = new List<ValuesHit>();
    for (int i = 0; i < 2000; i++)
    {
        values.Add(new ValuesHit() { Value = i.ToString() });
    }

    ValuesHitDBUtils.AddOrUpdateEntities(values);       //here 9496ms to insert 2000 rows
    ValuesHitDBUtils.InsertALotsOfValuesHit(values);    //here 71ms to insert 2000 rows
    
  ```
Here is a table to compare __the execution time__ of the two methods:

NB Rows to insert | via AddOrUpdateFromEntity | via InsertALotsOf
--- | --- | ---
2000 | 9496ms (~10s) | 71ms
20000 | 79885ms (~1min 20s) | 295ms
100000 | 404116ms (~6min 44s) | 1255ms

### Legal
Copyright © 2018 Romain Stiffler [https://rstiffler.net](https://rstiffler.net).

This is licensed under the terms of the [MIT license](License.txt).

### ENJOY !


