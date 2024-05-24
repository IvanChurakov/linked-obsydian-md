[Link to the course]([https://learn.microsoft.com/en-us/training/modules/implement-azure-app-configuration/](https://learn.microsoft.com/en-us/training/modules/work-with-data-source-limits-powerapps-canvas-app/)
# Delegation overview

Before picking a data source in Power Apps, understanding delegation is key. Delegation helps Power Apps work better with data sources by reducing how much data gets moved around. Basically, when possible, it lets Power Apps hand over some data processing work to the source itself. This includes tasks like filtering, searching, and sorting.

Whether your data can be delegated depends on both the source and the function you're using. If you're dealing with lots of data and need the data source to do heavy lifting like filtering, it might be wise to shift or copy your data to a system that's delegation-friendly, like Microsoft Dataverse.

To do this, you can use the Data Integrator tool to transfer data into Dataverse from another source.
## Delegation in action

Here’s an example to help you understand delegation. You have a list of 5,000 projects stored in SharePoint. The **ProjectStatus** column stores the values **Active** or **Inactive**. Half (2,500) of the records are set to each of these values. You could show the list in a gallery and filter it by using this formula.

`Filter(SharePointList, ProjectStatus = "Active")`

Because the **Filter** function is delegable to a SharePoint data source, Power Apps would send your formula to SharePoint. SharePoint would process all 5,000 records and return to Power Apps the 2,500 records for which **ProjectStatus** is set **Active**. Those records would all be available in your gallery. In this scenario, Power Apps didn't process any data, and only the matching records were sent from SharePoint to Power Apps, which is efficient.
## When delegation isn't available

Some functions can't be delegated to some data sources. An example of a non-delegable action is the **Search** function against the SharePoint data source. The **Search** function is similar to **Filter**, but you can use **Search** to check across multiple fields and to find partial matches. For example, `Search(SharePointList, "Rob", "FirstName","LastName")` would return all of the records where the string "rob" is in either the **FirstName** or the **LastName** column. In this example, Power Apps would return records for Robert Smith, Rob Jones, and Suzy Robinson.

The **Search** function doesn't work with SharePoint as a delegable action, which means Power Apps has to handle the records itself. Initially, all the records are sent from SharePoint to Power Apps. By default, SharePoint only sends the first 500 records, not the first 500 matching your formula but the first 500 in the entire data set.

For instance, if you use this formula in your gallery, SharePoint sends Power Apps the initial 500 records from the list, and then Power Apps performs the search locally. Imagine your list has 5,000 items; the remaining 4,500 records aren't processed or shown.

You can adjust the default limit of 500 records to a maximum of 2,000 in Power Apps Studio's advanced settings. However, even with this increase, if you had 5,000 items, 3,000 records would still not be processed or displayed.

## Consider delegation when choosing a data source

Delegation gets its own module because it's crucial when you're selecting a data source. Think about the specific functions you use, such as Search, and also the volume of data you are handling. These factors play a large role in picking the perfect data source for your app.

In the following unit, you learn more about how delegation works with various data sources.
# Functions, predicates, and data sources combine to determine delegation

Figuring out when delegation applies depends on various factors, starting with the data source itself. Take a look at this table for Microsoft Dataverse functions and their delegation support:

- **Yes**: Data source handles processing across all records.
- **No**: Data source sends only the initial 500 records (default) to Power Apps, which then processes the function locally.

![[Pasted image 20240524165815.png | center]]

1. Numbers with arithmetic expressions like `Filter(table, field + 10 > 100)` aren't delegable. Language and TimeZone aren't delegable.

2. Doesn't support Trim[Ends] or Len. Supports other functions such as Left, Mid, Right, Upper, Lower, Replace, and Substitute.

3. DateTime can be delegated except for DateTime functions Now() and Today().

4. Supports comparisons. For example, Filter(TableName, MyCol = Blank()).

5. The aggregate functions are limited to a collection of 50,000 records. If needed, use the Filter function to select 50,000 records from a larger set before using the aggregate function.

[Dataverse](https://learn.microsoft.com/en-us/connectors/commondataserviceforapps/) has more info about using the Dataverse as a data source, and about its delegable functions.

This table is only for supported delegable functions if you use the Dataverse as a data source. But what if you use a different data source, like SharePoint or SQL?
## Other data sources: SharePoint and SQL

When working with SharePoint or SQL as your data source, it's crucial to check their supported delegable functions by referring to their associated documentation. As mentioned earlier, each data source has its own set of delegable and non-delegable functions.

Before diving into app creation, we highly recommend exploring these differences among data sources. This helps in understanding their capabilities and limitations upfront. Every Power Apps project comes with unique business needs, so ensuring that your chosen data source supports those needs and the required functions for your data volume is key.

Additionally, if you use the Filter or LookUp function, then you also use a predicate. The predicate is what allows you to evaluate the formula. The function `FirstName = "Rob"` uses the = predicate. Some data sources don't support certain predicates. For example, Salesforce doesn't support the IsBlank predicate. So, although the formula `Filter(SalesforceCustomers, Name = "Contoso")` is delegable, the formula `Filter(SalesforceCustomers, IsBlank(Name))` isn't delegable.
## The Column type can also factor in

Column types wield a surprising influence on delegation possibilities. Take complex columns, such as SharePoint lookup columns—they're not delegation-friendly. Their intricate logic means Power Apps can only process them locally.

The silver lining? Power Apps has your back! It offers visual warnings for these issues. Imagine a blue underline highlighting a formula in your app—this is your cue! It signals a delegation hiccup that could mean not fetching all your records. Correcting it ensures your app performs seamlessly.

In the next section you'll learn more about delegation warnings, limits, and how to work around them if you ever run into a delegation issue.

# Delegation warnings, limits, and non-delegable functions

Power Apps uses visuals to help you, the app maker, understand when delegation is occurring. The maker portal also has one setting you can adjust to increase the amount of data returned when delegation isn't possible.
## Delegation warnings

Anytime you use a non-delegable function, Power Apps underlines it with a blue line and displays a yellow warning triangle as shown below.

![[Pasted image 20240524171012.png | center]]

This gives you a clear visual indicator that delegation isn't happening, which means you might not see all of your data. It's important to understand a couple of things about this visual indicator.

- Power Apps provides this warning whatever the size of your data source. Even if your data source only has a few items and delegation isn't technically causing you a problem, the warning still shows. Remember the first 500 items are returned by default and processed locally. The warning appears anytime that your formula isn't delegated.

- The warning indicator only processes through the first thing that causes delegation. Notice in the above screenshot that only the underlined field "FirstName" is in blue. That is because it was the first item that caused delegation. "LastName" would also cause delegation in this scenario. This can be confusing because people try to troubleshoot what is the difference between FirstName and LastName instead of the real issue, which is the Search function. If you come across this confusion, rearrange your formula. This validates and whichever field is first shows the issue.

![[Pasted image 20240524171312.png | center]]

- This visual indicator is only present when you are in the maker portal, building the app. When a user is running the app, they don't receive any notification that delegation isn't occurring and that they might only be seeing partial results. Keep this in mind when designing your app and build accordingly.
## Change the number of records returned when delegation isn't available

When a formula can't delegate to the data source for any reason, then by default Power Apps retrieves the first 500 records from that data source and then processes the formula locally. Power Apps does support adjusting this limit from 1 to 2000. You can adjust this limit in the Advanced settings.

1. From the Maker portal, select **File** in the upper-left corner.
2. In the left-most menu, select **Settings**.
3. Under **App settings**, select **Advanced settings**    
4. Set the Data row limit for non-delegable queries for any value between 1 to 2000.
5. After you set the limit, select the **arrow** in the upper left to save your change and return to the Maker portal.

![[Pasted image 20240524174736.png | center]]

There are two primary reasons that you might want to adjust this limit.

- To increase the limit if you're working with data where 500 records aren't enough, but less than 2000 is. For example, if you have a customer list and you know you'll never have more than 1000 customers, then you could design your app to ignore delegation and always return all 1000 records.

- To lower the limit to 1 or 10 to help with testing. If you run into scenarios where you aren't sure if a non-delegable function is causing problems with your app, you can lower the limit and then test. If you set the limit to 1 and your gallery is only presenting one record, you know that you had a non-delegable function. This setting speeds up your troubleshooting process.

## Non-delegable functions

In the previous unit, you learned about the functions that are delegable and how they relate to the various data sources. These other functions, not covered in that unit, aren't delegable. The following are notable functions that don't support delegation.

- First, FirstN, Last, LastN
- Choices
- Concat
- Collect, ClearCollect (Neither of these functions return a delegation warning, but they aren't delegable)
- CountIf, RemoveIf, UpdateIf
- GroupBy, Ungroup

All of these functions aren't delegable. So by adding them to a formula you might take a previously delegable function and make it not delegable, as seen in the previous example.
## Partially supported delegable functions

Although Products and Suppliers are potentially delegable data sources, and LookUp function falls within the delegable category, the AddColumns function possesses partial delegability. So, the outcome of the entire formula remains constrained to the initial segment of the Products data source.

While the LookUp function and its associated data source allow for delegation, facilitating the discovery of Suppliers across a vast dataset, it comes with a caveat. LookUp necessitates separate queries to the data source for each of the initial records in Products, resulting in increased network activity. However, if the Suppliers dataset is relatively small and remains stable, an alternative approach involves caching the data source within the app. Employing a Collect call during app initialization (using OnVisible on the opening screen) allows subsequent LookUp operations directly within the cached data source, mitigating network chatter.
# Check your knowledge

1. What happens when you delegate a function to a data source?

- [ ] Only the first 500 records return from the data source.
- [ ] The first 500 records process and then the matches are returned.
- [x] The data source processes the function against all of the data and returns only the matches.
- [ ] A maximum of 2000 records return.

2. What should you do when you see the warning triangle and blue line that delegation isn't occurring?

- [ ] Adjust the row limit from 500 to 2000 records and then ignore the warning.
- [x] Determine if the limited returned data causes issues for the functionality of your app and design your app as appropriate.
- [ ] Ignore it, warnings aren't significant and the user knows how to deal with the warning.
- [ ] Try a different function because Power Apps doesn't run if there are warnings.

3. When combining delegable and non-delegable functions in a formula what happens?

- [ ] The data is only limited for the non-delegable portion, which in no way affects the delegable functions.
- [ ] You can't combine delegable and non-delegable functions in the same formula.
- [ ] Power Apps generates errors only if the app has more than 2000 records.
- [x] The delegable functions might become non-delegable reducing the amount of data returned by the formula.