# Handwritten text recognition using Azure Cognitive Services

This sample shows how to create a blob triggered Azure function in the Azure portal, configure it for input/output, and implement code using Azure cognitive services to recognize handwritten text in an image.

The artificial intelligence analyzes the image and creates a JSON file with the words that were recognized as well as positional information about the words in the image.

This article assumes that you already have an Azure subscription. If you don't, [you can one for free](https://github.com/lbugnion/sample-azure-general/blob/master/Doc/trial-account.md).

## Configuring the blob containers

In this section, we will use the Microsoft Azure Storage Explorer to configure the blob containers. This is very easy.

1. Open the Microsoft Azure Storage Explorer. You can get more information [about how to install this tool here](https://github.com/lbugnion/sample-azure-general/blob/master/Doc/azure-explorer.md).

2. Expand the subscription under which you want to work.

3. Expand the Blob containers node.

4. Right click on Blob containers and select Create Blob Cotainer

![Creating a blob container](./Img/2017-11-16_16-17-47.png)

5. Enter a name for the new input blob container, for example ```images-text```.

6. Repeat the steps to create another blob container for the output, for example ```images-text-out```.

You're done! Remember the names ```images-text``` and ```images-text-out``` because you will need them when you create the functions.

## Creating the blob triggered function

After you [created the function application](./creating.md), you can now create the blog triggered function itself with the following steps:

1. Navigate to the function application that you just created. This is available by selecting the Function Apps menu on the left hand side.

![Function Apps in the portal](./Img/2017-11-16_12-11-30.png)

2. Select the function app that you just created and click on the + sign to create a new function.

![Creating the function](./Img/2017-11-16_12-12-57.png)

3. Click on "Custom function" under "Get Started on your own"

![Custom function](./Img/2017-11-16_12-13-39.png)

4. Scroll down to Blob Trigger with C# and click.

![Blob trigger with C#](./Img/2017-11-16_12-15-04.png)

5. Enter a name for your function, for example ```RecognizeText```

TODO CHECK THE FUNCTION'S NAME

6. Enter the path of the Azure blob storage container. This is only for the trigger, aka the "input blob". We will configure the output blob later. In our case, we use ```images-text/{name}```. Note that the ```{name}``` parameter will be the name of the file that you upload, which can be useful in the function.

![Configuring the name and Path](./Img/TODO_IMAGE)

7. Next to the "Storage account connection" combo box, click on New

![Configuring the storage account](./Img/2017-11-16_16-56-38.png)

8. In the blade opening, select the Storage account that you want to use. This is the same storage account that we configured earlier in the "Configuring the blob containers" section.

![Storage account](TODO_IMAGE)

9. Once everything is ready, click on the Create button.

Now we will configure the output blob container. We can do this at any time from the function's portal.

10. Click on the Integrate menu in the function's tree. In this menu you can see the properties that you entered earlier for the input blob.

![Monitor](./Img/2017-11-16_17-00-51.png)

11. Click on New Output

![New Output](./Img/2017-11-16_17-02-56.png)

12. Select Azure Blob Storage from the choice then click Select.

![Azure Blob Storage](./Img/2017-11-16_17-03-57.png)

13. If needed, you can change the name of the output blob parameter. This is what you will use in the Function to write to the Stream.

14. Change the path of the output container. In our sample we use ```images-thumbs/{name}```

15. Under Storage account connection, select the same connection than you selected for the input blob. Then click the Save button.

![Configuring the output blob container](./Img/2017-11-16_17-06-39.png)





## Code sample 2: Extracting handwritten text using blob trigger

```CS
public static async Task Run(Stream inBlob, Stream outBlob, TraceWriter log)
{
    string _apiKey = "22b1286a2f414d3b80bcb7a0e59c9206";
    string _apiUrlBase = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0/recognizeText";

    using (var httpClient = new HttpClient())
    {
        httpClient.BaseAddress = new Uri(_apiUrlBase);
        httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", _apiKey);
        using (HttpContent content = new StreamContent(inBlob))
        {
            //get response
            content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeWithQualityHeaderValue("application/octet-stream");
            var uri = $"{_apiUrlBase}?handwriting=true";
            var response = httpClient.PostAsync(uri, content).Result;

            string operationLocation = null;

            // The response contains the URI to retrieve the result of the process.
            if (response.IsSuccessStatusCode)
            {
                operationLocation = response.Headers.GetValues("Operation-Location").FirstOrDefault();
            }
            else
            {
                log.Error("Operation failed");
                return;
            }

            string contentString;
            int i = 0;
            do
            {
                System.Threading.Thread.Sleep(1000);
                response = await httpClient.GetAsync(operationLocation);
                contentString = await response.Content.ReadAsStringAsync();
                ++i;
            }
            while (i < 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1);

            if (i == 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1)
            {
                log.Error("\nTimeout error.\n");
                return;
            }

            using (var writer = new StreamWriter(outBlob))
            {
                writer.Write(contentString);
            }
        }
    }
}
```