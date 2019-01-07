---
title: Azure リソース プロバイダーの登録エラー | Microsoft Docs
description: Azure リソース プロバイダーの登録エラーを解決する方法について説明します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 704aa488d40a18d7be0b64c9fc9a1bd33f8a3d96
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184544"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>リソース プロバイダーの登録エラーの解決

この記事では、これまでサブスクリプションで使用したことがないリソース プロバイダーを使用する際に発生する可能性のあるエラーについて説明します。

## <a name="symptom"></a>症状

リソースをデプロイするときに、次のエラー コードとメッセージが表示される場合があります。

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

または、次のようなメッセージが表示される場合があります。

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

サポートされる場所や API バージョンがエラー メッセージに提示されます。 提示されたいずれかの値を使用するようにテンプレートを変更してください。 ほとんどのプロバイダーは、Azure portal またはご使用のコマンド ライン インターフェイスによって自動的に登録されますが、登録されない場合もあります。 まだ使ったことがないリソース プロバイダーについては、手動で登録しなければならない場合があります。

## <a name="cause"></a>原因

これらのエラーの原因として、次の 3 つの理由のいずれかが考えられます。

* サブスクリプションに対してリソース プロバイダーが登録されていない
* リソース タイプでサポートされた API バージョンに該当しない
* リソース タイプでサポートされた場所に該当しない

## <a name="solution-1---powershell"></a>解決策 1 - PowerShell

PowerShell では、**Get-AzureRmResourceProvider** を使用して登録の状態を確認します。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

プロバイダーを登録するには、 **Register-AzureRmResourceProvider** を使用し、登録するリソース プロバイダーの名前を指定します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

特定のタイプのリソースでサポートされている場所を取得するには、次のコマンドを使用します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

特定のタイプのリソースでサポートされている API バージョンを取得するには、次のコマンドを使用します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>解決策 2 - Azure CLI

プロバイダーが登録されているかどうかを確認するには、 `az provider list` コマンドを使用します。

```azurecli-interactive
az provider list
```

リソース プロバイダーを登録するには、 `az provider register` コマンドを使用し、登録する *名前空間* を指定します。

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

特定のリソースの種類に対してサポートされている場所と API バージョンを確認するには、次のコマンドを使用します。

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>解決策 3 - Azure Portal

ポータルを利用すると、登録の状態を確認したり、リソース プロバイダー名前空間を登録したりできます。

1. Portal で **[すべてのサービス]** を選択します。

   ![[すべてのサービス] を選択する](./media/resource-manager-register-provider-errors/select-all-services.png)

1. **[サブスクリプション]** を選択します。

   ![[サブスクリプション] を選択する](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. サブスクリプションの一覧から、リソース プロバイダーの登録に使用するサブスクリプションを選択します。

   ![リソース プロバイダーを登録するサブスクリプションを選択する](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. サブスクリプションについては、**[リソース プロバイダー]** を選択します。

   ![[リソース プロバイダー] を選択する](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. リソース プロバイダーの一覧を確認し、必要に応じて **[登録]** リンクを選択して、デプロイするタイプのリソース プロバイダーを登録します。

   ![List resource providers](./media/resource-manager-register-provider-errors/list-resource-providers.png)
