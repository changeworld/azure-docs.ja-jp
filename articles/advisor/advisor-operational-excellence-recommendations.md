---
title: Advisor を使用してオペレーショナル エクセレンスを向上させる
description: Azure Advisor を使用して、Azure サブスクリプションのオペレーショナル エクセレンスを最適化し、成熟させます。
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 1cac5a3f93df8422a3896b1251857bf552731fb4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85125397"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Azure Advisor を使用してオペレーショナル エクセレンスを実現する

Azure Advisor でのオペレーショナル エクセレンスの推奨事項は、次の点で役立ちます。 
- プロセスとワークフローの効率。
- リソースの管理容易性。
- デプロイのベスト プラクティス。 

これらの推奨事項については、Advisor ダッシュボードの **[オペレーショナル エクセレンス]** タブを見るとわかります。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure の問題による影響を受けるときに通知される Azure Service Health アラートを作成する

Azure サービスの問題による影響を受けるときに通知されるよう、Azure Service Health アラートを設定することをお勧めします。 [Azure Service Health](https://azure.microsoft.com/features/service-health/) は、Azure サービスの問題による影響を受ける場合に、カスタマイズされたガイダンスとサポートを提供する無料サービスです。 Advisor はアラートが構成されていないサブスクリプションを識別し、構成するように推奨します。


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>サブスクリプションの上限に到達しないようにストレージ アカウントを設計する

Azure リージョンでは、サブスクリプションごとに最大 250 個のストレージ アカウントをサポートできます。 この制限に達したら、そのリージョンとサブスクリプションの組み合わせでストレージ アカウントを作成することができなくなります。 Advisor では、サブスクリプションが確認されて、リージョンとサブスクリプションが上限に近づいている場合はストレージ アカウント数を減らして設計するように勧告されます。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>必要な時に Azure クラウドの専門家を利用できるようにする

ビジネスに不可欠なワークロードを実行しているとき、必要時にはテクニカル サポートを受けることができるようにしておくことが重要です。 Advisor は、サポート プランにサポート チームが含まれていない Bus Critical なサブスクリプションを特定します。 サポート チームを含むオプションにアップグレードすることが推奨されます。

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>プールを削除して再作成し、非推奨の内部コンポーネントを削除する

プールで非推奨の内部コンポーネントが使用されている場合は、安定性とパフォーマンスを向上させるためにプールを削除して再作成します。

## <a name="repair-invalid-log-alert-rules"></a>無効なログ アラート ルールを修復する

Azure Advisor は、条件セクションに無効なクエリが指定された警告ルールを検出します。 Azure Monitor でログ警告ルールを作成し、それらを使用して、指定された間隔で分析クエリを実行できます。 クエリの結果によって、アラートをトリガーする必要があるかどうかが決定されます。 分析クエリは、参照されるリソース、テーブル、またはコマンドの変更のために、時間の経過と共に無効になることがあります。 Advisor は、警告ルールが自動的に無効になることを防止し、Azure でのリソースの監視範囲を保証するために、警告ルール内のクエリを修正することを推奨します。 [アラート ルールのトラブルシューティング](https://aka.ms/aa_logalerts_queryrepair)の詳細を確認してください。

## <a name="use-azure-policy-recommendations"></a>Azure Policy の推奨事項を使用する

Azure Policy は、ポリシーの作成、割り当て、管理に使用できる Azure のサービスです。 これらのポリシーでは、リソースに対してルールが適用され、それらが影響を受けます。 次の Azure Policy の推奨事項は、オペレーショナル エクセレンスの実現に役立ちます。 

**タグを管理する。** このポリシーは、任意のリソースが作成または更新されたときに、指定されたタグと値を追加または置換します。 修復タスクをトリガーすることで、既存のリソースを修復できます。 このポリシーによってリソース グループのタグが変更されることはありません。

**geo コンプライアンス要件を強制する。** このポリシーでは、リソースをデプロイするときに組織が指定できる場所を制限できます。 

**デプロイに対して許可される仮想マシンの SKU を指定する。** このポリシーを使用して、組織でデプロイできる仮想マシン SKU のセットを指定できます。

"***Managed Disks を使用していない VM の監査*" を強制する。**

**"*リソース グループからのタグの継承*" を有効にする。** このポリシーは、任意のリソースが作成または更新されるときに、親リソース グループのタグと値を追加または置換します。 修復タスクをトリガーすることで、既存のリソースを修復できます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、以下を参照してください。
* [Advisor 入門](advisor-overview.md)
* [開始するには](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor の信頼性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
