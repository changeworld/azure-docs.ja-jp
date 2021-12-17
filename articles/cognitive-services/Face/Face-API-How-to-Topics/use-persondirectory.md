---
title: '例: PersonDirectory 構造を使用する - Face'
titleSuffix: Azure Cognitive Services
description: PersonDirectory データ構造を使用して、大容量の顔と個人のデータおよびその他の新しい特徴を格納する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/22/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 70bbf20570c39fa59da9af7f7883aeef2e107df4
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633788"
---
# <a name="use-the-persondirectory-structure"></a>PersonDirectory 構造を使用する

類似の識別や検索といった顔認識操作を実行するために、Face API のお客様は、**Person** オブジェクトの類別された一覧を作成する必要があります。 新しい **PersonDirectory** は、ディレクトリに追加された **Person** の各 ID について、一意の ID、オプションの名前文字列、およびオプションのユーザー メタデータ文字列が格納されたデータ構造です。

現在、Face API には同様の機能を持つ **LargePersonGroup** 構造が用意されていますが、100 万の ID に制限されています。 **PersonDirectory** 構造は、最大 7,500 万の ID までスケールアップできます。

**PersonDirectory** と以前のデータ構造のもう 1 つの大きな違いは、顔を **Person** オブジェクトに追加した後にトレーニングの呼び出しを行う必要がなくなったことです。更新プロセスは自動的に行われます。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)。
* Azure サブスクリプションを入手したら、Azure portal で [Face リソースを作成](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace)し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
  * 対象のアプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。 下のコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (F0) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="add-persons-to-the-persondirectory"></a>Persons を PersonDirectory に追加する
**Persons** は、**PersonDirectory** の基本登録単位です。 ディレクトリに **Person** を追加すると、認識モデルあたり最大 248 の顔画像をその **Person** に追加できます。 その後、さまざまなスコープを使用して、それらに対して顔を識別できます。

### <a name="create-the-person"></a>Person を作成する
**Person** を作成するには、**CreatePerson** API を呼び出して、name または userData プロパティ値を指定する必要があります。

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var addPersonUri = "https:// {endpoint}/face/v1.0-preview/persons";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Person");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(addPersonUri, content); 
}
```

CreatePerson を呼び出すと、**Person** に対して生成された ID と操作の場所が返されます。 **Person** データは非同期的に処理されるため、操作の場所を使用して結果を取得します。 

### <a name="wait-for-asynchronous-operation-completion"></a>非同期操作の完了を待機する
進行状況を確認するには、返された操作の場所の文字列を使用して非同期操作の状態を照会する必要があります。 

最初に、状態応答を処理するため、次のようなデータ モデルを定義する必要があります。

```csharp
[Serializable]
public class AsyncStatus
{
    [DataMember(Name = "status")]
    public string Status { get; set; }

    [DataMember(Name = "createdTime")]
    public DateTime CreatedTime { get; set; }

    [DataMember(Name = "lastActionTime")]
    public DateTime? LastActionTime { get; set; }

    [DataMember(Name = "finishedTime", EmitDefaultValue = false)]
    public DateTime? FinishedTime { get; set; }

    [DataMember(Name = "resourceLocation", EmitDefaultValue = false)]
    public string ResourceLocation { get; set; }

    [DataMember(Name = "message", EmitDefaultValue = false)]
    public string Message { get; set; }
}
```

上の HttpResponseMessage を使用して、URL をポーリングし、結果を待機することができます。

```csharp
string operationLocation = response.Headers.GetValues("Operation-Location").FirstOrDefault();

Stopwatch s = Stopwatch.StartNew();
string status = "notstarted";
do
{
    if (status == "succeeded")
    {
        await Task.Delay(500);
    }

    var operationResponseMessage = await client.GetAsync(operationLocation);

    var asyncOperationObj = JsonConvert.DeserializeObject<AsyncStatus>(await operationResponseMessage.Content.ReadAsStringAsync());
    status = asyncOperationObj.Status;

} while ((status == "running" || status == "notstarted") && s.Elapsed < TimeSpan.FromSeconds(30));
```


"succeeded" という状態が返されると、**Person** オブジェクトがディレクトリに追加されたと見なされます。

> [!NOTE]
> **Person** 作成呼び出しの非同期操作は、顔を追加するときは "succeeded" 状態になっていなくてもかまいませんが、**Person** を **DynamicPersonGroup** に追加したり (後の「**DynamicPersonGroup** の作成と更新」を参照)、Identify 呼び出しの間に比較したりするには、完了している必要があります。 顔が **Person** に正常に追加された直後に呼び出しが機能することを確認します。


### <a name="add-faces-to-persons"></a>顔を Persons に追加する

Person 作成呼び出しから **Person** ID を取得したら、認識モデルごとに最大 248 の顔画像を **Person** に追加できます。 各認識モデルのデータは **PersonDirectory** 内で個別に処理されるため、使用する認識モデル (および必要に応じて検出モデル) を呼び出しで指定します。

現在サポートされている認識モデルは次のとおりです。
* `Recognition_02`
* `Recognition_03`
* `Recognition_04`

また、画像に複数の顔が含まれている場合は、対象の顔を四角形の境界ボックスで指定する必要があります。 次のコードを使用すると、**Person** オブジェクトに顔が追加されます。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var queryString = "userData={userDefinedData}&targetFace={left,top,width,height}&detectionModel={detectionModel}";
var uri = "https://{endpoint}/face/v1.0-preview/persons/{personId}/recognitionModels/{recognitionModel}/persistedFaces?" + queryString;

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("url", "{image url}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

顔追加呼び出しの後、顔データは非同期的に処理されるため、前と同じ方法で操作が成功するまで待機する必要があります。

顔追加操作が完了すると、データは Identify の呼び出しで使用できるようになります。

## <a name="create-and-update-a-dynamicpersongroup"></a>**DynamicPersonGroup** の作成と更新

**DynamicPersonGroups** は、**PersonDirectory** 内の **Person** オブジェクトへの参照のコレクションです。これらは、ディレクトリのサブセットを作成するために使用されます。 一般的に使用されるのは、一致すると予想される **Person** オブジェクトだけにスコープを制限することにより、擬陽性を減らし、Identify 操作の精度を上げる場合です。 実際のユース ケースには、大規模なキャンパスや組織での特定の建物へのアクセスのためのディレクトリが含まれます。 組織ディレクトリには 500 万人の個人が含まれていても、特定のビルについては特定の 800 人だけを検索する必要がある場合、それらの特定の個人を含む **DynamicPersonGroup** を作成します。 

前に **PersonGroup** を使用したことがある場合は、2 つの大きな違いに注意してください。 
* **DynamicPersonGroup** 内の各 **Person** は、**PersonDirectory** 内の実際の **Person** への参照です。つまり、グループごとに **Person** を再作成する必要はありません。
* 前のセクションで説明したように、顔データはディレクトリ レベルで自動的に処理されるため、トレーニング呼び出しを行う必要はありません。

### <a name="create-the-group"></a>グループを作成する

**DynamicPersonGroup** を作成するには、英数字またはダッシュ文字でグループ ID を指定する必要があります。 この ID は、グループのすべての使用目的に対する一意の識別子として機能します。

グループ コレクションを初期化するには、2 つの方法があります。 最初に空のグループを作成し、後で設定することができます。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);
}
```

このプロセスはすぐに行われるので、非同期操作が成功するまで待つ必要はありません。

または、**Person** ID のセットを _AddPersonIds_ 引数で提供することにより、最初からそれらの参照を含めることができます。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

> [!NOTE]
> 呼び出しから戻るとすぐに、任意の **Person** 参照をプロセスで指定して、作成された **DynamicPersonGroup** を Identify 呼び出しで使用できるようになります。 一方、返された操作 ID の完了状態は、人とグループの関係の更新状態を示します。

### <a name="update-the-dynamicpersongroup"></a>DynamicPersonGroup を更新する

初期作成の後、Update Dynamic Person Group API を使用して、**DynamicPersonGroup** の **Person** 参照を追加および削除できます。 グループに **Person** オブジェクトを追加するには、_addPersonsIds_ 引数で **Person** ID のリストを指定します。 **Person** オブジェクトを削除するには、_removePersonIds_ 引数でそのリストを指定します。 1 回の呼び出しで、追加と削除の両方を実行することができます。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Dynamic Person Group updated");
body.Add("userData", "User defined data updated");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("removePersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PatchAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

呼び出しから戻った後、グループを照会すると、コレクションに対する更新が反映されています。 作成 API と同様に、返された操作では、更新に関係するすべての **Person** に関する人とグループの関係の更新状態が示されています。 グループに対する更新呼び出しをさらに行う前に、操作の完了を待つ必要はありません。

## <a name="identify-faces-in-a-persondirectory"></a>PersonDirectory 内の顔を識別する

**PersonDirectory** 内の顔データを使用する最も一般的な方法は、登録されている **Person** オブジェクトを特定の顔と比較し、それが属している可能性が最も高い候補を特定することです。 要求では複数の顔を指定することができ、それぞれについて応答で独自の比較結果セットを受け取ります。

**PersonDirectory** には、それぞれの顔を識別できる 3 種類のスコープがあります。

### <a name="scenario-1-identify-against-a-dynamicpersongroup"></a>シナリオ 1: DynamicPersonGroup に対して識別する
 
要求で _dynamicPersonGroupId_ プロパティを指定すると、グループ内で参照されているすべての **Person** に対して顔が比較されます。 1 回の呼び出しでは、1 つの **DynamicPersonGroup** についてのみ識別できます。 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("dynamicPersonGroupId", "{dynamicPersonGroupIdToIdentifyIn}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-2-identify-against-a-specific-list-of-persons"></a>シナリオ 2: 人の特定のリストに対して識別する

_personIds_ プロパティで **Person** の ID の一覧を指定して、それぞれと顔を比較することもできます。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-3-identify-against-the-entire-persondirectory"></a>シナリオ 3: **PersonDirectory** 全体を識別する

要求の _personIds_ プロパティで 1 つのアスタリスクを指定すると、**PersonDirectory** に登録されているすべての **Person** と顔が比較されます。 
 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"*"});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```
3 つのシナリオすべてについて、識別では、入力された顔と、AddPersonFace の呼び出しで "succeeded" 応答が返された顔とが比較されます。

## <a name="verify-faces-against-persons-in-the-persondirectory"></a>**PersonDirectory** 内の人に対して顔を確認する

検出呼び出しから返された顔 ID を使用して、その顔が **PersonDirectory** 内に登録されている特定の **Person** に属しているかどうかを確認できます。 _personId_ プロパティを使用して **Person** を指定します。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/verify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceId", "{guid1}");
body.Add("personId", "{guid1}");
var jsSerializer = new JavaScriptSerializer();
byte[] byteData = Encoding.UTF8.GetBytes(jsSerializer.Serialize(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

応答には、サービスで新しい顔が同じ **Person** に属していると見なせるかどうかを示すブール値と、予測の信頼スコアが含まれます。

## <a name="next-steps"></a>次のステップ

このガイドでは、**PersonDirectory** 構造を使用して、Face アプリの顔データと個人データを格納する方法について説明しました。 次に、ユーザーの顔データを追加するためのベスト プラクティスについて説明します。

* [ユーザーを追加するためのベスト プラクティス](../enrollment-overview.md)
