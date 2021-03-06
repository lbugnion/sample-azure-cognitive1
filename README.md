# Cognitive services in Azure Functions

> This sample is based on the demos shown at the [ISTA conference in Sofia, Bulgaria](http://galasoft.ch/presentations/2017020) in November 2017.

In this sample, I will describe how to create two Azure functions in the Azure web portal and use them to call Azure cognitive services to perform the following operations:

- [Smart creation of a thumbnail](./Doc/thumbnail.md)

- [Handwritten text recognition](./Doc/text-recognition.md)

We use an Azure Function with a blob trigger to execute the code. Of course it would be easy to adapt this sample to call other cognitive services, or to change the trigger and the output, for example to use an [HTTP triggered function](https://github.com/lbugnion/sample-azure-general/blob/master/Doc/functions-http.md) with a POST request, or any other desired scenario.

Similarly, even though this sample shows the functions being created and configured in the Azure web portal, it would be easy to do the same in a Visual Studio environment. I wrote earlier about creating a blob triggered function in Visual Studio. Copying the content of the code samples described here in the Visual Studio environment should work seamlessly.

> Note: You can find an example of creating a [Blob triggered Azure Function in Visual Studio here](https://github.com/lbugnion/sample-azure-general/blob/master/Doc/functions-blob.md).

Hopefully these samples are useful to understand blob triggered Azure functions as well as simple cognitive services.

1. [Creating the function application in the portal](./Doc/creating.md)

2. [Smart creation of a thumbnail using Azure Cognitive Services](./Doc/thumbnail.md)

3. [Handwritten text recognition using Azure Cognitive Services](./Doc/text-recognition.md)