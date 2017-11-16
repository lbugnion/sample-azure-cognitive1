# Smart creation of a thumbnail using Azure Cognitive Services

This sample shows how to create a blob triggered Azure function in the Azure portal, configure it for input/output, and implement code using Azure cognitive services to create smart thumbnails.

The artificial intelligence analyzes the image and create a cropped version that makes sure that relevant information is displayed.

This article assumes that you already have an Azure subscription. If you don't, [you can one for free](https://github.com/lbugnion/sample-azure-general/blob/master/Doc/trial-account.md).

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

5. Enter a name for your function, for example 




## Configuring the Blob storage

## Code sample 1: Smart thumbnail with Blob Trigger

```CS
public static async Task Run(
    Stream inBlob,
    Stream outBlob, 
    TraceWriter log)
{
    int width = 320;
    int height = 320;
    bool smartCropping = true;
    string _apiKey = "22b1286a2f414d3b80bcb7a0e59c9206";
    string _apiUrlBase = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0/generateThumbnail";

    using (var httpClient = new HttpClient())
    {
        httpClient.BaseAddress = new Uri(_apiUrlBase);
        httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", _apiKey);
        using (HttpContent content = new StreamContent(inBlob))
        {
            //get response
            content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeWithQualityHeaderValue("application/octet-stream");
            var uri = $"{_apiUrlBase}?width={width}&height={height}&smartCropping={smartCropping.ToString()}";
            var response = httpClient.PostAsync(uri, content).Result;
            var responseBytes = response.Content.ReadAsByteArrayAsync().Result;

            //write to output thumb
            outBlob.Write(responseBytes, 0, responseBytes.Length);
        }
    }
}
```
