---
title: Azure Cognitive Service for Language の個人を特定できる情報 (検出) によって認識されるエンティティのカテゴリ
titleSuffix: Azure Cognitive Services
description: PII 機能が非構造化テキストから認識できるエンティティについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/15/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 099926359944c1f9e9a673f3c754019c761e6220
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519536"
---
# <a name="supported-personally-identifiable-information-pii-entity-categories"></a>サポートされている個人を特定できる情報 (PII) エンティティのカテゴリ

この記事では、[PII 検出機能](../how-to-call.md) から取得できるエンティティのカテゴリを紹介しています。 この機能では予測モデルを実行して、入力ドキュメントから機密情報を識別、分類、および編集します。

PII 検出機能には、個人情報 (`PII`) と医療情報 (`PHI`) を検出する機能が備わっています。

## <a name="entity-categories"></a>エンティティのカテゴリ

> [!NOTE]
> 保護されている医療情報 (PHI) を検出するには、`domain=phi` パラメーターとモデル バージョン `2020-04-01` 以降を使用します。
>
> 例: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
API 要求の PII 機能を送信すると、次のエンティティ カテゴリが返されます。

## <a name="category-person"></a>カテゴリ:Person

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Person

    :::column-end:::
    :::column span="2":::
        **詳細**

        人の名前。 PII と PHI の両方として返されます。

        このエンティティ カテゴリを取得するには、`Person` を `pii-categories` パラメーターに追加します。 検出されると、`Person` が API 応答で返されます。
      
    :::column-end:::
    
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

## <a name="category-persontype"></a>カテゴリ:PersonType

このカテゴリには、次のエンティティが含まれます。


:::row:::
    :::column span="":::
        **エンティティ**

        PersonType

    :::column-end:::
    :::column span="2":::
        **詳細**

        人物の職種または職務。

        このエンティティ カテゴリを取得するには、`PersonType` を `pii-categories` パラメーターに追加します。 検出されると、`PersonType` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

## <a name="category-phonenumber"></a>カテゴリ:PhoneNumber

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **詳細**

        電話番号 (米国および EU の電話番号のみ)。 PII と PHI の両方として返されます。

        このエンティティ カテゴリを取得するには、`PhoneNumber` を `pii-categories` パラメーターに追加します。 検出されると、`PhoneNumber` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


## <a name="category-organization"></a>カテゴリ:Organization

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Organization

    :::column-end:::
    :::column span="2":::
        **詳細**

        企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関。 国籍と宗教は、このエンティティ型には含まれていません。 PII と PHI の両方として返されます。

        このエンティティ カテゴリを取得するには、`Organization` を `pii-categories` パラメーターに追加します。 検出されると、`Organization` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        医療    

    :::column-end:::
    :::column span="2":::
        **詳細**

        医療関係の企業および団体。

        このエンティティ カテゴリを取得するには、`OrganizationMedical` を `pii-categories` パラメーターに追加します。 検出されると、`OrganizationMedical` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        証券取引

    :::column-end:::
    :::column span="2":::

        証券取引団体。 

        このエンティティ カテゴリを取得するには、`OrganizationStockExchange` を `pii-categories` パラメーターに追加します。 検出されると、`OrganizationStockExchange` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        スポーツ

    :::column-end:::
    :::column span="2":::

        スポーツ関連の組織。

        このエンティティ カテゴリを取得するには、`OrganizationSports` を `pii-categories` パラメーターに追加します。 検出されると、`OrganizationSports` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


## <a name="category-address"></a>カテゴリ:Address

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Address

    :::column-end:::
    :::column span="2":::
        **詳細**

        完全な郵送先住所。 PII と PHI の両方として返されます。

        このエンティティ カテゴリを取得するには、`Address` を `pii-categories` パラメーターに追加します。 検出されると、`Address` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

## <a name="category-email"></a>カテゴリ:Email

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Email

    :::column-end:::
    :::column span="2":::
        **詳細**

        メール アドレス。 PII と PHI の両方として返されます。
      
        このエンティティ カテゴリを取得するには、`Email` を `pii-categories` パラメーターに追加します。 検出されると、`Email` が API 応答で返されます。

    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


## <a name="category-url"></a>カテゴリ:URL

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        URL

    :::column-end:::
    :::column span="2":::
        **詳細**

        Web サイトへの URL。 PII と PHI の両方として返されます。

        このエンティティ カテゴリを取得するには、`URL` を `pii-categories` パラメーターに追加します。 検出されると、`URL` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

## <a name="category-ip"></a>カテゴリ:IP

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        IP

    :::column-end:::
    :::column span="2":::
        **詳細**

        ネットワーク IP アドレス。 PII と PHI の両方として返されます。

        このエンティティ カテゴリを取得するには、`IP` を `pii-categories` パラメーターに追加します。 検出されると、`IP` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

## <a name="category-datetime"></a>カテゴリ:DateTime

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        DateTime

    :::column-end:::
    :::column span="2":::
        **詳細**

        日付と時刻。 

        このエンティティ カテゴリを取得するには、`DateTime` を `pii-categories` パラメーターに追加します。 検出されると、`DateTime` が API 応答で返されます。
      
    :::column-end:::
:::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        Date

    :::column-end:::
    :::column span="2":::
        **詳細**

        カレンダーの日付。 PII と PHI の両方として返されます。

        このエンティティ カテゴリを取得するには、`Date` を `pii-categories` パラメーターに追加します。 検出されると、`Date` が API 応答で返されます。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

## <a name="category-quantity"></a>カテゴリ:Quantity

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Quantity

    :::column-end:::
    :::column span="2":::
        **詳細**

        数値と数量です。

        このエンティティ カテゴリを取得するには、`Quantity` を `pii-categories` パラメーターに追加します。 検出されると、`Quantity` が API 応答で返されます。
      
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

        このエンティティ カテゴリを取得するには、`Age` を `pii-categories` パラメーターに追加します。 検出されると、`Age` が API 応答で返されます。
      
    :::column-end:::
    :::column span="2":::
        **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure の情報

これらのエンティティ カテゴリには、認証情報や接続文字列などの特定可能な Azure 情報が含まれます。 PHI としては返されません。

:::row:::
    :::column span="":::
        **エンティティ**

        Azure DocumentDB 認証キー 

    :::column-end:::
    :::column span="2":::
        **詳細**

        Azure Cosmos DB サーバーの認証キー。   

        このエンティティ カテゴリを取得するには、`AzureDocumentDBAuthKey` を `pii-categories` パラメーターに追加します。 検出されると、`AzureDocumentDBAuthKey` が API 応答で返されます。
      
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

        このエンティティ カテゴリを取得するには、`AzureIAASDatabaseConnectionAndSQLString` を `pii-categories` パラメーターに追加します。 検出されると、`AzureIAASDatabaseConnectionAndSQLString` が API 応答で返されます。
      
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
      
        このエンティティ カテゴリを取得するには、`AzureIoTConnectionString` を `pii-categories` パラメーターに追加します。 検出されると、`AzureIoTConnectionString` が API 応答で返されます。

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

        このエンティティ カテゴリを取得するには、`AzurePublishSettingPassword` を `pii-categories` パラメーターに追加します。 検出されると、`AzurePublishSettingPassword` が API 応答で返されます。
      
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

        このエンティティ カテゴリを取得するには、`AzureRedisCacheString` を `pii-categories` パラメーターに追加します。 検出されると、`AzureRedisCacheString` が API 応答で返されます。
      
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

        このエンティティ カテゴリを取得するには、`AzureSAS` を `pii-categories` パラメーターに追加します。 検出されると、`AzureSAS` が API 応答で返されます。
      
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

        このエンティティ カテゴリを取得するには、`AzureServiceBusString` を `pii-categories` パラメーターに追加します。 検出されると、`AzureServiceBusString` が API 応答で返されます。
      
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

        このエンティティ カテゴリを取得するには、`AzureStorageAccountKey` を `pii-categories` パラメーターに追加します。 検出されると、`AzureStorageAccountKey` が API 応答で返されます。
      
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

        このエンティティ カテゴリを取得するには、`AzureStorageAccountGeneric` を `pii-categories` パラメーターに追加します。 検出されると、`AzureStorageAccountGeneric` が API 応答で返されます。
      
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

        このエンティティ カテゴリを取得するには、`SQLServerConnectionString` を `pii-categories` パラメーターに追加します。 検出されると、`SQLServerConnectionString` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>識別

[!INCLUDE [supported identification entities](../includes/identification-entities.md)]


## <a name="next-steps"></a>次のステップ

* [NER の概要](../overview.md)
