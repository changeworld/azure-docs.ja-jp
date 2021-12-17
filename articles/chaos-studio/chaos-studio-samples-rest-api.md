---
title: REST API を使用して Azure Chaos Studio の実験を管理する
description: REST API を使用して、Azure Chaos Studio でカオス実験を実行して管理します。
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: f06c89e61f9ed1889502417ca5708493dd9d2e33
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718471"
---
# <a name="use-the-chaos-studio-rest-apis-to-run-and-manage-chaos-experiments"></a>Chaos Studio REST API を使用してカオス実験を実行および管理する

> [!WARNING]
> 障害を挿入すると、アプリケーションまたはサービスに影響を与える可能性があります。 顧客の処理を中断しないように注意してください。  

Chaos Studio API は、プログラムで実験を開始するためのサポートを提供します。 armclient と Azure CLI を使用して、これらのコマンドをコンソールから実行することもできます。 次の例は、Azure CLI 用です。

> [!Warning]
> これらの API は現在開発中であり、変更される可能性があります。

## <a name="rest-apis"></a>REST API

Squall REST API を使用すると、実験の開始と停止、ターゲットの状態のクエリ、実験の状態のクエリ、サブスクリプション構成のクエリと削除を実行できます。 `AZ CLI` ユーティリティを使用して、コマンド ラインからこれらのアクションを実行できます。

> [!TIP]
> AZ CLI でさらに詳細な出力を取得するには、各コマンドの末尾に **--verbose** を追加します。 これにより、デバッグに役立つ **x-ms-correlation-request-id** など、コマンドの実行時により多くのメタデータが返されます。

### <a name="chaos-provider-commands"></a>カオス プロバイダー コマンド

#### <a name="enumerate-details-about-the-microsoftchaos-resource-provider"></a>Microsoft.Chaos リソース プロバイダーに関する詳細を列挙する

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-all-the-operations-of-the-chaos-studio-resource-provider"></a>Chaos Studio リソース プロバイダーのすべての操作を一覧表示する

```bash
az rest --method get --url "https://management.azure.com/providers/Microsoft.Chaos/operations?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-chaos-provider-configurations"></a>カオス プロバイダーの構成を一覧表示する

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/?api-version={apiVersion}" --resource "https://management.azure.com" --verbose 
```

#### <a name="create-chaos-provider-configuration"></a>カオス プロバイダーの構成を作成する

```bash
az rest --method put --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/{chaosProviderType}?api-version={apiVersion}" --body @{providerSettings.json} --resource "https://management.azure.com"
```

### <a name="chaos-target-and-agent-commands"></a>カオス ターゲット コマンドとエージェント コマンド

#### <a name="list-all-the-targets-or-agents-under-a-subscription"></a>サブスクリプションに含まれるすべてのターゲットまたはエージェントを一覧表示する

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos/chaosTargets/?api-version={apiVersion}" --url-parameter "chaosProviderType={chaosProviderType}" --resource "https://management.azure.com"
```

### <a name="chaos-experiment-commands"></a>カオス実験コマンド

#### <a name="list-all-experiments-in-a-resource-group"></a>リソース グループ内のすべての実験を一覧表示する

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Chaos/chaosExperiments?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-an-experiment-configuration-details-by-name"></a>実験構成の詳細を名前で取得する

```bash
az rest --method get --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="create-or-update-an-experiment"></a>実験を作成または更新する

```bash
az rest --method put --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --body @{experimentName.json} --resource "https://management.azure.com"
```

#### <a name="delete-an-experiment"></a>実験の削除

```bash
az rest --method delete --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com" --verbose
```

#### <a name="start-an-experiment"></a>実験を開始する

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/start?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-statuses-history-of-an-experiment"></a>実験の状態 (履歴) を取得する

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/statuses?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-status-of-an-experiment"></a>実験の状態を取得する

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/status?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="cancel-stop-an-experiment"></a>実験をキャンセル (停止) する

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/cancel?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-the-last-two-experiment-executions"></a>最後の 2 つの実験実行の詳細を一覧表示する

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-a-specific-experiment-execution"></a>特定の実験実行の詳細を一覧表示する

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails/{executionDetailsId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

## <a name="parameter-definitions"></a>パラメーターの定義

| パラメーター名 | 定義 | 参照 |
| --- | --- | --- |
| {apiVersion} | 指定されたコマンドを実行するときに使用する API のバージョン | 詳しくは、[API ドキュメント](/rest/api/chaosstudio/)を参照してください。 |
| {experimentId} | 実験の Azure リソース ID | [Chaos Studio 実験ポータル ブレード](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments)で確認できます。 |
| {chaosProviderType} | Chaos Studio プロバイダーの種類または名前 | 使用可能なプロバイダーについては、[現在のプロバイダー構成の種類の一覧](chaos-studio-fault-providers.md)を参照してください。 |
| {experimentName.json} | カオス実験の構成を含む JSON | ユーザーによって生成される |
|  {subscriptionId}  | ターゲット リソースがあるサブスクリプション ID | [サブスクリプション ポータル ブレード](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で確認できます。 |
| {resourceGroupName} | ターゲット リソースがあるリソース グループの名前 | [リソース グループ ポータル ブレード](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)で確認できます |
| {executionDetailsId} | 実験実行の実行 ID | [Chaos Studio 実験ポータル ブレード](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments)で確認できます。 |