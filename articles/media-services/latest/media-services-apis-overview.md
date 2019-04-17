---
title: v3 API を使用した開発 - Azure | Microsoft Docs
description: この記事では、Media Services v3 を使用して開発を行う際にエンティティと API に適用される規則について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471790"
---
# <a name="developing-with-media-services-v3-apis"></a>Media Services v3 API を使用した開発

この記事では、Media Services v3 を使用して開発を行う際にエンティティと API に適用される規則について説明します。

## <a name="naming-conventions"></a>名前付け規則

Azure Media Services v3 のリソース名 (アセット、ジョブ、変換など) には、Azure Resource Manager の名前付け規則が適用されます。 Azure Resource Manager に従って、リソース名は常に一意となります。 そのため、リソース名には一意識別子の文字列 (GUID など) を使用できます。 

Media Services リソース名には、"<",">"、"%"、"&"、":"、"&#92;"、"?"、"/"、"*"、"+"、"."、一重引用符などの制御文字を使用することができません。 それ以外の文字は使用できます。 リソース名の最大文字数は 260 文字です。 

Azure Resource Manager の名前付けの詳細については、[名前付けの要件](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)と[名前付け規則](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。

## <a name="v3-api-design-principles-and-rbac"></a>v3 API の設計原則と RBAC

v3 API の主要な設計原則の 1 つは、API の安全性の向上です。 v3 API は、**Get** または **List** 操作でシークレットまたは資格情報を返しません。 キーは常に、null または空であるか、応答から削除されます。 ユーザーがシークレットまたは資格情報を取得するには、別のアクション メソッドを呼び出す必要があります。 **Reader** ロールでは、操作を呼び出せないので、Asset.ListContainerSas、StreamingLocator.ListContentKeys、ContentKeyPolicies.GetPolicyPropertiesWithSecrets などの操作を呼び出すことはできません。 別のアクションがあることにより、必要に応じてカスタム ロールに、より細かい RBAC セキュリティ許可を設定できます。

詳細については、次を参照してください。

- [組み込みのロールの定義](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [RBAC を使用したアクセス管理](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Media Services アカウント用のロールベースのアクセス制御](rbac-overview.md)
- [コンテンツ キー ポリシーの取得 - .NET](get-content-key-policy-dotnet-howto.md)。

## <a name="long-running-operations"></a>長時間にわたって実行される操作

Azure Media Services の [Swagger ファイル](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)に `x-ms-long-running-operation` とマークされた操作は長期操作です。 

非同期の Azure 操作を追跡する方法について詳しくは、[非同期操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)に関するセクションを参照してください。

Media Services には、次のような長期操作があります。

* LiveEvent の作成
* LiveEvent の更新
* LiveEvent の削除
* LiveEvent の開始
* LiveEvent の停止
* LiveEvent のリセット
* LiveOutput の作成
* LiveOutput の削除
* StreamingEndpoint の作成
* StreamingEndpoint の更新
* StreamingEndpoint の削除
* StreamingEndpoint の開始
* StreamingEndpoint の停止
* StreamingEndpoint のスケーリング

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services エンティティのフィルター処理、順序付け、ページング

[Azure Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[SDK/ツールを使用して Media Services v3 API での開発を始める](developers-guide.md)
