 ---
タイトル: Azure Media Services REST 呼び出しのための Postman 環境のインポート 説明: このトピックでは、Azure Media Services REST 呼び出しの Postman 環境の定義について説明します。
services: media-services documentationcenter: '' author: Juliako manager: cfowler editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: article ms.date: 01/04/2017 ms.author: juliako

---

# <a name="import-the-postman-environment"></a>Postman 環境のインポート 

この記事には、Media Services REST API を呼び出す、グループ化された HTTP 要求を含む [Postman コレクション](postman-collection.md)で使用される **Postman** 環境変数の定義が示されています。 環境およびコレクション ファイルは「[Configure Postman for Media Services REST API calls (Media Services REST API のための Postman の構成)](media-rest-apis-with-postman.md)」チュートリアルで使用されます。

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
