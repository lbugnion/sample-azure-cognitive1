# Cognitive services in Azure Functions

> This sample is based on the demos shown at the [ISTA conference in Sofia, Bulgaria](TODO_LINK) in November 2017.

In this sample, I will describe how to create two Azure functions in the Azure web portal and use them to call Azure cognitive services to perform the following operations:

- [Smart creation of a thumbnail](./Doc/thumbnail.md)

- [Handwritten text recognition](./Doc/text-recgnition.md)

This sample uses an Azure Function with a blob trigger to execute the code. Of course it would be easy to adapt this sample to call other cognitive services, or to change the trigger and the output, for example to use an [HTTP triggered function](TODO_LINK) with a POST request, or any other desired scenario.

Similarly, even though this sample shows the functions being created and configured in the Azure web portal, it would be easy to do the same in a Visual Studio environment. I wrote earlier about creating a blob triggered function in Visual Studio. Copying the content of the code samples described here in the Visual Studio environment should work seamlessly.

Hopefully these samples are useful to understand blob triggered Azure functions as well as simple cognitive services.

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