# Handwritten text recognition using Azure Cognitive Services

> Note: This is a work in progress. Stay tuned to my [Twitter feed](http://twitter.com/lbugnion) for updates.

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