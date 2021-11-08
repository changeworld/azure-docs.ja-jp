---
title: Azure Container Apps プレビューでのアプリケーション ライフサイクル管理
description: Azure Container Apps プレビューの完全なアプリケーション ライフサイクルについて説明します
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: f731ceae2059c5b0e48f1f1428b60a988ee12ce0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027187"
---
# <a name="application-lifecycle-management-in-azure-container-apps-preview"></a>Azure Container Apps プレビューでのアプリケーション ライフサイクル管理

Azure Container Apps アプリケーションのライフサイクルは、[リビジョン](revisions.md)を中心に展開されます。

コンテナー アプリをデプロイすると、最初のリビジョンが自動的に作成されます。 [コンテナー](containers.md)の変更時に[さらにリビジョンが作成](revisions.md)されたり、構成の `template` セクションに対して調整が行われたりします。

コンテナー アプリには、デプロイ、更新、非アクティブ化の 3 つのフェーズがあります。

## <a name="deployment"></a>展開

コンテナー アプリがデプロイされると、最初のリビジョンが自動的に作成されます。

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deployment.png" alt-text="Azure Container Apps: デプロイ フェーズ":::

## <a name="update"></a>更新

コンテナー アプリが[リビジョン スコープ変更](revisions.md#revision-scope-changes)で更新されると、新しいリビジョンが作成されます。 [新旧のリビジョンを自動的に非アクティブ化するか、または使用可能なままにするか](revisions.md)選べます。

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-update.png" alt-text="Azure Container Apps: 更新フェーズ":::

## <a name="deactivate"></a>非アクティブ化

リビジョンが不要になったら、後で再アクティブ化するオプション付きでリビジョンを非アクティブ化できます。 非アクティブ化中、コンテナーは[シャットダウン](#shutdown)されます。

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deactivate.png" alt-text="Azure Container Apps: 非アクティブ化フェーズ":::

## <a name="shutdown"></a>Shutdown

コンテナーは、次の状況でシャットダウンされます。

- コンテナー アプリのスケールイン時
- コンテナー アプリが削除される
- リビジョンが非アクティブ化されている

シャットダウンが開始されると、コンテナー ホストは [SIGTERM メッセージ](https://wikipedia.org/wiki/Signal_(IPC))をコンテナーに送信します。 コンテナーに実装されているコードでは、このオペレーティング システム レベルのメッセージに応答して終了処理をすることができます。

アプリケーションが `SIGTERM` メッセージに応答しない場合、[SIGKILL](https://wikipedia.org/wiki/Signal_(IPC)) でコンテナーが終了します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [リビジョンを使う](revisions.md)
