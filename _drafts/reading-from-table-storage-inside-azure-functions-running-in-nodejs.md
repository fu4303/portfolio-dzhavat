---
layout: post
title: "Reading from Table storage inside Azure Functions running in Node.js, pt. 1"
---

**Note**: In this first post, I‘m going to show you how to read a single entity from Table storage inside an Azure function running in Node.js. In a second post, I'm going to show you how to read multiple entities. 

I spent a lot of time last week figuring out how to read entities from Table storage inside my Azure function. I don‘t know if it was me, the tutorials I found or just the concept in general but something was not working. At some point I figured it out but it had already taken me more time than necessary for such a simple task.

So I‘m writing the following guide primarily for myself but also for other people who struggle with a similar problem.

Before I start, though, I need to set some limits. The steps bellow describe how I managed to make this work while staying inside the Azure Portal. If you want to read from Table storage while working in a code editor, check out [this tutorial](https://docs.microsoft.com/en-us/azure/cosmos-db/table-storage-how-to-use-nodejs). Furthermore, I assume you already have an existing table to read from. If you don‘t have one, [read this tutorial](https://microsoft.github.io/AzureTipsAndTricks/blog/tip82.html). The table I‘m going to use is this:

<figure>
  <img src="/assets/img/reading-from-table-storage-inside-azure-functions-running-in-nodejs/example-table.jpg" alt="Example table">
  <figcaption>Example table</figcaption>
</figure>

Let‘s get started.

#### Step 1

Create a new Azure function. If you‘ve already done this, feel free to skip this step.

1. Click on the “+” next to “Functions”.
2. Click on “HTTP trigger”.
3. Give your function a name.
4. Click the “Create” button.

<figure>
  <img src="/assets/img/reading-from-table-storage-inside-azure-functions-running-in-nodejs/creating-a-new-azure-function.jpg" alt="Creating a new Azure Function">
  <figcaption>Creating a new Azure Function</figcaption>
</figure>

#### Step 2

Once the functions is created, you‘ll be redirected to a file called `index.js`, which is the actual function code. We‘ll come back to this page a bit later. Now click on to the “Integrate” tab.

<figure>
  <img src="/assets/img/reading-from-table-storage-inside-azure-functions-running-in-nodejs/default-azure-function-view.jpg" alt="Newly created Azure function">
  <figcaption>Newly created Azure function</figcaption>
</figure>

#### Step 3

On this page you can add different integrations. Right now we‘re interested in creating a new Table storage input binding.

1. Click on the “New Input” button under “Inputs”.
2. Select “Azure Table Storage” from the list.
3. Click “Select”.

<figure>
  <img src="/assets/img/reading-from-table-storage-inside-azure-functions-running-in-nodejs/select-storage-table-binding.jpg" alt="Creating an Azure Table storage binding">
  <figcaption>Creating an Azure Table storage binding</figcaption>
</figure>

#### Step 4

Next, you‘ll be presented with a page where you can set some settings.

1. “Table parameter name” is used for indentifying the binding in the code. It‘s already set to `inputTable`. You can leave it as is or change it to whatever you want. Just remember it because you‘re going to use it later.
2. “Table name”, as the name suggest, is the name of the storage table that will be used. In my case it‘s `example`.
3. “Partition key” is the name of the partition key column in the table. In my case that‘s `Users`.
4. “Row key” is the name of the row key column in the table. In my case that‘s `1`.
5. Finally, don‘t forget to “Save” everything.

<figure>
  <img src="/assets/img/reading-from-table-storage-inside-azure-functions-running-in-nodejs/setting-up-azure-storage.jpg" alt="Setting up Azure Table storage">
  <figcaption>Setting up Azure Table storage</figcaption>
</figure>

It‘s important to note here that even though “Partition key” and “Row key” fields are optional, inputting a value in any of them makes the other one required.

#### Step 5

Now‘s time to come back to the `index.js` file you saw in step 2. Open it and replace its content with the following code (and don‘t forget to replace `FirstName` with a column name found in your table):

```js
module.exports = async function (context, req) {
    context.log(context.bindings.inputTable.FirstName);
};
```

* The `bindings` property contains the input binding to Table storage created during step 4.
* `inputTable` is how we named our table parameter. In my case, this reffers to the `example` table.
* `FirstName` is a column name in my table.

Press “Save and run”. While the function is executing you‘ll see some logs in the Logs tab underneath. If things are set up correctly you‘ll see the value logged out.

<figure>
  <img src="/assets/img/reading-from-table-storage-inside-azure-functions-running-in-nodejs/logging-out-the-value-of-a-single-entity.jpg" alt="Logging out the value of a single entity">
  <figcaption>Logging out the value of a single entity</figcaption>
</figure>

That‘s it! Now you know how to read a single entity from Table storage in your Azure function. It wasn‘t hard at all, was it? Stay tuned for the second part of this post where I‘m going to share how to read multiple entities. 


