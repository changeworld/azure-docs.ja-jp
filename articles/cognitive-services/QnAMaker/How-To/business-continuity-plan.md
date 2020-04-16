---
title: ビジネス継続性計画 - QnA Maker
description: ビジネス継続性計画の主な目的は、回復性に優れたナレッジ ベース エンドポイントを作成し、そのナレッジ ベース エンドポイントを使用するボットまたはアプリケーションにダウン タイムが発生しないようにすることです。
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887072"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>QnA Maker サービスのビジネス継続性計画を作成する

ビジネス継続性計画の主な目的は、回復性に優れたナレッジ ベース エンドポイントを作成し、そのナレッジ ベース エンドポイントを使用するボットまたはアプリケーションにダウン タイムが発生しないようにすることです。

## <a name="business-continuity-with-traffic-manager"></a>トラフィック マネージャーを使用したビジネス継続性

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 計画](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上に示した概念の説明は次のとおりです。

1. [ペアになっている Azure リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)に、同一の [QnA Maker サービス](set-up-qnamaker-service-azure.md)を 1 つずつ、計 2 つ設定します。

2. プライマリ QnA Maker App サービスを[バックアップ](../../../app-service/manage-backup.md)して、セカンダリ セットアップで[復元](../../../app-service/web-sites-restore.md)します。 これにより、両方のセットアップが同じホスト名とキーで動作するようになります。

3. プライマリとセカンダリの Azure 検索インデックスを常に同期しておきます。Azure のインデックスをバックアップおよび復元する方法については、[GitHub](https://github.com/pchoudhari/QnAMakerBackupRestore) でサンプルを参照してください。

4. [連続エクスポート](../../../application-insights/app-insights-export-telemetry.md)を使って Application Insights をバックアップします。

5. プライマリとセカンダリのスタックを設定したら、[トラフィック マネージャー](../../../traffic-manager/traffic-manager-overview.md)を使用して 2 つのエンドポイントを構成し、ルーティング方法を設定します。

6. トラフィック マネージャーのエンドポイント向けに Transport Layer Security (TLS) (旧称は Secure Sockets Layer (SSL)) 証明書を作成する必要があります。 アプリ サービスで [TLS/SSL 証明書](../../../app-service/configure-ssl-bindings.md)をバインドします。

7. 最後に、ボットまたはアプリでトラフィック マネージャー エンドポイントを使用します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [容量の選択](./improve-knowledge-base.md)