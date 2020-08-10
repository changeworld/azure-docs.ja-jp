---
title: オファーを作成または変更する - Azure Marketplace
description: オファーの新規作成または既存オファーの更新を行うための API です。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 68416fa91f6b75fb3ef2b91c1297f13d84f2bdab
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292961"
---
<a name="create-or-modify-an-offer"></a>オファーを作成または変更する
=========================

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](./cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

この呼び出しでは、パブリッシャーの名前空間内にある特定のオファーが更新されるか、または新しいオファーが作成されます。

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`


<a name="uri-parameters"></a>URI パラメーター
--------------

|  **名前**         |  **説明**                      |  **データの種類**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  パブリッシャー ID。たとえば、`contoso` |   String |
| offerId           |  オファー ID                     |   String        |
| api-version       |  API の最新バージョン            |   Date           |
|  |  |  |

<a name="header"></a>ヘッダー
------

|  **Name**        |  **Value**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| 承認    | `Bearer YOUR_TOKEN`      |
|  |  |


<a name="body-example"></a>本文の例
------------

次の例では、offerID が `contosovirtualmachine` のオファーを作成します。

### <a name="request"></a>Request

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```


### <a name="response"></a>Response

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> このオファーを変更するには、* に設定した **If-Match** ヘッダーを上の要求に追加します。 上記と同じ要求本文を使いますが、必要に応じて値を変更します。 


### <a name="response-status-codes"></a>応答状態コード

| **コード**  |  **説明**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. 要求が正常に処理されて、オファーが正常に変更されました。           |
|  201      | `Created`. 要求が正常に処理されて、オファーが正常に作成されました。   |
|  400      | `Bad/Malformed request`. エラーの応答本文にさらに情報が含まれている場合があります。            |
|  403      | `Forbidden`. クライアントは、要求された名前空間にアクセスできません。                     |
|  404      | `Not found`. クライアントによって参照されているエンティティは存在しません。                           |
|  412      | サーバーが、要求者が要求で指定した前提条件の 1 つを満たしていません。 クライアントは、要求で送信された ETAG をチェックする必要があります。 |
|  |  |


<a name="uploading-artifacts"></a>成果物のアップロード
-------------------

イメージやロゴなどの成果物は、Web 上のアクセス可能な場所にアップロードした後、上記の例のように、PUT 要求の URI として組み込むことにより、共有する必要があります。 これらのファイルが Azure Marketplace のストレージに存在しないことが検出されると、ストレージにファイルがダウンロードされます。  その結果、将来の GET 要求ではこれらのファイルに対して Azure Marketplace サービスの URL が返されます。
