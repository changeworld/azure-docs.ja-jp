---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2020
ms.author: memildin
ms.openlocfilehash: 052758079d8d413f7b0fead2a5abf3b47b9a691e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511333"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

- [Azure Policy の評価で "適用除外" であったリソースは "準拠" とレポートされる](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [Azure セキュリティ ベンチマークのカバレッジを広げるために追加される、35 個のプレビュー推奨事項](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>Azure Policy の評価で "適用除外" であったリソースは "準拠" とレポートされる

**変更予定日:** 2021 年 1 月

現在、Azure Policy の推奨事項で **適用除外** と評価されるリソースは、"非準拠" と表示されています。 ユーザー アクションによって状態を "準拠" に変更することはできません。 今回計画されている変更以降、わかりやすくするためにこれらは "準拠" としてレポートされます。

Azure Policy への影響は 1 つだけで、準拠しているリソースの数が増えます。 Azure Security Center のセキュア スコアには影響はありません。

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure セキュリティ ベンチマークのカバレッジを広げるために追加される、35 個のプレビュー推奨事項

**変更予定日:** 2020 年 12 月

Azure セキュリティ ベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 [Azure セキュリティ ベンチマークの詳細を確認してください](../security/benchmarks/introduction.md)。

ベンチマークのカバレッジを拡大するために、次の 35 個のプレビュー推奨事項が Security Center に追加されています。

プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」を参照してください。

| セキュリティ コントロール                     | 新しい推奨事項                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 保存時の暗号化を有効にする            | - Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある<br>- MySQL サーバーに対して Bring Your Own Key データ保護を有効にする必要がある<br>- PostgreSQL サーバーに対して Bring Your Own Key データ保護を有効にする必要がある<br>- Cognitive Services アカウントでカスタマー マネージド キー (CMK) によるデータ暗号化を有効にする必要がある<br>- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある<br>- SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- ストレージ アカウントでは暗号化にカスタマー マネージド キー (CMK) を使用する必要がある                                                                                                                                                              |
| セキュリティのベストプラクティスを実装する    | - サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある<br> - 自分のサブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある<br> - 重要度が高いアラートの電子メール通知を有効にする必要がある<br> - 重要度が高いアラートについて、サブスクリプション所有者に対する電子メール通知を有効にする必要がある<br> - キー コンテナーで消去保護が有効になっている必要がある<br> - キー コンテナーで論理的な削除が有効になっている必要がある |
| アクセスおよびアクセス許可の管理        | - 関数アプリで "クライアント証明書 (着信クライアント証明書)" を有効にする必要がある |
| DDoS 攻撃からアプリケーションを保護する | - Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある<br> - Azure Front Door Service サービスに対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある |
| 承認されていないネットワーク アクセスの制限 | - キー コンテナーでファイアウォールを有効にする必要がある<br> - キー コンテナー用にプライベート エンドポイントを構成する必要がある<br> - App Configuration ではプライベート リンクを使用する必要がある<br> - Azure Cache for Redis は仮想ネットワーク内に存在しなければならない<br> - Azure Event Grid ドメインではプライベート リンクを使用する必要がある<br> - Azure Event Grid トピックではプライベート リンクを使用する必要がある<br> - Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある<br> - Azure SignalR Service ではプライベート リンクを使用する必要がある<br> - Azure Spring Cloud ではネットワークの挿入を使用する必要がある<br> - コンテナー レジストリでは無制限のネットワーク アクセスを許可しない<br> - コンテナー レジストリではプライベート リンクを使用する必要がある<br> - MariaDB サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - MySQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - PostgreSQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - ストレージ アカウントではプライベート リンク接続を使用する必要がある<br> - ストレージ アカウントでは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある<br> - VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

関連リンク:

- [Azure セキュリティ ベンチマークについての詳細情報](../security/benchmarks/introduction.md)
- [Azure Database for MariaDB についての詳細情報](../mariadb/overview.md)
- [Azure Database for MySQL についての詳細情報](../mysql/overview.md)
- [Azure Database for PostgreSQL についての詳細情報](../postgresql/overview.md)





## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。