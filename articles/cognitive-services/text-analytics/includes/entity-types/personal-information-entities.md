---
title: 個人情報の名前付きエンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/09/2021
ms.author: aahi
ms.openlocfilehash: ce85895307af9e437fb9ceaaff3f1a385e77e17d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672591"
---
> [!NOTE]
> 保護されている医療情報 (PHI) を検出するには、`domain=phi` パラメーターとモデル バージョン `2020-04-01` 以降を使用します。
>
> 例: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
`/v3.1/entities/recognition/pii` エンドポイントに要求を送信すると、次のエンティティ カテゴリが返されます。


| カテゴリ   |  説明                          |
|------------|-------------|
| [Person](#category-person)      |  人の名前。  |
| [PersonType](#category-persontype) | 人物の職種または職務。 |
| [電話番号](#category-phonenumber) |電話番号 (米国および EU の電話番号のみ)。 |
| [組織](#category-organization) |  企業、グループ、政府機関、その他の組織。  |
| [アドレス](#category-address) | 完全な郵送先住所。  |
| [Email](#category-email) | メール アドレス。   |
| [URL](#category-url) | Web サイトへの URL。  |
| [IPAddress](#category-ipaddress) | ネットワーク IP アドレス。  |
| [DateTime](#category-datetime) | 日付と時刻。 | 
| [Quantity](#category-quantity) | 数値と数量です。  |
| [Azure の情報](#azure-information) | 識別可能な Azure 情報 (認証情報など)。  |
| [[識別]](#identification) | 財務および国固有の ID。  |

### <a name="category-person"></a>カテゴリ:Person

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Person

    :::column-end:::
    :::column span="2":::
        **詳細**

        人の名前。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`Person` を `piiCategories` パラメーターに追加します。 検出されると、`Person` が API 応答で返されます。
      
    :::column-end:::
    
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>カテゴリ:PersonType

このカテゴリには、次のエンティティが含まれます。


:::row:::
    :::column span="":::
        **エンティティ**

        PersonType

    :::column-end:::
    :::column span="2":::
        **詳細**

        人物の職種または職務。

        このエンティティ カテゴリを取得するには、`PersonType` を `piiCategories` パラメーターに追加します。 検出されると、`PersonType` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>カテゴリ:PhoneNumber

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **詳細**

        電話番号 (米国および EU の電話番号のみ)。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`PhoneNumber` を `piiCategories` パラメーターに追加します。 検出されると、`PhoneNumber` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


### <a name="category-organization"></a>カテゴリ:Organization

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Organization

    :::column-end:::
    :::column span="2":::
        **詳細**

        企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関。 国籍と宗教は、このエンティティ型には含まれていません。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`Organization` を `piiCategories` パラメーターに追加します。 検出されると、`Organization` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

### <a name="category-address"></a>カテゴリ:Address

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Address

    :::column-end:::
    :::column span="2":::
        **詳細**

        完全な郵送先住所。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`Address` を `piiCategories` パラメーターに追加します。 検出されると、`Address` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-email"></a>カテゴリ:Email

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Email

    :::column-end:::
    :::column span="2":::
        **詳細**

        メール アドレス。 `domain=phi` でも返されます。
      
        このエンティティ カテゴリを取得するには、`Email` を `piiCategories` パラメーターに追加します。 検出されると、`Email` が API 応答で返されます。

    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


### <a name="category-url"></a>カテゴリ:URL

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        URL

    :::column-end:::
    :::column span="2":::
        **詳細**

        Web サイトへの URL。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`URL` を `piiCategories` パラメーターに追加します。 検出されると、`URL` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ipaddress"></a>カテゴリ: IPAddress

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        IPAddress

    :::column-end:::
    :::column span="2":::
        **詳細**

        ネットワーク IP アドレス。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`IPAddress` を `piiCategories` パラメーターに追加します。 検出されると、`IPAddress` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>カテゴリ:DateTime

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        DateTime

    :::column-end:::
    :::column span="2":::
        **詳細**

        日付と時刻。 

        このエンティティ カテゴリを取得するには、`DateTime` を `piiCategories` パラメーターに追加します。 検出されると、`DateTime` が API 応答で返されます。
      
    :::column-end:::
:::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>カテゴリ:Quantity

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Quantity

    :::column-end:::
    :::column span="2":::
        **詳細**

        数値と数量です。

        このエンティティ カテゴリを取得するには、`Quantity` を `piiCategories` パラメーターに追加します。 検出されると、`Quantity` が API 応答で返されます。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        Age

    :::column-end:::
    :::column span="2":::
        **詳細**

        年齢。 

        このエンティティ カテゴリを取得するには、`Age` を `piiCategories` パラメーターに追加します。 検出されると、`Age` が API 応答で返されます。
      
    :::column-end:::
    :::column span="2":::
        **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure の情報

これらのエンティティ カテゴリには、認証情報や接続文字列などの特定可能な Azure 情報が含まれます。 `domain=phi` パラメーターで返されません。

:::row:::
    :::column span="":::
        **エンティティ**

        Azure DocumentDB 認証キー 

    :::column-end:::
    :::column span="2":::
        **詳細**

        Azure Cosmos DB サーバーの認証キー。   

        このエンティティ カテゴリを取得するには、`AzureDocumentDBAuthKey` を `piiCategories` パラメーターに追加します。 検出されると、`AzureDocumentDBAuthKey` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IAAS データベース接続文字列および Azure SQL 接続文字列。
        

    :::column-end:::
    :::column span="2":::

        Azure IaaS (サービスとしてのインフラストラクチャ) データベースの接続文字列と SQL 接続文字列。

        このエンティティ カテゴリを取得するには、`AzureIAASDatabaseConnectionAndSQLString` を `piiCategories` パラメーターに追加します。 検出されると、`AzureIAASDatabaseConnectionAndSQLString` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT 接続文字列  

    :::column-end:::
    :::column span="2":::

        Azure IoT の接続文字列。 
      
        このエンティティ カテゴリを取得するには、`AzureIoTConnectionString` を `piiCategories` パラメーターに追加します。 検出されると、`AzureIoTConnectionString` が API 応答で返されます。

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure 発行設定パスワード  

    :::column-end:::
    :::column span="2":::

        Azure 発行設定のパスワード。

        このエンティティ カテゴリを取得するには、`AzurePublishSettingPassword` を `piiCategories` パラメーターに追加します。 検出されると、`AzurePublishSettingPassword` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache 接続文字列 

    :::column-end:::
    :::column span="2":::

        Redis Cache の接続文字列。

        このエンティティ カテゴリを取得するには、`AzureRedisCacheString` を `piiCategories` パラメーターに追加します。 検出されると、`AzureRedisCacheString` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Azure SaaS (サービスとしてのソフトウェア) の接続文字列。

        このエンティティ カテゴリを取得するには、`AzureSAS` を `piiCategories` パラメーターに追加します。 検出されると、`AzureSAS` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus 接続文字列

    :::column-end:::
    :::column span="2":::

        Azure Service Bus の接続文字列。

        このエンティティ カテゴリを取得するには、`AzureServiceBusString` を `piiCategories` パラメーターに追加します。 検出されると、`AzureServiceBusString` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage アカウント キー 

    :::column-end:::
    :::column span="2":::

        Azure Storage アカウントのアカウント キー。 

        このエンティティ カテゴリを取得するには、`AzureStorageAccountKey` を `piiCategories` パラメーターに追加します。 検出されると、`AzureStorageAccountKey` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage アカウント キー (汎用)

    :::column-end:::
    :::column span="2":::

        Azure Storage アカウントの汎用アカウント キー。

        このエンティティ カテゴリを取得するには、`AzureStorageAccountGeneric` を `piiCategories` パラメーターに追加します。 検出されると、`AzureStorageAccountGeneric` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server 接続文字列 

    :::column-end:::
    :::column span="2":::

        SQL Server を実行するコンピューターの接続文字列。

        このエンティティ カテゴリを取得するには、`SQLServerConnectionString` を `piiCategories` パラメーターに追加します。 検出されると、`SQLServerConnectionString` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>識別

[!INCLUDE [supported identification entities](./identification-entities.md)]
