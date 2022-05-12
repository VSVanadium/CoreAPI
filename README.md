# CoreAPI

It's a sample project to protect a web api using microsoft Azure Authentication. One has to generate bearer token using azure credentials( client ID and Client secret) which then can be passed in header while calling the web api.  

## Settings in Azure

Register an App in microsoft azure Active directory, which will generate few values like Application(client) ID and Directory(tenant) ID.
Also, generate client secret from Certificate & secret tab at Azure portal.


## Usage

In appsettings.json file of the project, under AzureAD section, enter the values for tenant and client ID
```
"AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "TenantId": "",
    "ClientId": "",
    "Audience": "same as clientID"
  },
```
The sample controller has decorators for Autherization
```
    [Authorize(AuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]  
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```
## Generate bearer token via following function
```
 public static async void GetAuthToken()
        {
            HttpClient httpClient = new HttpClient();

            // client id and secret from the app registration
            byte[] authHeader = Encoding.UTF8.GetBytes("CLient_ID" + ":" + "Client_Secret");
            string base64 = Convert.ToBase64String(authHeader);

            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", base64);

            StringContent body = new StringContent("grant_type=client_credentials&scope=", Encoding.UTF8, "application/x-www-form-urlencoded");

            HttpResponseMessage response = await httpClient.PostAsync("https://login.microsoftonline.com/Tenant_ID/oauth2/token", body);

            var result = response.Content.ReadAsStringAsync().Result;
            var tokenResult = JsonConvert.DeserializeObject<TokenRequest>(result);
            Console.WriteLine(tokenResult.AccessToken);


        }

 public class TokenRequest
        {
            [JsonProperty("token_type")]
            public string TokenType { get; set; }

            [JsonProperty("access_token")]
            public string AccessToken { get; set; }
        }


```

The generated token then can be used while calling the api using postman

## License
[MIT](https://choosealicense.com/licenses/mit/)
