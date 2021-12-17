---
title: Azure Container Apps 環境プレビュー
description: Azure Container Apps で環境を管理する方法について説明します。
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 07d54583b884c071708910daea45f3dcb94d14f3
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578313"
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

## <a name="logs"></a>ログ

設定は、Azure Container Apps 環境 API リソースに関連します。

| プロパティ | 説明 |
|---|---|
| `properties.appLogsConfiguration` | 環境内のすべてのアプリのログが発行される Log Analytics ワークスペースの構成に使用されます |
| `properties.containerAppsConfiguration.daprAIInstrumentationKey` | トレースのために Dapr に提供される App Insights のインストルメンテーション キー |

## <a name="billing"></a>課金

課金は、個々のコンテナー アプリとそのリソース使用量にのみ関連します。 Container Apps 環境に関連付けられている基本料金はありません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [自動スケール動作の管理](scale-app.md)