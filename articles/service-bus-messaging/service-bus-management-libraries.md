---
title: "Azure Service Bus 管理ライブラリ | Microsoft Docs"
description: ".NET から Service Bus の名前空間とメッセージング エンティティを管理する"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: ec9f2fa3d88f59172d320b58287208deb084856f
ms.contentlocale: ja-jp
ms.lasthandoff: 04/11/2017


---

# <a name="service-bus-management-libraries"></a>Service Bus 管理ライブラリ

Service Bus 管理ライブラリは、Service Bus の名前空間とエンティティを動的にプロビジョニングできます。 これにより、複雑なデプロイとメッセージング シナリオを可能にし、プロビジョニングするエンティティをプログラムを使用して決定できます。 現在、これらのライブラリは .NET で使用できます。

## <a name="supported-functionality"></a>サポートされる機能

* 名前空間の作成、更新、削除
* キューの作成、更新、削除
* トピックの作成、更新、削除
* サブスクリプションの作成、更新、削除

## <a name="prerequisites"></a>前提条件

Service Bus 管理ライブラリの使用を開始するには、Azure Active Directory (AAD) で認証する必要があります。 AAD では、Azure リソースへのアクセスを提供するサービス プリンシパルとして認証する必要があります。 サービス プリンシパルを作成する方法について詳しくは、次の記事のいずれかをご覧ください。  

* [リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルを Azure Portal で作成する](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

これらのチュートリアルでは、`AppId` (クライアント ID)、`TenantId`、`ClientSecret` (認証キー) が提供されます。これらはすべて管理ライブラリによって認証に使用されます。 実行するリソース グループに対する "所有者" のアクセス許可が必要です。

## <a name="programming-pattern"></a>プログラミング パターン

すべての Service Bus リソースを操作するパターンは、次の共通のプロトコルに従います。

1. **Microsoft.IdentityModel.Clients.ActiveDirectory** ライブラリを使用して、Azure Active Directory からトークンを取得します。
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. `ServiceBusManagementClient` オブジェクトを作成します。
    ```csharp
    var creds = new TokenCredentials(token);
    var sbClient = new ServiceBusManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. `CreateOrUpdate` パラメーターを指定した値に設定します。
    ```csharp
    var queueParams = new QueueCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"],
        EnablePartitioning = true
    };
    ```

1. 呼び出しを実行します。
    ```csharp
    await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
    ```

## <a name="next-steps"></a>次のステップ
* [.NET 管理のサンプル](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.Management.ServiceBus リファレンス](/dotnet/api/Microsoft.Azure.Management.ServiceBus)

