---
title: Azure Container Apps 環境プレビュー
description: Azure Container Apps で環境を管理する方法について説明します。
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3de8b98a992a97bb96d5e6164321e89da01f6342
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092060"
---
# <a name="azure-container-apps-preview-environments"></a>Azure Container Apps プレビュー環境

個々のコンテナー アプリは 1 つの Container Apps 環境にデプロイされます。これは、コンテナー アプリのグループに対するセキュリティで保護された境界として機能します。 同じ環境内のコンテナー アプリは、同じ仮想ネットワークにデプロイされ、同じ Log Analytics ワークスペースにログを書き込みます。

:::image type="content" source="media/environments/azure-container-apps-environments.png" alt-text="Azure Container Apps 環境。":::

コンテナー アプリを同じ環境にデプロイする理由には、以下が必要な状況が含まれます。

- 管理に関連するサービス
- 異なるアプリケーションを同じ仮想ネットワークにデプロイする
- アプリケーションが Dapr を使用して相互に通信する
- 複数のアプリケーションで同じ Dapr 構成を共有する
- 複数のアプリケーションで同じ Log Analytics ワークスペースを共有する

コンテナー アプリをさまざまな環境にデプロイする理由には、以下を確実にしたい状況が含まれます。

- 2 つのアプリケーションが同じコンピューティング リソースを共有しない
- 2 つのアプリケーションが Dapr 経由で相互に通信できない

## <a name="virtual-network-integration"></a>Virtual Network 統合

各環境には、外部 IP アドレスが自動的に割り当てられます。 ただし、仮想ネットワークの外部からアクセスできないように個々のコンテナー アプリを構成することができます。

| プロパティ | 説明 |
|---|---|
| `properties.workerAppsConfiguration.subnetResourceId` | 環境インフラストラクチャに使用されるサブネットの Azure Resource Manager リソース ID。 |
| `properties.workerAppsConfiguration.aciSubnetResourceName` | コンテナー アプリが実行されているのと同じ VNET 内のサブネットの名前。 |

## <a name="logs"></a>ログ

Kubernetes 環境 API リソースに関連する設定。

| プロパティ | 説明 |
|---|---|
| `properties.appLogsConfiguration` | 環境内のすべてのアプリのログが発行される Log Analytics ワークスペースの構成に使用されます |
| `properties.workerAppsConfiguration.daprAIInstrumentationKey` | トレースのために Dapr に提供される App Insights のインストルメンテーション キー |

## <a name="billing"></a>課金

課金は、個々のコンテナー アプリとそのリソース使用量にのみ関連します。 Container Apps 環境に関連付けられている基本料金はありません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [自動スケール動作の管理](scale-app.md)
