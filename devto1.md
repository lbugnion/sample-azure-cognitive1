# Using Azure Functions to call cognitive services

In this sample, I will describe how to create two [Azure functions](TODO) in the Azure web portal and use them to call [Azure cognitive services](TODO) to perform the following operations:

- [Smart creation of a thumbnail](TODO)

- [Handwritten text recognition](TODO)

> Note: This will be a 3-posts series. In this article here, we will talk about the creation of the Azure Functions application.

We will use an Azure Function with a [blob trigger](TODO) to execute the code. Of course it would be easy to adapt this sample to call other cognitive services, or to change the trigger and the output, for example to use an [HTTP triggered function](TODO) with a POST request, or any other desired scenario.

Similarly, even though this sample shows the functions being created and configured in the Azure web portal, it would be easy to do the same in a Visual Studio environment. I wrote earlier about creating a blob triggered function in Visual Studio. Copying the content of the code samples described here in the Visual Studio environment should work seamlessly.

> Note: You can find an example of creating a [Blob triggered Azure Function in Visual Studio here](https://github.com/lbugnion/sample-azure-general/blob/master/Doc/functions-blob.md).

Hopefully these samples are useful to understand blob triggered Azure functions as well as simple cognitive services.

## Creating the function application in the portal

1. Navigate to [the Azure web portal](http://portal.azure.com).

> You will need an Azure account to be able to perform the next steps. You can get [a free Azure account](TODO) with 200 U$ credit and the possibility to use some of our services for a trial period of one year!

2. Click on Create a resource.

![Create a resource](./Img/2017-11-16_11-56-25.png)

3. In the Azure Marketplace, select Serverless Function App (or Function App).

![Serverless Function App](./Img/2017-11-16_11-57-45.png)

4. Fill the information.
- App Name: Any unique name (in this sample I use LbCognitive).
- Subscription: The Azure subscription to which this resource will be associated.
- Resource group: You can either use an existing Resource group, or create one for this application. It's just a logical way to group and manage resources used for an Azure application.
- OS: You can choose between Windows and Linux
- Hosting plan: You can start with a Consumption plan, where your function will only be billed when it is used. Later if usage is growing, you can select an App service plan instead.
- Location: Select a data center close to your users.
- Storage: Create a new storage account where the blob containers will be placed, or select an existing one. It makes sense for the storage account to be in the same region as the function itself.
- Applications Insights: Turn this on if you want to use additional analytics on your function's usage.

5. Click on Create.

![Creating the Function](./Img/2017-11-16_12-04-59.png)

## What's next?

Now that we have the Functions application ready, we can be creative and add actual Functions to run our code. In the next article in this series, we will see how to use a blob triggered Function to create a smart thumbnail for a picture. Finally in the last article we will use another blob triggered Function to recognize hand-written text in a picture.

Stay tuned and happy coding!

Laurent