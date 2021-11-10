---
title: Azure Container Apps プレビューのリビジョン
description: Azure Container Apps でリビジョンがどのように作成されるかについて説明します
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: daa5f00ba30bfcd0af41e92339518f0d9c1f44ae
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134068"
---
# <a name="revisions-in-azure-container-apps-preview"></a>Azure Container Apps プレビューのリビジョン

リビジョンは、コンテナー アプリの不変スナップショットです。

- コンテナー アプリをデプロイすると、最初のリビジョンが自動的に作成されます。
- コンテナー アプリの `template` 構成が変更されると、新しいリビジョンが自動的に作成されます。
- リビジョンは変更できませんが、グローバル構成値の変更の影響を受けます。この変更は、すべてのリビジョンに適用されます。

:::image type="content" source="media/revisions/azure-container-apps-revisions.png" alt-text="Azure Container Apps: コンテナー":::

リビジョンは、[イングレス](ingress.md)を有効にしてコンテナー アプリに HTTP 経由でアクセスできるようにする場合に最も役立ちます。  リビジョンは、コンテナー アプリのあるスナップショットから次のスナップショットにトラフィックを転送する場合によく使用されます。 一般的なトラフィックの方向の戦略には、[A/B テスト](https://wikipedia.org/wiki/A/B_testing)と[ブルーグリーン デプロイ](https://martinfowler.com/bliki/BlueGreenDeployment.html)が含まれます。

次の図は、2 つのリビジョンを持つコンテナー アプリを示しています。

:::image type="content" source="media/revisions/azure-container-apps-revisions-traffic-split.png" alt-text="Azure Container Apps: リビジョン間でのトラフィックの分割":::

上記のシナリオは、コンテナー アプリが次の状態であることを前提としています。

- [イングレス](ingress.md)が有効になっている。これにより、HTTP 経由でコンテナー アプリが利用できるようになります。
- 最初のリビジョンは、"_リビジョン 1_" としてデプロイされます。
- コンテナーが更新された後、新しいリビジョンが "_リビジョン 2_" としてアクティブ化されました。
- [トラフィックの分割](revisions-manage.md#traffic-splitting)ルールは、"_リビジョン 1_" が要求の 80% を受信し、"_リビジョン 2_" が残りの 20% を受信するように構成されています。

## <a name="change-types"></a>変更の種類

コンテナー アプリに加えられた変更は、"*リビジョンスコープ*" と "*アプリケーションスコープ*" の変更の 2 つのカテゴリのいずれかに分類されます。 リビジョンスコープの変更は、新しいリビジョンをトリガーする変更ですが、アプリケーションスコープの変更ではリビジョンは作成されません。

### <a name="revision-scope-changes"></a>リビジョンスコープの変更

次の種類の変更では、新しいリビジョンが作成されます。

- コンテナーへの変更
- スケーリング ルールの追加または更新
- Dapr 設定への変更
- 構成の `template` セクションに影響するすべての変更

### <a name="application-scope-changes"></a>アプリケーションスコープの変更

次の種類の変更では、新しいリビジョンは作成されません。

- [トラフィックの分割ルール](revisions-manage.md#traffic-splitting)への変更
- [イングレス](ingress.md)のオンとオフを切り替える
- [シークレット値](secure-app.md)への変更
- 構成の `template` セクション以外のすべての変更

シークレットへの変更はアプリケーションスコープの変更ですが、コンテナーが新しいシークレット値を認識する前に、リビジョンを[再起動する](revisions.md)必要があります。

## <a name="activation-state"></a>アクティブ化の状態

新しいリビジョンは、非アクティブ化するか、古いリビジョンを自動的に非アクティブ化するようにコンテナー アプリを設定するまで、アクティブなままです。

- 非アクティブなリビジョンは、特定の状態のコンテナー アプリのスナップショット レコードとして残ります。
- 非アクティブなリビジョンに対しては課金されません。
- 最大 100 のリビジョンが、削除されるまで利用できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリをセキュリティで保護する](secure-app.md)
