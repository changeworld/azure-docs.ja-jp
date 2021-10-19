---
title: Azure Static Web Apps ホスティング プラン
description: さまざまな Azure Static Web Apps ホスティング プランを比較対照します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 10/05/2021
ms.author: cshoe
ms.openlocfilehash: a29a13e11416e533a27020a745daddf6e26ef919
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729636"
---
# <a name="azure-static-web-apps-hosting-plans"></a>Azure Static Web Apps ホスティング プラン

Azure Static Web Apps は、Free と Standard の 2 種類のプランで利用できます。 [Standard プランのコストについては価格のページ](https://azure.microsoft.com/pricing/details/app-service/static/)を参照してください。

## <a name="features"></a>特徴

| 機能 | Free プラン <br> (個人プロジェクトの場合) | Standard プラン <br> (運用アプリの場合) |
| --- | --- | --- |
| Web ホスティング | ✔ | ✔ |
| GitHub との統合 | ✔ | ✔ |
| Azure DevOps 統合 | ✔ | ✔ |
| グローバルに分散した静的コンテンツ | ✔ | ✔ |
| SSL 証明書の無料の自動更新 | ✔ | ✔ |
| ステージング環境 | アプリあたり 3 | アプリあたり 10 |
| アプリの最大サイズ | アプリあたり 250 MB | アプリあたり 500 MB |
| カスタム ドメイン | アプリあたり 2 | アプリあたり 5 |
| Azure Functions による API | マネージド | マネージドまたは<br>[独自の関数アプリの持ち込み](functions-bring-your-own.md) |
| 認証プロバイダーの統合 | [事前構成済み](authentication-authorization.md)<br>(サービスで定義済み) | [カスタム登録](authentication-custom.md) |
| [関数を使用したカスタム ロールの割り当て](authentication-authorization.md?tabs=function#role-management) | - | ✔ |
| プライベート エンドポイント | - | ✔ |
| [サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/app-service-static/v1_0/) | なし  | ✔ |

## <a name="selecting-a-plan"></a>プランの選択

次のシナリオは、Standard プランがニーズに最も適しているかどうかを判断するのに役立ちます。

- 予想されるトラフィック量が帯域幅の最大値を超えている。
- 使用する既存の Azure Functions アプリが、HTTP エンドポイントを超えるトリガーとバインドを含んでいるか、またはマネージド Functions アプリに変換できない。
- [カスタム プロバイダーの登録](authentication-custom.md)を必要とするセキュリティ要件。
- サイトの Web 資産の合計ファイル サイズが、ストレージの最大値を超えている。
- 正式なカスタマー サポートが必要である。
- 4 つ以上の[ステージング環境](review-publish-pull-requests.md)が必要である。

制限の詳細については、[クォータ ガイド](quotas.md)に関する記事を参照してください。

## <a name="changing-plans"></a>プランの変更

Azure portal を使用して、Free プランと Standard プランの間を移動できます。

1. Azure portal でお使いの Static Web Apps リソースに移動します。

1. _[設定]_ メニューの **[ホスティング プラン]** を選択します。

1. お使いの静的 Web アプリに必要なホスティング プランを選択します。

1. **[保存]** を選択します。
