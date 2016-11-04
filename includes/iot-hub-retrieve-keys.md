## IoT Hub キーを取得します。
新しい IoT Hub の認証キーを表示します。

1. 次のメソッドを Program.cs に追加します。
   
    ```
    static void ShowIoTHubKeys(ResourceManagementClient client, string token)
    {
   
    }
    ```
2. 次のコードを **ShowIoTHubKeys** メソッドに追加して、認証キーをコンソールに出力します。
   
    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
        new AuthenticationHeaderValue("Bearer", token);
    var httpsRepsonse = client.HttpClient.PostAsync(
        string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}/listKeys?api-version=2015-08-15-preview", 
        subscriptionId, rgName, iotHubName),
        null).Result;
   
    Console.WriteLine("Keys: {0}, 
        httpsRepsonse.Content.ReadAsStringAsync().Result);
    ```

<!---HONumber=AcomDC_1203_2015-->