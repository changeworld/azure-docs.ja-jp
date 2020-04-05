---
title: Azure Advisor を使用して Azure サブスクリプションのオペレーショナル エクセレンスを向上させる
description: Advisor を使用して、Azure サブスクリプションのオペレーショナル エクセレンスを最適化し、成熟させます
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443073"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Azure Advisor でオペレーショナル エクセレンスを実現する

Azure Advisor のオペレーショナル エクセレンスに関する推奨事項は、プロセスとワークフローの効率、リソースの管理容易性、デプロイのベスト プラクティスによってお客様を支援します。 これらの推奨事項については、Advisor ダッシュボードの **[オペレーショナル エクセレンス]** タブを見るとわかります。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure の問題による影響を受けたときに通知する Azure Service Health アラートを作成する

Azure サービスの問題による影響を受けたときに通知する Azure Service Health アラートを設定することをお勧めします。 [Azure Service Health](https://azure.microsoft.com/features/service-health/) は、Azure サービスの問題による影響を受けた場合に、カスタマイズされたガイダンスとサポートを提供する無料サービスです。 Advisor はアラートが構成されていないサブスクリプションを識別し、アラートの作成を推奨します。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>サブスクリプションの上限に到達しないようにストレージ アカウントを設計する

Azure リージョンでは、サブスクリプションごとに最大 250 個のストレージ アカウントをサポートできます。 制限に達した場合、そのリージョン/サブスクリプションの組み合わせでは、それ以上ストレージ アカウントを作成することはできません。 Advisor は、サブスクリプションを確認して、上限に近づいている場合はストレージ アカウント数を減らして設計するように勧告します。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>必要な時に Azure クラウドの専門家を利用できるようにする

ビジネスに不可欠なワークロードを実行しているとき、必要時にはテクニカル サポートを受けることができるようにしておくことが重要です。 Advisor は、サポート計画にテクニカル サポートが含まれないビジネスに不可欠なサブスクリプションを識別し、テクニカル サポートが含まれるオプションへのアップグレードを勧めします。

## <a name="repair-invalid-log-alert-rules"></a>無効なログ アラート ルールを修復する

Azure Advisor は、それぞれの状態セクションで指定された無効なクエリを含むアラート ルールを検出します。 ログ アラート ルールは Azure Monitor で作成され、指定された間隔で分析クエリを実行するために使用されます。 クエリの結果によって、アラートをトリガーする必要があるかどうかが決定されます。 分析クエリは、参照されるリソース、テーブル、またはコマンドの変更のために、時間の経過と共に無効になることがあります。 Advisor は、アラート ルールが自動的に無効になることを防止し、Azure でのリソースの監視範囲を保証するために、アラート ルール内のクエリを修正することを推奨します。 [アラート ルールのトラブルシューティング](https://aka.ms/aa_logalerts_queryrepair)の詳細を確認してください。

## <a name="follow-best-practices-using-azure-policy"></a>Azure Policy を使用してベスト プラクティスに従う

Azure Policy は、ポリシーの作成、割り当て、管理に使用する Azure のサービスです。 これらのポリシーでは、リソースに対して異なるルールが適用され、それらに影響を与えます。 オペレーショナル エクセレンスの実現に役立つ Azure Policy の推奨事項を以下に示します。 
1. Azure Policy を使用してタグを管理する: このポリシーは、任意のリソースが作成または更新されたときに、指定されたタグと値を追加または置換します。 修復タスクをトリガーすることで、既存のリソースを修復できます。 また、これによりリソース グループのタグが変更されることはありません。
2. Azure Policy を使用して geo コンプライアンス要件を適用する: ポリシーでは、リソースをデプロイするときに組織が指定できる場所を制限できます。 
3. デプロイに対して許可される仮想マシンの SKU を指定する: このポリシーを使用して、組織でデプロイできる仮想マシン SKU のセットを指定できます。
4. Azure Policy を使用して、"マネージド ディスクを使用していない VM を監査する" ポリシーを適用する
5. Azure Policy を使用して、"リソース グループからタグを継承する" を使用する: このポリシーでは、任意のリソースが作成または更新されるときに、親リソース グループのタグと値が追加または置換されます。 修復タスクをトリガーすることで、既存のリソースを修復できます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、以下を参照してください。
* [Advisor 入門](advisor-overview.md)
* [開始するには](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
