---
title: 'クイックスタート: Translator を使ってみる'
titleSuffix: Azure Cognitive Services
description: Translator サービスを使用して、テキストの翻訳、テキストの表記変換、言語の検出などを行う方法について説明します。 例は、C#、Java、JavaScript、および Python で提供されています。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: erhopf
ms.custom: cog-serv-seo-aug-2020
keywords: Translator, Translator サービス, テキストの翻訳, テキストの表記変換, 言語の検出
ms.openlocfilehash: f0397921b4ab29a16ee5259ce19502cad092eb83
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713033"
---
# <a name="quickstart-get-started-with-translator"></a>クイックスタート: Translator を使ってみる

このクイックスタートでは、REST を介して Translator サービスを使用する方法について説明します。 基本的な例から始めて、開発時によく使用される、次のような主要な構成オプションに進みます。

* [翻訳](#translate-text)
* [音訳](#transliterate-text)
* [言語の識別と検出](#detect-language)
* [文の長さの計算](#get-sentence-length)
* [翻訳の代替候補](#dictionary-lookup-alternate-translations)と[文中における単語の使用例](#dictionary-examples-translations-in-context)の取得

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* Azure サブスクリプションを用意したら、Azure portal で [Translator リソースを作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
  * 自分のアプリケーションを Translator サービスに接続するには、リソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (F0) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="platform-setup"></a>プラットフォームの設定

# <a name="c"></a>[C#](#tab/csharp)

* 新しいプロジェクトを作成します (`dotnet new console -o your_project_name`)。
* Program.cs を以下の C# コードで置き換えます。
* Program.cs でサブスクリプション キーとエンドポイントの値を設定します。
* [.NET CLI を使用して Newtonsoft.Json を追加します](https://www.nuget.org/packages/Newtonsoft.Json/)。
* プロジェクト ディレクトリからプログラムを実行します (``dotnet run``)。

# <a name="go"></a>[Go](#tab/go)

* 任意のコード エディターで新しい Go プロジェクトを作成します。
* 次に示すコードを追加します。
* `subscriptionKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
* "go" という拡張子でファイルを保存します。
* Go がインストールされているコンピューターのコマンド プロンプトを開きます。
* ファイルをビルドします (例: "go build example-code.go")。
* ファイルを実行します (例: "example-code")。

# <a name="java"></a>[Java](#tab/java)

* 自分のプロジェクト用に作業ディレクトリを作成します (例: `mkdir sample-project`)。
* Gradle を使用して自分のプロジェクトを初期化します (`gradle init --type basic`)。 **DSL** を選択するよう求められたら、**Kotlin** を選択します。
* `build.gradle.kts` を更新します。 サンプルに応じて `mainClassName` を更新する必要があることに注意してください。
  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "<NAME OF YOUR CLASS>"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
  }
  ```
* Java ファイルを作成し、提供されているサンプルからコードをコピーします。 忘れずに自分のサブスクリプション キーを追加してください。
* サンプルを実行します (`gradle run`)。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* 任意の IDE またはエディターで新しいプロジェクトを作成します。
* いずれかのサンプルから自分のプロジェクトにコードをコピーします。
* サブスクリプション キーを設定します。
* プログラムを実行します。 (例: `node Translate.js`)。

# <a name="python"></a>[Python](#tab/python)

* 任意の IDE またはエディターで新しいプロジェクトを作成します。
* いずれかのサンプルから自分のプロジェクトにコードをコピーします。
* サブスクリプション キーを設定します。
* プログラムを実行します。 (例: `python translate.py`)。

---

## <a name="headers"></a>ヘッダー 

REST を介して Translator サービスを呼び出すときは、必ず各要求に次のヘッダーを含める必要があります。 ご安心ください。以降の各セクションのサンプル コードにはヘッダーが含まれています。 

<table width="100%">
  <th width="20%">ヘッダー</th>
  <th>説明</th>
  <tr>
    <td>認証ヘッダー</td>
    <td>"<em>必須の要求ヘッダー</em>" です。<br/><code>Ocp-Apim-Subscription-Key</code><br/><br/>"<em>Cognitive Services リソースを使用している場合は、必須の要求ヘッダーです。Translator リソースを使用している場合は、省略可能です。</em>"<br/><code>Ocp-Apim-Subscription-Region</code><br/><br/><a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">認証に使用できるオプション</a>に関するページをご覧ください。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>"<em>必須の要求ヘッダー</em>" です。<br/>ペイロードのコンテンツ タイプを指定します。<br/> 指定できる値は <code>application/json; charset=UTF-8</code> です。</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>"<em>必須の要求ヘッダー</em>" です。<br/>要求本文の長さです。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>オプション</em>。<br/>要求を一意に識別する、クライアントで生成された GUID。 <code>ClientTraceId</code> という名前のクエリ パラメーターを使用してクエリ文字列内にトレース ID を含める場合、このヘッダーは省略できます。</td>
  </tr>
</table> 

## <a name="keys-and-endpoints"></a>[Keys and endpoints]\(キーとエンドポイント)

このページのサンプルでは、わかりやすくするために、ハードコーディングされたキーとエンドポイントを使用しています。 **終わったらコードからキーを削除し**、**公開しない** よう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../cognitive-services-security.md)に関するページを参照してください。

## <a name="translate-text"></a>テキストを翻訳する 

Translator サービスの主要な操作は、テキストの翻訳です。 このセクションでは、1 つのソース (`from`) を受け取り、2 つの出力 (`to`) を提供する要求を作成します。 次に、要求と応答の両方を調整するために使用できるいくつかのパラメーターを確認します。 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";
    
    static async Task Main(string[] args)
    {
        // Input and output languages are defined as parameters.
        string route = "/translate?api-version=3.0&from=en&to=de&to=it";
        string textToTranslate = "Hello, world!";
        object[] body = new object[] { new { Text = textToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "de")
    q.Add("to", "it")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello, world!"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```


# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "de")
        .addQueryParameter("to", "it")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello World!\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```Javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': ['de', 'it']
    },
    data: [{
        'text': 'Hello World!'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': ['de', 'it']
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello World!'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

呼び出しが成功すると、次の応答が表示されます。 

```JSON
[
    {
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="detect-language"></a>言語を検出する

翻訳する必要があるのに、Translator サービスに送信するテキストの言語がわからない場合は、言語の検出操作を使用できます。 ソース テキストの言語を識別する方法は複数あります。 このセクションでは、`translate` エンドポイントと `detect` エンドポイントを使用して、言語の検出を使用する方法について説明します。 

### <a name="detect-source-language-during-translation"></a>翻訳中にソース言語を検出する

翻訳要求に `from` パラメーターが含まれていない場合は、Translator サービスによってソース テキストの言語の検出が試行されます。 応答では、検出された言語 (`language`) と信頼度スコア (`score`) が返されます。 `score` が `1.0` に近いほど、検出結果の正しさの信頼度が増します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";
    
    static async Task Main(string[] args)
    {
        // Output languages are defined as parameters, input language detected.
        string route = "/translate?api-version=3.0&to=de&to=it";
        string textToTranslate = "Hello, world!";
        object[] body = new object[] { new { Text = textToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```


# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "de")
    q.Add("to", "it")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello, world!"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "de")
        .addQueryParameter("to", "it")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello World!\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': ['de', 'it']
    },
    data: [{
        'text': 'Hello World!'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': ['de', 'it']
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello World!'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

呼び出しが成功すると、次の応答が表示されます。 

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

### <a name="detect-source-language-without-translation"></a>翻訳せずにソース言語を検出する

Translator サービスでは、翻訳を実行せずに、ソース テキストの言語を検出することもできます。 これを行うには、[`/detect`](./reference/v3-0-detect.md) エンドポイントを使用します。 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";    
    
    static async Task Main(string[] args)
    {
        // Just detect language
        string route = "/detect?api-version=3.0";
        string textToLangDetect = "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.";
        object[] body = new object[] { new { Text = textToLangDetect } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";   

    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/detect?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
         log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Detect {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/detect")
        .addQueryParameter("api-version", "3.0")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Detect detectRequest = new Detect();
            String response = detectRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/detect',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0'
    },
    data: [{
        'text': 'Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/detect'
constructed_url = endpoint + path

params = {
    'api-version': '3.0'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```
---

`/detect` エンドポイントを使用した場合、応答には検出の代替候補が含まれます。また、検出されたすべての言語について、翻訳と表記変換がサポートされているかどうかを確認することができます。 呼び出しが成功すると、次の応答が表示されます。 

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "nl",
                "score": 0.92
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "sk",
                "score": 0.77
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "de",
        "score": 1.0
    }
]
```

## <a name="transliterate-text"></a>テキストを表記変換する 

表記変換は、音声の類似性に基づいて、単語や語句の書記法 (アルファベット) を、ある言語から別の言語のものに変換するプロセスです。 表記変換を使用すると、たとえば、"สวัสดี" (`thai`) を "sawatdi" (`latn`) に変換することができます。 表記変換を実行する方法は複数あります。 このセクションでは、`translate` エンドポイントと `transliterate` エンドポイントを使用して、言語の検出を使用する方法について説明します。 

### <a name="transliterate-during-translation"></a>翻訳中に表記変換する

ソースとは異なるアルファベット (または音素) を使用する言語に翻訳する場合は、表記変換が必要になることがあります。 この例では、"Hello" を英語からタイ語に翻訳します。 タイ語の翻訳の取得に加え、ラテン アルファベットを使用して、翻訳された語句の表記変換を取得します。

`translate` エンドポイントから表記変換を取得するには、`toScript` パラメーターを使用します。

> [!NOTE]
> 使用できる言語と表記変換オプションの完全な一覧については、[言語サポート](language-support.md)に関するページを参照してください。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";    
    
    static async Task Main(string[] args)
    {
        // Output language defined as parameter, with toScript set to latn
        string route = "/translate?api-version=3.0&to=th&toScript=latn";
        string textToTransliterate = "Hello";
        object[] body = new object[] { new { Text = textToTransliterate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";   
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "th")
    q.Add("toScript", "latn")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "th")
        .addQueryParameter("toScript", "latn")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'to': 'th',
        'toScript': 'latn'
    },
    data: [{
        'text': 'Hello'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```Python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': 'th',
    'toScript': 'latn'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

呼び出しが成功すると、次の応答が表示されます。 `translate` エンドポイントからの応答には、検出されたソース言語と信頼度スコア、出力言語のアルファベットを使用した翻訳、およびラテン アルファベットを使用した表記変換が含まれていることに注目してください。

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "สวัสดี",
                "to": "th",
                "transliteration": {
                    "script": "Latn",
                    "text": "sawatdi"
                }
            }
        ]
    }
]
```

### <a name="transliterate-without-translation"></a>翻訳せずに表記変換する

`transliterate` エンドポイントを使用して表記変換を取得することもできます。 表記変換エンドポイントを使用する場合は、ソース言語 (`language`)、ソースの書記法またはアルファベット (`fromScript`)、および出力の書記法またはアルファベット (`toScript`) をパラメーターとして指定する必要があります。 この例では、สวัสดี の表記変換を取得します。 

> [!NOTE]
> 使用できる言語と表記変換オプションの完全な一覧については、[言語サポート](language-support.md)に関するページを参照してください。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";   
    
    static async Task Main(string[] args)
    {
        // For a complete list of options, see API reference.
        // Input and output languages are defined as parameters.
        string route = "/translate?api-version=3.0&to=th&toScript=latn";
        string textToTransliterate = "Hello";
        object[] body = new object[] { new { Text = textToTransliterate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/transliterate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("language", "th")
    q.Add("fromScript", "thai")
    q.Add("toScript", "latn")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "สวัสดี"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Transliterate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/transliterate")
            .addQueryParameter("api-version", "3.0")
            .addQueryParameter("language", "th")
            .addQueryParameter("fromScript", "thai")
            .addQueryParameter("toScript", "latn")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"สวัสดี\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Transliterate transliterateRequest = new Transliterate();
            String response = transliterateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/transliterate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'language': 'th',
        'fromScript': 'thai',
        'toScript': 'latn'
    },
    data: [{
        'text': 'สวัสดี'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/transliterate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'language': 'th',
    'fromScript': 'thai',
    'toScript': 'latn'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'สวัสดี'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, indent=4, separators=(',', ': ')))
```

---

呼び出しが成功すると、次の応答が表示されます。 `translate` エンドポイントの呼び出しとは異なり、`transliterate` では `script` と出力 `text` のみが返されます。 

```json
[
    {
        "script": "latn",
        "text": "sawatdi"
    }
]
```

## <a name="get-sentence-length"></a>文の長さを取得する

Translator サービスを使用すると、一文または一連の文の文字数を取得できます。 応答は、検出された各文の文字数を含む配列として返されます。 文の長さは、`translate` および `breaksentence` エンドポイントを使用して取得できます。

### <a name="get-sentence-length-during-translation"></a>翻訳中に文の長さを取得する

`translate` エンドポイントを使用して、ソース テキストと翻訳出力の両方の文字数を取得できます。 文の長さ (`srcSenLen` および `transSenLen`) を返すには、`includeSentenceLength` パラメーターを `True` に設定する必要があります。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";   
    
    static async Task Main(string[] args)
    {
        // Include sentence length details.
        string route = "/translate?api-version=3.0&to=es&includeSentenceLength=true";
        string sentencesToCount = 
                "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine.";
        object[] body = new object[] { new { Text = sentencesToCount } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "es")
    q.Add("includeSentenceLength", "true")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "es")
        .addQueryParameter("includeSentenceLength", "true")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'to': 'es',
        'includeSentenceLength': true
    },
    data: [{
        'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': 'es',
    'includeSentenceLength': True
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

呼び出しが成功すると、次の応答が表示されます。 検出されたソース言語と翻訳に加えて、ソース (`srcSentLen`) と翻訳 (`transSentLen`) の両方について、検出された各文の文字数が返されます。

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "sentLen": {
                    "srcSentLen": [
                        44,
                        21,
                        12
                    ],
                    "transSentLen": [
                        48,
                        18,
                        10
                    ]
                },
                "text": "¿Puedes decirme cómo llegar a la estación Penn? ¿No estás seguro? Está bien.",
                "to": "es"
            }
        ]
    }
]
```

### <a name="get-sentence-length-without-translation"></a>翻訳せずに文の長さを取得する

また、Translator サービスでは、`breaksentence` エンドポイントを使用して、翻訳せずに文の長さを要求することもできます。 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";   
    
    static async Task Main(string[] args)
    {
        // Only include sentence length details.
        string route = "/breaksentence?api-version=3.0";
        string sentencesToCount = 
                "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine.";
        object[] body = new object[] { new { Text = sentencesToCount } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/breaksentence?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class BreakSentence {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/breaksentence")
        .addQueryParameter("api-version", "3.0")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            BreakSentence breakSentenceRequest = new BreakSentence();
            String response = breakSentenceRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/breaksentence',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0'
    },
    data: [{
        'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/breaksentence'
constructed_url = endpoint + path

params = {
    'api-version': '3.0'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, indent=4, separators=(',', ': ')))
```

---

呼び出しが成功すると、次の応答が表示されます。 `translate` エンドポイントの呼び出しとは異なり、`breaksentence` ではソース テキストの文字数のみが `sentLen` と呼ばれる配列で返されます。 

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "sentLen": [
            44,
            21,
            12
        ]
    }
]
```

## <a name="dictionary-lookup-alternate-translations"></a>辞書検索 (翻訳の代替候補)

エンドポイントを使用すると、単語または語句の翻訳の代替候補を取得できます。 たとえば、"shark" という単語を `en` から `es` に翻訳する場合、このエンドポイントからは "tiburón" と "escualo" の両方が返されます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION"; 
    
    static async Task Main(string[] args)
    {
        // See many translation options
        string route = "/dictionary/lookup?api-version=3.0&from=en&to=es";
        string wordToTranslate = "shark";
        object[] body = new object[] { new { Text = wordToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/dictionary/lookup?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "es")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "shark"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class DictionaryLookup {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/dictionary/lookup")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "es")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Shark\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            DictionaryLookup dictionaryLookupRequest = new DictionaryLookup();
            String response = dictionaryLookupRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/dictionary/lookup',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': 'es'
    },
    data: [{
        'text': 'shark'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/dictionary/lookup'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': 'es'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'shark'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

呼び出しが成功すると、次の応答が表示されます。 この記事の他の例よりも JSON が複雑になるため、詳しく見てみましょう。 `translations` 配列には、翻訳の一覧が含まれています。 この配列内の各オブジェクトには、信頼度スコア (`confidence`)、エンドユーザーの表示用に最適化されたテキスト (`displayTarget`)、正規化されたテキスト (`normalizedText`)、音声の一部 (`posTag`)、および以前の翻訳に関する情報 (`backTranslations`) が含まれています。 この応答の詳細については、[辞書検索](reference/v3-0-dictionary-lookup.md)に関するページを参照してください

```json
[
    {
        "displaySource": "shark",
        "normalizedSource": "shark",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "shark",
                        "frequencyCount": 45,
                        "normalizedText": "shark",
                        "numExamples": 0
                    }
                ],
                "confidence": 0.8182,
                "displayTarget": "tiburón",
                "normalizedTarget": "tiburón",
                "posTag": "OTHER",
                "prefixWord": ""
            },
            {
                "backTranslations": [
                    {
                        "displayText": "shark",
                        "frequencyCount": 10,
                        "normalizedText": "shark",
                        "numExamples": 1
                    }
                ],
                "confidence": 0.1818,
                "displayTarget": "escualo",
                "normalizedTarget": "escualo",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="dictionary-examples-translations-in-context"></a>辞書の例 (コンテキスト内の翻訳)

辞書検索を実行した後、ソース テキストと翻訳を `dictionary/examples` エンドポイントに渡すことによって、文または語句のコンテキスト内で両方の用語が現れる例の一覧を取得できます。 これまでの例を基に、辞書検索の応答の `normalizedText` と `normalizedTarget` を、それぞれ `text` と `translation` として使用します。 ソース言語 (`from`) と出力ターゲット (`to`) のパラメーターが必要です。 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION"; 
    
    static async Task Main(string[] args)
    {
        // See examples of terms in context
        string route = "/dictionary/examples?api-version=3.0&from=en&to=es";
        object[] body = new object[] { new { Text = "Shark",  Translation = "tiburón" } } ;
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/dictionary/examples?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "es")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text        string
        Translation string
    }{
        {
            Text:        "Shark",
            Translation: "tiburón",
        },
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator Text API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class DictionaryExamples {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/dictionary/examples")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "es")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Shark\", \"Translation\": \"tiburón\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            DictionaryExamples dictionaryExamplesRequest = new DictionaryExamples();
            String response = dictionaryExamplesRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/dictionary/examples',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': 'es'
    },
    data: [{
        'text': 'shark',
        'translation': 'tiburón'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/dictionary/examples'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': 'es'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'shark',
    'translation': 'tiburón'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

呼び出しが成功すると、次の応答が表示されます。 応答の詳細については、[辞書検索](reference/v3-0-dictionary-examples.md)に関するページを参照してください

```json
[
    {
        "examples": [
            {
                "sourcePrefix": "More than a match for any ",
                "sourceSuffix": ".",
                "sourceTerm": "shark",
                "targetPrefix": "Más que un fósforo para cualquier ",
                "targetSuffix": ".",
                "targetTerm": "tiburón"
            },
            {
                "sourcePrefix": "Same with the mega ",
                "sourceSuffix": ", of course.",
                "sourceTerm": "shark",
                "targetPrefix": "Y con el mega ",
                "targetSuffix": ", por supuesto.",
                "targetTerm": "tiburón"
            },
            {
                "sourcePrefix": "A ",
                "sourceSuffix": " ate it.",
                "sourceTerm": "shark",
                "targetPrefix": "Te la ha comido un ",
                "targetSuffix": ".",
                "targetTerm": "tiburón"
            }
        ],
        "normalizedSource": "shark",
        "normalizedTarget": "tiburón"
    }
]
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="java-users"></a>Java ユーザー

接続の問題が発生した場合、SSL 証明書の有効期限が切れている可能性があります。 この問題を解決するには、[DigiCertGlobalRootG2.crt](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt) をプライベート ストアにインストールします。 

## <a name="next-steps"></a>次の手順

* [API で文字数をカウントする方法を確認する](character-counts.md)
* [翻訳をカスタマイズおよび改善する](customization.md)

## <a name="see-also"></a>関連項目

* [Translator v3 API リファレンス](reference/v3-0-reference.md)
* [言語サポート](language-support.md)
