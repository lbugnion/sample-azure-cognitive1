# Smart creation of a thumbnail using Azure Cognitive Services

> Note: This is a work in progress. Stay tuned to my [Twitter feed](http://twitter.com/lbugnion) for updates.

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
