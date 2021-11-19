---
title: Microsoft Defender for Cloud の強化されたセキュリティ機能について理解する
description: Microsoft Defender for Cloud の強化されたセキュリティを有効にする利点について説明します
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: overview
ms.date: 11/14/2021
ms.openlocfilehash: 4f17d5228bdac13797aaef0fe4441f61d474042a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557111"
---
# <a name="microsoft-defender-for-clouds-enhanced-security-features"></a>Microsoft Defender for Cloud の強化されたセキュリティ機能

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

強化されたセキュリティ機能は、最初の 30 日間無料です。 30 日経過した時点で、このサービスの利用を継続することを選択した場合、使用量に応じた課金が自動的に開始されます。

「[クイックスタート: 強化されたセキュリティ機能を有効にする](enable-enhanced-security.md)」で説明されているように、 **[環境設定]** ページからアップグレードできます。 選択した通貨でのお住まいのリージョンに応じた価格の詳細については、[価格ページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

:::image type="content" source="media/enhanced-security-features-overview/defender-plans-top.png" alt-text="Microsoft Defender for Cloud の強化されたセキュリティ機能を有効にする。":::

## <a name="what-are-the-benefits-of-enabling-enhanced-security-features"></a>強化されたセキュリティ機能を有効にする利点は何ですか?

Defender for Cloud は、次の 2 つのモードで提供されます。

- **強化されたセキュリティ機能なし** (無料) - Defender for Cloud は、Azure portal 内のワークロード保護ダッシュボードに初めてアクセスしたとき、または API を介してプログラムで有効にした場合に、すべての Azure サブスクリプションで無料で有効になります。 この無料モードを使用すると、セキュリティ スコアとそれに関連する機能 (セキュリティ ポリシー、継続的なセキュリティ評価、Azure リソースの保護に役立つ実践的なセキュリティの推奨事項) が提供されます。

- **強化されたすべてのセキュリティ機能を備えた Defender for Cloud** - 強化されたセキュリティを有効にすると、無料モードの機能が、プライベートおよび他のパブリック クラウドで実行されるワークロードまで拡張され、ハイブリッド クラウド ワークロード全体に統合されたセキュリティ管理と脅威防止機能が提供されます。 主な利点を次に示します。

    - **Microsoft Defender for Endpoint** - Microsoft Defender for servers には、包括的なエンドポイントの検出と応答 (EDR) のために、[Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) が含まれています。 Microsoft Defender for Endpoint を Defender for Cloud と共に使用する利点については、[Defender for Cloud の統合された EDR ソリューションの使用](integration-defender-for-endpoint.md)に関するページを参照してください。
    - **仮想マシン、コンテナー レジストリ、SQL リソースの脆弱性評価** - 脆弱性評価ソリューションを簡単に有効にして、脆弱性を検出、管理、解決することができます。 結果の表示、調査、修復を Defender for Cloud 内から直接実行できます。
    - **マルチクラウドのセキュリティ** - アマゾン ウェブ サービス (AWS) と Google Cloud Platform (GCP) のアカウントを接続して、Microsoft Defender for Cloud のセキュリティ機能の範囲で、それらのプラットフォームのリソースとワークロードを保護できます。
    - **ハイブリッド セキュリティ** - オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 セキュリティ ポリシーを適用し、ハイブリッド クラウドのワークロードのセキュリティを継続的に評価することで、セキュリティ標準に確実に準拠できます。 ファイアウォールやその他のパートナー ソリューションなどのさまざまなソースから、セキュリティ データを収集、検索、分析します。
    - **脅威防止アラート** - 高度な行動分析と Microsoft インテリジェント セキュリティ グラフを使用して、巧妙化するサイバー攻撃に対応します。 組み込みの行動分析と機械学習により、各種攻撃やゼロデイ攻撃を特定することができます。 ネットワーク、マシン、データ ストア (azure の内部と外部でホストされている SQL サーバー、Azure SQL データベース、Azure SQL Managed Instance、Azure Storage)、クラウドサービスを監視して、着信攻撃や侵害後のアクティビティを確認します。 対話型のツールと状況に応じた脅威インテリジェンスにより、調査を効率化します。
    - **さまざまな標準でのコンプライアンスの追跡** - Defender for Cloud は、お使いのハイブリッド クラウド環境を継続的に評価して、[Azure セキュリティ ベンチマーク](/security/benchmark/azure/introduction)の制御とベスト プラクティスに従ってリスク要因を分析します。 強化されたセキュリティ機能を有効にすると、ご自分の組織のニーズに応じて、他の業界標準、規制標準、ベンチマークを広範に適用できます。 [規制コンプライアンス ダッシュボード](update-regulatory-compliance-packages.md)から、標準を追加したり、コンプライアンスを追跡したりすることができます。
    - **アクセスとアプリケーションの制御** - 特定のワークロードに適応する、機械学習を活用した推奨事項を適用して、許可リストとブロックリストを作成することで、マルウェアや他の望ましくないアプリケーションをブロックします。 Azure VM の管理ポートに対するジャスト イン タイムの制御されたアクセスで、ネットワーク攻撃対象領域を減らします。 アクセスとアプリケーションの制御によって、ブルート フォース攻撃やその他のネットワーク攻撃にさらされる可能性が大幅に低くなります。
    - **コンテナーのセキュリティ機能** - コンテナー化された環境で、脆弱性管理とリアルタイムの脅威の防止が利用できます。 料金は、接続されたレジストリにプッシュされた一意のコンテナー イメージの数に基づいています。 イメージのスキャンが 1 回完了すると、変更されてもう 1 度プッシュされない限り、再度課金されることはありません。
    - **Azure に接続されているリソースの広範な脅威保護** - すべてのリソースに共通する Azure サービス (Azure Resource Manager、Azure DNS、Azure ネットワーク レイヤー、Azure Key Vault) のための Cloud ネイティブの脅威保護。 Defender for Cloud では、Azure 管理レイヤーと Azure DNS レイヤーを独自の視点で可視化できるため、これらのレイヤーに接続されているクラウド リソースを保護できます。


## <a name="faq---pricing-and-billing"></a>FAQ - 価格と課金 

- [Defender for Cloud の Microsoft Defender プランを有効にした組織内のユーザーをどのような方法で追跡できますか?](#how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud)
- [Defender for Cloud では、どのようなプランが提供されていますか?](#what-are-the-plans-offered-by-defender-for-cloud)
- [自分のサブスクリプションで Defender for Cloud の強化されたセキュリティを有効にするにはどのようにしますか?](#how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription)
- [自分のサブスクリプションで、サーバーのサブセットに対して Microsoft Defender for servers を有効にすることはできますか?](#can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Microsoft Defender for Endpoint のライセンスが既にある場合、Defender for servers の割引を受けることはできますか?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers)
- [自分のサブスクリプションで Microsoft Defender for servers が有効になっています。実行していないサーバーは課金されますか?](#my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Log Analytics エージェントがインストールされていないマシンは課金されますか?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Log Analytics エージェントが複数のワークスペースにレポートする場合、二重に課金されるのですか?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Log Analytics エージェントが複数のワークスペースにレポートする場合、それらすべてで 500 MB 無料のデータ インジェストが利用できるのですか?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [500 MB 無料のデータ インジェストの計算対象は、ワークスペース全体ですか、それとも厳密にマシン単位ですか?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [500 MB のデータの 1 日あたりの許容量には、どの種類のデータが含まれますか?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud"></a>Defender for Cloud の Microsoft Defender プランを有効にした組織内のユーザーをどのような方法で追跡できますか?
Azure サブスクリプションには、価格設定を変更する許可が与えられた管理者が複数設定されていることがあります。 変更を行ったユーザーを見つけるには、Azure アクティビティ ログを使用します。

:::image type="content" source="media/enhanced-security-features-overview/logged-change-to-pricing.png" alt-text="価格変更イベントを示す Azure アクティビティ ログ。":::

**[イベント開始者]** 列の一覧にユーザーの情報が表示されない場合は、イベントの JSON から適切な情報を探してください。

:::image type="content" source="media/enhanced-security-features-overview/tracking-pricing-changes-in-activity-log.png" alt-text="Azure アクティビティ ログの JSON エクスプローラー。":::


### <a name="what-are-the-plans-offered-by-defender-for-cloud"></a>Defender for Cloud では、どのようなプランが提供されていますか? 
Microsoft Defender for Cloud の無料プランでは、セキュリティ スコアと関連ツールが提供されます。 強化されたセキュリティを有効にすると、すべての Microsoft Defender プランが有効になり、Azure、ハイブリッド、マルチクラウド環境のすべてのリソースに対してさまざまなセキュリティ上の利点が得られます。  

### <a name="how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription"></a>自分のサブスクリプションで Defender for Cloud の強化されたセキュリティを有効にするにはどのようにしますか? 
以下に示すどの方法でも、ご利用のサブスクリプションに対して強化されたセキュリティを有効にすることができます。 

| メソッド                                          | 手順                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure portal 内の Defender for Cloud のページ    | [強化された保護を有効にする](enable-enhanced-security.md)                                                                                         |
| REST API                                        | [Pricings API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [バンドルの価格](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>自分のサブスクリプションで、サーバーのサブセットに対して Microsoft Defender for servers を有効にすることはできますか?
いいえ。 サブスクリプションで [Microsoft Defender for servers](defender-for-servers-introduction.md) を有効にすると、サブスクリプション内のすべてのマシンが Defender for servers によって保護されます。

また、Log Analytics ワークスペース レベルで Microsoft Defender for servers を有効にする方法もあります。 この場合、そのワークスペースにレポートするサーバーだけが保護され、課金されるようになります。 ただし、いくつかの機能が利用できなくなります。 それらの例としては、Just-in-Time VM アクセス、ネットワーク検出、規制コンプライアンス、アダプティブ ネットワークのセキュリティ強化機能、適応型アプリケーション制御などが挙げられます。 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers"></a>Microsoft Defender for Endpoint のライセンスが既にある場合、Defender for servers の割引を受けることはできますか?
Microsoft Defender for Endpoint のライセンスを既に取得している場合は、Defender for servers のライセンスのその部分について料金を払う必要はありません。

割引を依頼するには、Defender for Cloud のサポート チームに連絡し、割引の対象となるワークスペースの ID、リージョン、そのワークスペースのマシンに対して申請した Microsoft Defender for Endpoint ライセンスの数をお伝えください。

割引は承認日から有効となり、さかのぼって適用されることはありません。

### <a name="my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>自分のサブスクリプションで Microsoft Defender for servers が有効になっています。実行していないサーバーは課金されますか? 
いいえ。 サブスクリプションで [Microsoft Defender for servers](defender-for-servers-introduction.md) を有効にしても、割り当て解除済みの電源状態のマシンは、その状態である間は課金されません。 マシンは電源の状態に基づいて課金されます。次の表を参照してください。

| State        | 説明                                                                                                                                      | 課金されたインスタンスの使用 |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| 開始中     | VM を起動中です。                                                                                                                               | 課金されません            |
| 実行中      | VM の通常の動作状態です                                                                                                                    | 課金されます                |
| 停止中     | これは過渡的な状態です。 完了すると、Stopped と表示されます。                                                                           | 課金されます                |
| 停止済み      | ゲスト OS 内から、または PowerOff API によって、VM がシャットダウンされています。 ハードウェアは引き続き VM に割り当てられ、VM はホスト上に残ります。 | 課金されます                |
| 割り当て解除中 | 過渡的な状態です。 完了すると、VM は割り当て解除済みと表示されます。                                                                             | 課金されません            |
| 割り当て解除済み  | VM は正常に停止され、ホストから削除されました。                                                                                  | 課金されません            |

:::image type="content" source="media/enhanced-security-features-overview/deallocated-virtual-machines.png" alt-text="割り当て解除状態のマシンを示す Azure Virtual Machines。":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Log Analytics エージェントがインストールされていないマシンは課金されますか?
正解です。 サブスクリプションで [Microsoft Defender for servers](defender-for-servers-introduction.md) を有効にすると、そのサブスクリプション内のマシンには、Log Analytics エージェントがインストールされていなくても一連の保護が適用されます。 これは、Azure 仮想マシン、Azure 仮想マシン スケール セット インスタンス、Azure Arc 対応サーバーに適用されます。

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Log Analytics エージェントが複数のワークスペースにレポートする場合、二重に課金されるのですか? 
はい。 2 つ以上の異なる Log Analytics ワークスペース (マルチホーム) にデータを送信するように Log Analytics エージェントを構成した場合、"セキュリティ" または "マルウェア対策" ソリューションがインストールされている各ワークスペースが課金の対象となります。 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Log Analytics エージェントが複数のワークスペースにレポートする場合、それらすべてで 500 MB 無料のデータ インジェストが利用できるのですか?
正解です。 2 つ以上の異なる Log Analytics ワークスペース (マルチホーム) にデータを送信するように Log Analytics エージェントを構成した場合、500 MB 分のデータ インジェストが無料になります。 これはノード単位、レポート先ワークスペース単位、日単位で計算され、"セキュリティ" または "マルウェア対策" ソリューションがインストールされているどのワークスペースでも利用できます。 500 MB の制限を超えて取り込まれたデータについては課金の対象となります。

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>500 MB 無料のデータ インジェストの計算対象は、ワークスペース全体ですか、それとも厳密にマシン単位ですか?
ワークスペースに接続されている各 Windows マシンについて、1 日あたり 500 MB のデータ インジェストが無料で提供されます。 具体的には、Defender for Cloud によって直接収集されたセキュリティ データのタイプが対象となります。 

このデータは、1 日あたりのレートを全ノードにわたって平均したものです。 したがって 100 MB を送信するマシンの他に 800 MB を送信するマシンがあったとしても、その合計が、無料の上限である **[マシンの台数] x 500 MB** を超えなければ、余分に課金されることはありません。

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>500 MB のデータの 1 日あたりの許容量には、、どの種類のデータが含まれますか?
Defender for Cloud の課金は、Log Analytics の課金と密接に関連しています。 [Microsoft Defender for servers](defender-for-servers-introduction.md) では、[セキュリティ データ タイプ](/azure/azure-monitor/reference/tables/tables-category#security)の次のサブセットに対して、Windowsマシンで 1 日当たり 500 MB/ノードの割り当てが提供されます。
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- SysmonEvent
- ProtectionStatus
- ワークスペースで Update Management ソリューションが実行されていないか、ソリューションのターゲット設定が有効になっている場合は、Update と UpdateSummary のデータの種類

ワークスペースが従来のノードごとの価格レベルである場合、Defender for Cloud と Log Analytics の割り当てが結合されて、取り込まれたすべての課金対象データにまとめて適用されます。

## <a name="next-steps"></a>次の手順
この記事では、Defender for Cloud の価格オプションについて説明しました。 関連資料については、以下を参照してください。

- [Azure ワークロードのコストを最適化する方法](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [ご利用の通貨とリージョンにおける価格の詳細](https://azure.microsoft.com/pricing/details/security-center/)
- ソリューションを特定のエージェント セットに制限することで、コストを管理し、ソリューション用に収集されるデータの量を制限することもできます。 [ソリューションのターゲット設定](../azure-monitor/insights/solution-targeting.md)を使用して、ソリューションにスコープを適用し、ワークスペース内のコンピューターのサブセットを対象にします。 ソリューションのターゲット設定を使用している場合、Defender for Cloud では、ソリューションがないものとしてワークスペースが一覧表示されます。
