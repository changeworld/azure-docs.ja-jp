---
title: クラウドの Defender の新機能のアーカイブ
description: 6か月前以前のクラウドの Defender での新機能と変更点について説明します。
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: reference
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: f88b40cacd6b741c239f271311b0ec132f6f052e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525563"
---
# <a name="archive-for-whats-new-in-defender-for-cloud"></a>クラウドの Defender の新機能のアーカイブ

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

「 [Defender For Cloud の新機能」に](release-notes.md) は、過去6か月間の更新プログラムが含まれていますが、このページには古い項目が含まれています。

このページでは、以下に関する情報を提供します。

- 新機能
- バグの修正
- 非推奨の機能

## <a name="may-2021"></a>2021 年 5 月

5 月の更新プログラムには次のものが含まれます。

- [Azure Defender for DNS および Azure Defender for Resource Manager を一般提供 (GA) としてリリース](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [オープンソースのリレーショナル データベース向け Azure Defender を一般提供 (GA) としてリリース](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [Azure Defender for Resource Manager の新しいアラート](#new-alerts-for-azure-defender-for-resource-manager)
- [GitHub ワークフローおよび Azure Defender を使用したコンテナー イメージの CI/CD 脆弱性スキャン (プレビュー)](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [一部の推奨事項に使用できる Resource Graph クエリを増加](#more-resource-graph-queries-available-for-some-recommendations)
- [SQL データ分類の推奨事項の重要度を変更](#sql-data-classification-recommendation-severity-changed)
- [トラステッド起動機能を有効にするための新しい推奨事項 (プレビュー段階)](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [Kubernetes クラスターを強化するための新しい推奨事項 (プレビュー段階)](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [Assessments API を 2 つの新しいフィールドで拡張](#assessments-api-expanded-with-two-new-fields)
- [資産インベントリでクラウド環境フィルターを取得](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>Azure Defender for DNS および Azure Defender for Resource Manager を一般提供 (GA) としてリリース

これら 2 つのクラウドネイティブの広範な脅威保護計画が GA になりました。

これらの新しい保護により、脅威アクターからの攻撃に対する回復性が強化され、Azure Defender によって保護される Azure リソースの数が大幅に増加します。

- **Azure Defender for Resource Manager** - 組織で実行されるすべてのリソース管理操作を自動的に監視します。 詳細については、次を参照してください。
    - [Azure Defender for Resource Manager の概要](defender-for-resource-manager-introduction.md)
    - [Azure Defender for Resource Manager アラートに対応する](defender-for-resource-manager-usage.md)
    - [Azure Defender for Resource Manager で提供されるアラートの一覧](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender for DNS** - Azure リソースからのすべての DNS クエリを継続的に監視します。 詳細については、次を参照してください。
    - [Azure Defender for DNS の概要](defender-for-dns-introduction.md)
    - [Azure Defender for DNS アラートに対応する](defender-for-dns-usage.md)
    - [Azure Defender for DNS で提供されるアラートの一覧](alerts-reference.md#alerts-dns)

これらの計画を有効にするプロセスを簡略化するには、次の推奨事項を使用します。

- **Azure Defender for Resource Manager を有効にする必要がある**
- **Azure Defender for DNS を有効にする必要がある**

> [!NOTE]
> Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページ https://aka.ms/pricing-security-center を参照してください。


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>オープンソースのリレーショナル データベース向け Azure Defender を一般提供 (GA) としてリリース

Azure Security Center では、オープンソースのリレーショナル データベースに対応するために、新しいバンドルを使用して SQL 保護のオファーを拡張します。

- **Azure SQL データベース サーバー向け Azure Defender** - Azure ネイティブの SQL Server を保護します
- **マシン上の SQL サーバー向け Azure Defender** - ハイブリッド、マルチクラウド、およびオンプレミスの環境で SQL サーバーに対して同様の保護を拡張します
- **オープンソースのリレーショナル データベース向け Azure Defender** - MySQL、PostgreSQL、MariaDB 単一サーバー向けの Azure Defender を防御します。

オープンソースのリレーショナル データベース向け Azure Defender では、サーバーに対するセキュリティ上の脅威を常に監視し、MySQL、PostgreSQL、MariaDB 向けの Azure Defender に対する潜在的な脅威を示す異常なデータベース アクティビティを検出します。 いくつかの例を次に示します。

- **ブルート フォース攻撃の詳細な検出** - オープンソースのリレーショナル データベース向け Azure Defender により、ブルート フォース攻撃の試行および成功について詳細な情報が提供されます。 これにより、調査を行い、お使いの環境に対する攻撃の性質や状態について理解を深め、対処することができます。
- **動作アラートの検出** - オープンソースのリレーショナル データベース向け Azure Defender により、データベースに対するアクセス パターンの変更など、サーバーに対する疑わしいおよび不測の動作が警告されます。
- **脅威インテリジェンスベースの検出** - Azure Defender では、Microsoft の脅威インテリジェンスと膨大なナレッジ ベースを適用して脅威アラートが表示されるため、それらに対処することができます。

詳細については、「[オープンソースのリレーショナル データベース向け Azure Defender の概要](defender-for-databases-introduction.md)」を参照してください。

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>Azure Defender for Resource Manager の新しいアラート

Azure Defender for Resource Manager によって提供される脅威保護を拡張するために、次のアラートを追加しました。

| アラート (アラートの種類)                                                                                                                                                | 説明                                                                                                                                                                                                                                                                                                                                                                                                                              | MITRE の方針 | 重大度 |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**Permissions granted for an RBAC role in an unusual way for your Azure environment (Preview) \(Azure 環境の通常の方法とは異なる方法で RBAC ロールに付与されたアクセス許可 \(プレビュー\)\)**<br>(ARM_AnomalousRBACRoleAssignment)|割り当て時間、任命者の場所、担当者、認証方法、割り当てられたエンティティ、使用されるクライアントソフトウェア、割り当ての範囲が異常であるため、テナント内の同じ任命者によって実行された、または同じ担当者に対して実行された他の割り当てとは異なる RBAC ロール割り当てが、Azure Defender for Resource Manager によって検出されました。 この操作は、組織内の正当なユーザーによって実行された可能性があります。 または、組織内のアカウントが侵害されたこと、および脅威アクターが、自分が所有する追加のユーザー アカウントにアクセス許可を付与しようとしていることを示している場合もあります。|侵入の拡大、防御回避|Medium|
|**Privileged custom role created for your subscription in a suspicious way (Preview) \(疑わしい方法でサブスクリプション用に作成された特権付きカスタム ロール \(プレビュー\)\)**<br>(ARM_PrivilegedRoleDefinitionCreation)|Azure Defender for Resource Manager によって、サブスクリプション内で、疑わしい方法で作成された特権付きカスタム ロールの定義が検出されました。 この操作は、組織内の正当なユーザーによって実行された可能性があります。 または、組織内のアカウントが侵害されたこと、および脅威アクターが、将来検出を回避するために使用する特権ロールを作成しようとしていることを示している場合もあります。|侵入の拡大、防御回避|低|
|**Azure Resource Manager operation from suspicious IP address (Preview) \(疑わしい IP アドレスからの Azure Resource Manager の操作 \(プレビュー\)\)**<br>(ARM_OperationFromSuspiciousIP)|Azure Defender for Resource Manager によって、脅威インテリジェンス フィードで疑わしいとしてマークされている IP アドレスからの操作が検出されました。|実行|Medium|
|**Azure Resource Manager operation from suspicious proxy IP address (Preview) \(疑わしいプロキシ IP アドレスからの Azure Resource Manager の操作 \(プレビュー\)\)**<br>(ARM_OperationFromSuspiciousProxyIP)|Azure Defender for Resource Manager によって、TOR などのプロキシ サービスに関連付けられた IP アドレスからのリソース管理操作が検出されました。 この動作は正当である可能性もありますが、悪意のあるアクティビティで脅威アクターがソース IP を隠そうとするときに多く見られます。|防御回避|Medium|
||||

詳細については、次を参照してください。
- [Azure Defender for Resource Manager の概要](defender-for-resource-manager-introduction.md)
- [Azure Defender for Resource Manager アラートに対応する](defender-for-resource-manager-usage.md)
- [Azure Defender for Resource Manager で提供されるアラートの一覧](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>GitHub ワークフローおよび Azure Defender を使用したコンテナー イメージの CI/CD 脆弱性スキャン (プレビュー)

コンテナー レジストリ向け Azure Defender により、DevSecOps チームは、GitHub アクション ワークフローを監視できます。

Trivy を利用した、コンテナー イメージのための新しい脆弱性スキャン機能により、開発者は、イメージをコンテナー レジストリにプッシュする "*前*" に、コンテナー イメージ内の一般的な脆弱性をスキャンできます。

コンテナー スキャン レポートは Azure Security Center に要約されるため、セキュリティ チームは、脆弱なコンテナーイメージのソースと、それらが生成されたワークフローとリポジトリについて、より詳細な分析情報を入手して理解を深めることができます。

詳細については、「[CI/CD ワークフローで脆弱なコンテナー イメージを特定する](defender-for-container-registries-cicd.md)」を参照してください。

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>一部の推奨事項に使用できる Resource Graph クエリを増加

Security Center のすべての推奨事項には、 **[クエリを開く]** から Azure Resource Graph を使用して、影響を受けるリソースの状態に関する情報を表示するためのオプションがあります。 この強力な機能の詳細については、「[Azure Resource Graph Explorer (ARG) で推奨事項データを確認する](review-security-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg)」を参照してください。

Security Center には、VM、SQL サーバーとそのホスト、コンテナー レジストリでセキュリティ上の脆弱性をスキャンするための脆弱性スキャンが組み込まれています。 結果は、各リソースの種類ごとに個別のすべての結果が単一のビューに収集され、推奨事項として返されます。 推奨事項は次のとおりです。

- Azure Container Registry イメージの脆弱性を修復する必要がある (Qualys を利用)
- 仮想マシンの脆弱性を修復する必要がある
- SQL データベースでは脆弱性の検出結果を解決する必要がある
- マシン上の SQL サーバーでは脆弱性の検出結果を解決する必要がある

この変更に伴い、 **[クエリを開く]** ボタンを使用して、セキュリティの結果を示すクエリを開くこともできます。

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="[クエリを開く] ボタンを使用して、より詳細なクエリを実行し、脆弱性スキャナー関連の推奨事項に関するセキュリティの結果を表示できるようになりました。":::

**[クエリを開く]** ボタンを使用すると、関連するその他の推奨事項に関する追加のオプションを表示できます。

Security Center の脆弱性スキャナーの詳細については、次のページを参照してください。

- [Azure およびハイブリッド マシンに対する Azure Defender の統合された Qualys 脆弱性評価スキャナー](deploy-vulnerability-assessment-vm.md)
- [SQL サーバーに対して Azure Defender の統合された脆弱性評価スキャナーを使用する](defender-for-sql-on-machines-vulnerability-assessment.md)
- [コンテナー レジストリ向け Azure Defender の統合された脆弱性評価スキャナー](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>SQL データ分類の推奨事項の重要度を変更

推奨事項 "**SQL データベースの機密データを分類する必要がある**" の重要度が **高** から **低** に変更されました。

これは、「[SQL データベースで機密データを分類するための推奨事項の強化](upcoming-changes.md#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)」で発表された、この推奨事項に対する継続的な変更の一環として行われたものです。

### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>トラステッド起動機能を有効にするための新しい推奨事項 (プレビュー段階)

Azure からは、[第 2 世代](../virtual-machines/generation-2.md)の VM のセキュリティを向上させるためのシームレスな方法として、トラステッド起動が提供されています。 トラステッド起動により、高度で永続的な攻撃手法から保護されます。 トラステッド起動は、個別に有効にできる、複数の連携するインフラストラクチャ テクノロジで構成されています。 テクノロジごとに、高度な脅威に対する防御の別のレイヤーが提供されます。 詳細については、「[Azure 仮想マシンのトラステッド起動](../virtual-machines/trusted-launch.md)」を参照してください。

> [!IMPORTANT]
> トラステッド起動を使用するには、新しい仮想マシンを作成する必要があります。 最初に作成されたときにトラステッド起動が有効にされていない既存の仮想マシンで、トラステッド起動を有効にすることはできません。
> 
> トラステッド起動は、現在パブリック プレビュー段階にあります。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

Security Center の推奨事項「**vTPM should be enabled on supported virtual machines \(サポートされる仮想マシンで vTPM を有効にする必要がある\)** 」により、Azure VM で vTPM が確実に使用されます。 この仮想化バージョンのハードウェア トラステッド プラットフォーム モジュールでは、VM のブート チェーン全体 (UEFI、OS、システム、ドライバー) を測定することにより、構成証明を有効にします。

vTPM を有効にすると、**Guest Attestation 拡張機能** により、セキュア ブートをリモートで検証できます。 次の推奨事項により、この拡張機能が確実にデプロイされます。

- **セキュア ブートを、サポートしている Windows 仮想マシンで有効にする必要があります**
- **Guest Attestation 拡張機能を、サポートしている Windows 仮想マシンにインストールする必要があります**
- **サポートされている Windows 仮想マシンのスケール セットに Guest Attestation 拡張機能をインストールする必要がある**
- **Guest Attestation 拡張機能を、サポートしている Linux 仮想マシンにインストールする必要があります**
- **サポートされている Linux 仮想マシンのスケール セットに Guest Attestation 拡張機能をインストールする必要がある**

詳細については、「[Azure 仮想マシンのトラステッド起動](../virtual-machines/trusted-launch.md)」を参照してください。 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>Kubernetes クラスターを強化するための新しい推奨事項 (プレビュー段階)

次の推奨事項を使用すると、Kubernetes クラスターをさらに強化できます。

- **Kubernetes クラスターで既定の名前空間を使用しない** - ConfigMap、Pod、Secret、Service、ServiceAccount という種類のリソースを無許可アクセスから保護するために、Kubernetes クラスターで既定の名前空間を使用しないようにします。
- **Kubernetes クラスターでは、API 資格情報の自動マウントを無効にする必要がある** - 侵害されたおそれがある Pod リソースにより、Kubernetes クラスターに対して API コマンドが実行されるのを防ぐには、API 資格情報の自動マウントを無効にします。
- **Kubernetes クラスターでは、CAPSYSADMIN セキュリティ機能を許可しない**

お使いのコンテナー化された環境を Security Center で保護する方法については、「[Security Center のコンテナーのセキュリティ](container-security.md)」を参照してください。

### <a name="assessments-api-expanded-with-two-new-fields"></a>Assessments API を 2 つの新しいフィールドで拡張

[Assessments REST API](/rest/api/securitycenter/assessments) に次の 2 つのフィールドを追加しました。

- **FirstEvaluationDate** - 推奨事項が作成されて最初に評価された日時。 ISO 8601 形式の UTC 時刻として返されます。
- **StatusChangeDate** –推奨事項の状態が最後に変更された日時。 ISO 8601 形式の UTC 時刻として返されます。

これらのフィールドに使用される初期の既定値は、すべての推奨事項で `2021-03-14T00:00:00+0000000Z` です。

この情報には、次の表に示したいずれかの方法でアクセスできます。

| ツール                 | 詳細                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API の呼び出し        | GET https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| 連続エクスポート    | Log Analytics ワークスペース データで、2 つの専用フィールドが利用できるようになります                                                                                            |
| [CSV エクスポート](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | CSV ファイルには、2 つのフィールドが含まれています                                                        |
|                      |                                                                                                                                                                        |


[Assessments REST API](/rest/api/securitycenter/assessments) の詳細をご覧ください。


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>資産インベントリでクラウド環境フィルターを取得

Security Center の資産インベントリのページには、表示されるリソースの一覧をすばやく絞り込むために多数のフィルターが用意されています。 詳細については、「[資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)」を参照してください。

新しいフィルターには、Security Center のマルチクラウド機能と接続しているクラウド アカウントに応じて、一覧を絞り込むためのオプションが用意されています。

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="インベントリの環境フィルター":::

マルチクラウド機能の詳細については、次のページを参照してください。

- [Azure Security Center への AWS アカウントの接続](quickstart-onboard-aws.md)
- [Azure Security Center への GCP アカウントの接続](quickstart-onboard-gcp.md)


## <a name="april-2021"></a>2021 年 4 月

4 月の更新プログラムには次のものが含まれます。
- [更新された [リソース正常性] ページ (プレビュー)](#refreshed-resource-health-page-in-preview)
- [最近プルされたコンテナー レジストリ イメージを毎週再スキャン (一般提供 (GA) リリース)](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護 (プレビュー)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [Azure Defender と Microsoft Defender for Endpoint の統合で、Windows Server 2019 および Windows 10 Virtual Desktop (WVD) をサポート (一般提供 (GA) リリース)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [Azure Defender for DNS および Azure Defender for Resource Manager を有効にする際の推奨事項 (プレビュー)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [追加された 3 つの規制コンプライアンス標準: Azure CIS 1.3.0、CMMC レベル 3、New Zealand ISM Restricted](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [ゲスト構成に関連する 4 つの新しい推奨事項 (プレビュー)](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [CMK の推奨事項をベスト プラクティスのセキュリティ コントロールに移動](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 個の Azure Defender アラートを非推奨化](#11-azure-defender-alerts-deprecated)
- ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [[Azure Defender for SQL on machine] タイルを Azure Defender ダッシュボードから削除](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [21 個の推奨事項をセキュリティ コントロール間で移動](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>更新された [リソース正常性] ページ (プレビュー)

Security Center のリソース正常性が拡張、強化、改善され、1 つのリソースの全体的な正常性のスナップショット ビューが提供されるようになりました。 

リソースに関する詳細情報と、そのリソースに適用されるすべての推奨事項を確認できます。 また、 [Microsoft Defender の高度な保護プラン](defender-for-cloud-introduction.md)を使用している場合は、その特定のリソースの未処理のセキュリティアラートも表示されます。

リソースの [リソース正常性] ページを開くには、[資産インベントリ ページ](asset-inventory.md)でリソースを選択します。

Security Center のポータル ページにあるこのプレビュー ページには、次の情報が表示されます。

1. **リソース情報** - リソースが属するリソース グループとサブスクリプション、地理的な場所など。
1. **適用されているセキュリティ機能** - そのリソースに対して Azure Defender が有効になっているかどうか。
1. **未処理の推奨事項とアラートの数** - 未処理のセキュリティに関する推奨事項と Azure Defender アラートの数。
1. **実行可能な推奨事項とアラート** - リソースに適用される推奨事項とアラートが 2 つのタブに一覧表示されます。

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="仮想マシンの正常性情報を示す Azure Security Center の [リソース正常性] ページ":::

詳細については、「[チュートリアル: リソースの正常性を調査する](investigate-resource-health.md)」をご覧ください。


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>最近プルされたコンテナー レジストリ イメージを毎週再スキャン (一般提供 (GA) リリース)

コンテナー レジストリ用 Azure Defender には、組み込みの脆弱性スキャナーが含まれています。 このスキャナーは、レジストリにプッシュされたイメージと、過去 30 日以内にプルされたすべてのイメージを即座にスキャンします。

新しい脆弱性は毎日検出されます。 この更新により、過去 30 日間にレジストリからプルされたコンテナー イメージが毎週 **再スキャン** されます。 これにより、新しく検出された脆弱性をイメージ内で確実に識別できます。

スキャンはイメージごとに課金されるため、これらの再スキャンに対する追加料金は発生しません。

このスキャナーの詳細については、「[コンテナー レジストリ用の Azure Defender を使用してイメージの脆弱性をスキャンする](defender-for-container-registries-usage.md)」を参照してください。


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護 (プレビュー)

Azure Defender for Kubernetes は、クラスターがどこにデプロイされていても防御できるように、脅威保護機能が拡張されています。 これは、[Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) とその新しい[拡張機能](../azure-arc/kubernetes/extensions.md)を統合することによって有効になりました。 

非 Azure Kubernetes クラスターで Azure Arc を有効にすると、Azure Security Center の新しい推奨事項として、わずか数回のクリックで Azure Defender 拡張機能をデプロイすることが提案されます。

推奨事項 (**Azure Arc 対応 Kubernetes クラスターには、Azure Defender の拡張機能がインストールされている必要がある**) と拡張機能を使用して、他のクラウド プロバイダーにデプロイされている (ただし、そのマネージド Kubernetes サービス上にはない) Kubernetes クラスターを保護します。

この Azure Security Center、Azure Defender、Azure Arc 対応 Kubernetes の間の統合によって、次のことが可能になります。

- 保護されていない Azure Arc 対応 Kubernetes クラスターへの Azure Defender 拡張機能の容易な (手動で、かつ大規模な) プロビジョニング
- Azure Arc ポータルから Azure Defender 拡張機能とそのプロビジョニングの状態を監視する
- Security Center からのセキュリティに関する推奨事項が、Azure Arc ポータルの新しい [セキュリティ] ページでレポートされる
- Azure Defender で特定されたセキュリティ上の脅威が、Azure Arc ポータルの新しい [セキュリティ] ページでレポートされる
- Azure Arc 対応 Kubernetes クラスターの Azure Security Center プラットフォームおよびエクスペリエンスへの統合

詳細については、[オンプレミスおよびマルチクラウドの Kubernetes クラスターでの Azure Defender for Kubernetes の使用](defender-for-kubernetes-azure-arc.md)に関する記事をご覧ください。

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 対応 Kubernetes クラスター用の Azure Defender 拡張機能をデプロイするための Azure Security Center の推奨事項。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>Azure Defender と Microsoft Defender for Endpoint の統合で、Windows Server 2019 および Windows 10 Virtual Desktop (WVD) をサポート (一般提供 (GA) リリース)

Microsoft Defender for Endpoint は、クラウドで提供される包括的なエンドポイント セキュリティ ソリューションです。 リスクベースによる脆弱性の管理と評価、およびエンドポイントでの検出と対応 (EDR) を提供します。 Defender for Endpoint を Azure Security Center と併用するメリットの全容については、「[Security Center に統合された EDR ソリューション Microsoft Defender for Endpoint でエンドポイントを保護する](integration-defender-for-endpoint.md)」を参照してください。

Windows サーバー上のサーバーに Azure Defender を有効にした場合、プランに Defender for Endpoint のライセンスが含まれています。 サーバーに対して既に Azure Defender を有効にしており、サブスクリプションに Windows 2019 サーバーが存在する場合には、今回の更新により自動的に Defender for Endpoint が追加されます。 特に何か操作をする必要はありません。 

このたびサポートが拡張され、Windows Server 2019 と [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) が含まれるようになりました。

> [!NOTE]
> Windows Server 2019 のマシンで Defender for Endpoint を有効にする場合には、マシンが「[Microsoft Defender for Endpoint 統合を有効にする](integration-defender-for-endpoint.md#enable-the-microsoft-defender-for-endpoint-integration)」に記載されている前提条件を満たしていることを確認してください。


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>Azure Defender for DNS および Azure Defender for Resource Manager を有効にする際の推奨事項 (プレビュー)

[Azure defender For Resource Manager](defender-for-resource-manager-introduction.md) と [Azure Defender for DNS](defender-for-dns-introduction.md) を有効にするプロセスを簡略化するために、2 つの新しい推奨事項が追加されました。

- **Azure Defender for Resource Manager を有効にする必要がある** - Defender for Resource Manager は組織内のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。
- **Azure Defender for DNS を有効にする必要がある** - Defender for DNS では、Azure リソースからのすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。

Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページ https://aka.ms/pricing-security-center を参照してください。

> [!TIP]
> プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](implement-security-recommendations.md)」を参照してください。


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>追加された 3 つの規制コンプライアンス標準: Azure CIS 1.3.0、CMMC レベル 3、New Zealand ISM Restricted

Azure Security Center で使用するための 3 つの標準が追加されました。 規制コンプライアンス ダッシュボードを使用して、次の標準への準拠を追跡できるようになりました。

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC レベル 3](../governance/policy/samples/cmmc-l3.md)
- [New Zealand ISM Restricted](../governance/policy/samples/new-zealand-ism.md)

「[規制コンプライアンス ダッシュボードで標準セットをカスタイマイズする](update-regulatory-compliance-packages.md)」で説明するように、これらをサブスクリプションに割り当てることができます。

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Azure Security Center の規制コンプライアンス ダッシュボードで使用するために追加された 3 つの標準。" lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

詳細情報:
- [規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ](update-regulatory-compliance-packages.md)
- [チュートリアル:規制に対するコンプライアンスの向上](regulatory-compliance-dashboard.md)
- [FAQ - 規制コンプライアンス ダッシュボード](regulatory-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>ゲスト構成に関連する 4 つの新しい推奨事項 (プレビュー)

Azure の[ゲスト構成拡張機能](../governance/policy/concepts/guest-configuration.md)により、仮想マシンのゲスト内設定の確実な強化を支援するように Security Center に対してレポートが行われます。 この拡張機能は Arc 接続マシン エージェントに含まれているため、Arc 対応サーバーには必要ありません。 この拡張機能を使用するには、マシンにシステムマネージド ID が必要です。

この拡張機能を最大限に活用するために、Security Center に 4 つの新しい推奨事項が追加されました。

- 2 つの推奨事項では、拡張機能と、その必須のシステムマネージド ID をインストールすることを求めています。
    - **ゲスト構成拡張機能をマシンにインストールする必要がある**
    - **仮想マシンのゲスト構成拡張機能はシステム割り当てマネージド ID を使用してデプロイする必要がある**

- 拡張機能がインストールされて実行されると、マシンの監査が開始され、オペレーティング システムの構成や環境設定などの設定を強化するように求められます。 これらの 2 つの推奨事項に従って、Windows および Linux マシンを強化するように求められます。
    - **マシンで Windows Defender Exploit Guard を有効にする必要がある**
    - **Linux マシンに対する認証では SSH キーを要求する必要がある**

詳細については、「[Azure Policy のゲストの構成の理解](../governance/policy/concepts/guest-configuration.md)」を参照してください。

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK の推奨事項をベスト プラクティスのセキュリティ コントロールに移動

すべての組織のセキュリティ プログラムには、データ暗号化要件が含まれています。 既定では、Azure のお客様のデータは、サービス マネージド キーを使用して保存時に暗号化されます。 ただし、規制コンプライアンス基準を満たすためには、一般にカスタマー マネージド キー (CMK) が必要です。 CMK を使用すると、自分が作成して所有する [Azure Key Vault](../key-vault/general/overview.md) キーを使用してデータを暗号化できます。 これにより、ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。

Azure Security Center のセキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 コントロールごとに、すべてのリソースについて、コントロールに示されているすべての推奨事項を修復した場合にセキュリティ スコアに追加できる最大ポイント数があります。 **[セキュリティのベスト プラクティスの実装]** セキュリティ コントロールは、0 ポイントです。 したがって、このコントロールの推奨事項は、セキュリティ スコアに影響しません。

次に示す推奨事項は、オプションとしての性質をより適切に反映するために、 **[セキュリティのベスト プラクティスの実装]** セキュリティ コントロールに移動されています。 この移動によって、これらの推奨事項は、その目標を達成するために最も適したコントロールに含められます。

- Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- Cognitive Services アカウントでカスタマー マネージド キー (CMK) によるデータ暗号化を有効にする必要がある
- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- ストレージ アカウントでは暗号化にカスタマー マネージド キー (CMK) を使用する必要がある

各セキュリティ コントロールに含まれる推奨事項については、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。


### <a name="11-azure-defender-alerts-deprecated"></a>11 個の Azure Defender アラートを非推奨化

次に示す 11 個の Azure Defender アラートは非推奨となりました。

- 次の 2 つのアラートは、新しいアラートで置き換えられ、より適切なカバレッジが提供されます。

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW - MicroBurst toolkit "Get-AzureDomainInfo" function run detected (プレビュー - MicroBurst ツールキット "Get-AzureDomainInfo" 関数の実行が検出されました) |
    | ARM_MicroBurstRunbook    | PREVIEW - MicroBurst toolkit "Get-AzurePasswords" function run detected (プレビュー - MicroBurst ツールキット "Get-AzurePasswords" 関数の実行が検出されました)  |
    |                          |                                                                          |

- 次の 9 つのアラートは、既に非推奨になっている Azure Active Directory Identity Protection コネクタ (IPC) に関連したものです。

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | 通常とは異なるサインイン プロパティ |
    | AnonymousLogin      | 匿名 IP アドレス          |
    | InfectedDeviceLogin | マルウェアにリンクした IP アドレス     |
    | ImpossibleTravel    | 特殊な移動               |
    | MaliciousIP         | 悪意のある IP アドレス          |
    | LeakedCredentials   | 漏洩した資格情報            |
    | PasswordSpray       | パスワード スプレー                |
    | LeakedCredentials   | Azure AD 脅威インテリジェンス  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > これらの 9 つの IPC アラートは、Security Center アラートではありませんでした。 これらは Azure Active Directory (AAD) Identity Protection コネクタ (IPC) の一部であり、IPC によって Security Center に送信されていました。 過去 2 年間に、これらのアラートが表示されたお客様は、2019 年以前に (コネクタから ASC への) エクスポートを構成した組織だけです。 AAD IPC は引き続き独自のアラート システムに表示されており、今後も Azure Sentinel で利用できます。 唯一の変更点は、Security Center に表示されなくなったことです。

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化 

次の 2 つの推奨事項は非推奨とされ、この変更によってセキュリティ スコアにわずかな影響が生じる可能性があります。

- **システムの更新プログラムを適用するには、マシンを再起動する必要があります**
- **お使いのマシンに監視エージェントをインストールする必要があります**。 この推奨事項は、オンプレミスのマシンにのみ関係します。また、そのロジックのいくつかは、別の推奨事項 (**お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある**) に移される予定です

連続エクスポートとワークフロー自動化の構成をチェックして、該当する推奨事項が含まれているかどうかを確認することをお勧めします。 また、ダッシュボードまたはそれらを使用する他の監視ツールを適宜更新する必要があります。

これらの推奨事項の詳細については、[セキュリティに関する推奨事項のリファレンス ページ](recommendations-reference.md)を参照してください。

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>[Azure Defender for SQL on machine] タイルを Azure Defender ダッシュボードから削除

Azure Defender ダッシュボードの適用範囲には、お使いの環境用の関連 Azure Defender プランのタイルが含まれています。 保護されているリソースと保護されていないリソースの数のレポートに関する問題が発生したため、問題が解決されるまでは、**Azure Defender for SQL on machine** のリソース適用範囲の状態を一時的に削除することにしました。


### <a name="21-recommendations-moved-between-security-controls"></a>21 個の推奨事項をセキュリティ コントロール間で移動 

次の推奨事項は、別のセキュリティ コントロールに移動されました。 セキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 この移動によって、これらの各推奨事項は、その目標を達成するために最も適したコントロールに含められます。

各セキュリティ コントロールに含まれる推奨事項については、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。

|推奨 |変更と影響  |
|---------|---------|
|脆弱性評価を SQL サーバー上で有効にする必要がある<br>脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある<br>SQL データベースの脆弱性を新たに修復する必要がある<br>VM 内の SQL データベースの脆弱性を修復する必要があります     |脆弱性の修復 (6 ポイント分) から<br>セキュリティ構成の修復 (4 ポイント分) に移動されます。<br>お客様の環境によっては、これらの推奨事項がスコアに与える影響が少なくなります。|
|複数の所有者がサブスクリプションに割り当てられている必要がある<br>Automation アカウント変数は、暗号化する必要がある<br> IoT デバイス - Auditd プロセスでイベントの送信が停止された<br> IoT デバイス - オペレーティング システムのベースラインの検証に失敗した<br> IoT デバイス - TLS 暗号スイートのアップグレードが必要<br> IoT デバイス - デバイス上でポートを開く<br> IoT デバイス - チェーンのうちの 1 つに制限の緩すぎるファイアウォール ポリシーが見つかりました<br> IoT デバイス - 入力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました<br> IoT デバイス - 出力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました<br>IoT Hub の診断ログを有効にする必要がある<br> IoT デバイス - エージェントで低使用率のメッセージが送信されている<br>IoT デバイス - 既定の IP フィルター ポリシーを拒否にする必要がある<br>IoT デバイス - IP フィルター ルールの IP 範囲が広い<br>IoT デバイス - エージェントのメッセージ間隔とサイズを調整する必要がある<br>IoT デバイス - 認証の資格情報が同一<br>IoT デバイス - 監査対象プロセスでイベントの送信が停止された<br>IoT デバイス - オペレーティング システム (OS) のベースライン構成を修正する必要がある|**セキュリティのベスト プラクティスの実装** に移動されます。<br>推奨事項が「セキュリティのベスト プラクティスの実装」セキュリティ コントロールに移動される (ポイントは発生しない) と、この推奨事項はセキュリティ スコアに影響を及ぼさなくなります。|
|||


## <a name="march-2021"></a>2021 年 3 月

3 月の更新プログラムには次のものが含まれます。

- [Azure Firewall の管理を Security Center に統合](#azure-firewall-management-integrated-into-security-center)
- [SQL 脆弱性評価に "ルールの無効化" エクスペリエンスを追加 (プレビュー)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor ブックを Security Center に統合し、テンプレートを 3 件提供](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [規制コンプライアンス ダッシュボードに Azure 監査レポートを追加 (プレビュー)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [[Explore in ARG]\(ARG で探索\) を使用した Azure Resource Graph での推奨データの表示](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [ワークフローの自動化をデプロイするためのポリシーを更新](#updates-to-the-policies-for-deploying-workflow-automation)
- [従来の 2 つの推奨事項で Azure アクティビティ ログにデータが直接書き込まれなくなる](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [推奨事項ページの拡充](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Azure Firewall の管理を Security Center に統合

Azure Security Center を開いたときに、最初に表示されるページが概要ページです。 

この対話式のダッシュボードでは、ハイブリッド クラウド ワークロードのセキュリティ対策について一元的なビューが提供されます。 また、セキュリティ アラート、カバレッジ情報なども表示されます。

このたび、セキュリティの状態を一元的に確認できるようにするための取り組みの一環として、このダッシュボードに Azure Firewall Manager を統合しました。 全ネットワークをまたいでファイアウォールのカバレッジ状態を確認できるほか、Security Center から Azure Firewall のポリシーを一元管理できるようになりました。

このダッシュボードの詳細については [Azure Security Center の概要ページ](overview-page.md)を参照してください。

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Security Center の概要ダッシュボードに Azure Firewall のタイルが表示されているところ":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL 脆弱性評価に "ルールの無効化" エクスペリエンスを追加 (プレビュー)

Security Center には、データベースの脆弱性となりうる問題を検出、追跡、および修復するうえで役立つ脆弱性スキャナーが組み込まれています。 評価スキャンの結果では、SQL マシンのセキュリティ状態の概要と、セキュリティに関する検出結果の詳細が示されます。

組織のニーズとして、検出結果を修復するのではなく無視する必要がある場合は、必要に応じて検出結果を無効にできます。 無効化された検出結果は、セキュリティ スコアに影響を与えたり、不要なノイズを生成したりすることはありません。

詳細については、「[特定の検出結果を無効にする](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)」を参照してください。



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor ブックを Security Center に統合し、テンプレートを 3 件提供

Ignite Spring 2021 において、Security Center に Azure Monitor ブックを統合したエクスペリエンスを発表しました。

この新しい統合を使用すると、Security Center のギャラリーから面倒な設定のいらないテンプレートを使えるようになります。 ブック テンプレートを使用すると、組織のセキュリティ体制を追跡するための動的なビジュアル レポートを閲覧したり、作成したりできます。 また、Security Center のデータをはじめとするサポートされているデータ型に基づいて新しいブックを作成したり、Security Center の GitHub コミュニティから入手したコミュニティ ワークブックをすばやくデプロイしたりすることもできます。

用意されているテンプレート レポートは次の 3 つです。

- **セキュリティ スコアの推移** - サブスクリプションのスコアと、リソースに対する推奨事項の変化を追跡できます
- **システムの更新プログラム** - リソース、OS、重要度などに応じて、システムに適用できていない更新プログラムを表示します
- **脆弱性評価の検出結果** - Azure リソースを対象とした脆弱性スキャンの検出結果を表示します

ここに挙げたレポートを使用する方法や、独自のレポートを作成する方法の詳細については、「[豊富な機能を備えた対話型の Security Center データ レポートを作成する](custom-dashboards-azure-workbooks.md)」を参照してください。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="経過時間に応じたセキュア スコア レポート。":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>規制コンプライアンス ダッシュボードに Azure 監査レポートを追加 (プレビュー)

規制コンプライアンス ダッシュボードのツール バーから、Azure と Dynamics の証明書レポートをダウンロードできるようになりました。 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="規制コンプライアンス ダッシュボードのツール バー":::

関連するレポートの種類 (PCI、SOC、ISO など) のタブを選択し、フィルターを使用すると、必要なレポートを見つけることができます。

詳細は、「[規制コンプライアンス ダッシュボードでの標準の管理](update-regulatory-compliance-packages.md)」を参照してください。

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="利用可能な Azure 監査レポートの一覧にフィルターを適用する。":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>[Explore in ARG]\(ARG で探索\) を使用した Azure Resource Graph での推奨データの表示

推奨事項の詳細ページに、[Explore in ARG]\(ARG で探索\) ツールバー ボタンが含まれるようになりました。 このボタンを使用して、Azure Resource Graph のクエリを開き、推奨データを探索、エクスポート、および共有します。

Azure Resource Graph (ARG) を使用すると、堅牢なフィルター処理、グループ化、および並べ替え機能を使用して、クラウド環境全体のリソース情報にすばやくアクセスできます。 これは、Azure サブスクリプション全体の情報を、プログラムから、または Azure portal 内ですばやく効率的に照会する方法です。

[Azure Resource Graph (ARG)](../governance/resource-graph/index.yml) の詳細について参照してください。

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Azure Resource Graph で推奨データを探索する。":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>ワークフローの自動化をデプロイするためのポリシーを更新

組織の監視とインシデント対応プロセスを自動化すると、セキュリティ インシデントの調査と軽減にかかる時間を大幅に短縮できます。

Microsoft では、ワークフローの自動化のプロシージャを作成および構成し、組織全体にデプロイするための Azure Policy "DeployIfNotExist" ポリシーを 3 つ提供しています。

|目標  |ポリシー  |ポリシー ID  |
|---------|---------|---------|
|セキュリティ アラートのワークフローの自動化|[Azure Security Center アラートに対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|セキュリティに関する推奨事項のワークフローの自動化|[Azure Security Center 推奨事項に対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|規制コンプライアンスの変化に関するワークフローの自動化|[Azure Security Center の法令遵守のためにワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

ここに挙げたポリシーの機能について、2 点更新を実施しました。

- 割り当てると適用され、そのまま有効な状態が維持されます。
- 既にデプロイされている場合でも、ポリシーをカスタマイズしたり、パラメーターを更新したりできるようになりました。 たとえば、別の評価キーを追加したり、既存の評価キーを編集したりする必要が生じた場合には、そのような操作を行うことができます。

[ワークフローの自動化テンプレート](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)を使用して開始するには、こちらを参照してください。

方法の詳細については、「[Security Center のトリガーへの応答を自動化する](workflow-automation.md)」を参照してください。


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>従来の 2 つの推奨事項で Azure アクティビティ ログにデータが直接書き込まれなくなる 

Security Center によって、ほぼすべてのセキュリティに関する推奨事項のデータが Azure Advisor に渡され、そこから [Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)に書き込まれます。

2 つの推奨事項については、データは同時に Azure アクティビティ ログに直接書き込まれます。 この変更により、Security Center によって、これらの従来のセキュリティに関する推奨事項のデータがアクティビティ ログに直接書き込まれなくなります。 代わりに、他のすべての推奨事項と同様に、データは Azure Advisor にエクスポートされます。

この 2 つの従来の推奨事項は次のとおりです。
-  - お使いのマシンで Endpoint Protection の正常性の問題を解決する必要があります
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある

アクティビティ ログの "TaskDiscovery タイプの推奨事項" カテゴリでこの 2 つの推奨事項の情報にアクセスしていた場合、これは使用できなくなります。


### <a name="recommendations-page-enhancements"></a>推奨事項ページの拡充 

推奨事項一覧の改良版がリリースされ、一目で詳細情報を確認できるようになりました。

これにより、ページには以下が表示されます。

1. 各セキュリティ コントロールの最大スコアと現在のスコア。
1. **修正** や **プレビュー** などのタグに代わるアイコン。
1. 各推奨事項に関連する[ポリシー イニシアチブ](security-policy-concept.md)を示す新しい列。これは、"コントロールでグループ化" が無効になっている場合に表示されます。

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Azure Security Center の推奨事項ページの機能強化 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Azure Security Center の推奨事項の &quot;フラット&quot; リストの機能強化 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

詳細については、[Azure Security Center でのセキュリティに関する推奨事項](review-security-recommendations.md)を参照してください。

## <a name="february-2021"></a>2021 年 2 月

2 月の更新プログラムには次のものが含まれます。

- [一般提供 (GA) 用にリリースされた Azure portal の新しいセキュリティ アラート ページ](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Kubernetes ワークロード保護の推奨事項が一般提供 (GA) リリース](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Azure Defender に統合した Microsoft Defender for Endpoint が、Windows Server 2019 および Windows 10 Virtual Desktop (WVD) をサポート (プレビュー)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [[推奨事項の詳細] ページからポリシーへの直接リンク](#direct-link-to-policy-from-recommendation-details-page)
- [SQL データ分類の推奨事項がセキュリティ スコアに影響しなくなった](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [規制コンプライアンス評価の変更によって、ワークフロー自動化をトリガー可能 (プレビュー)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [資産インベントリ ページの拡充](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>一般提供 (GA) 用にリリースされた Azure portal の新しいセキュリティ アラート ページ

Azure Security Center のセキュリティ アラート ページは、次の機能を提供できるように再設計されました。

- **アラートのトリアージ エクスペリエンスの向上** - アラートに対応する労力を減らし、最も関連性の高い脅威に焦点を絞りやすくするために、一覧にはカスタマイズ可能なフィルターとグループ化のオプションが追加されました。
- **アラート一覧への情報の追加** - MITRE ATT & ACK の戦術など。
- **サンプル アラートを作成するボタン** - Azure Defender の機能を評価し、アラートの 構成 (SIEM の統合、メール通知、ワークフローの自動化) をテストするために、すべての Azure Defender のプランからサンプル アラートを作成できます。
- **Azure Sentinel のインシデント エクスペリエンスとの連携** - 両方の製品を使用するお客様は、より簡単に 2 つを切り替えることによって、どちらの製品からも、もう一方の製品が提供する情報を取得しやすくなりました。
- 長大なアラート一覧の **パフォーマンス向上**
- アラート一覧の **キーボードによるナビゲーション**
- **Azure Resource Graph のアラート** - Azure Resource Graph (すべてのリソースを対象とする、Kusto に似た API) のアラートにクエリを実行できます。 これは、独自のアラート ダッシュボードを作成している場合にも役立ちます。 [Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/index.yml)。
- **サンプルのアラート機能の作成** - 新しいアラート エクスペリエンスでサンプル アラートを作成するには、「[Azure Defender アラートのサンプルを生成する](alert-validation.md#generate-sample-security-alerts)」を参照してください。

:::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center のセキュリティ アラートの一覧":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes ワークロード保護の推奨事項が一般提供 (GA) リリース

Kubernetes ワークロード保護に関する一連の推奨事項が一般提供 (GA) されたことをお知らせします。

Security Center では、Kubernetes ワークロードが既定で確実に保護されるように、Kubernetes 受付制御を含む適用オプションなど、Kubernetes レベルの強化の推奨事項が追加されました。

Azure Kubernetes Service (AKS) クラスターに Kubernetes 用 Azure Policy アドオンをインストールすると、Kubernetes API サーバーに対するすべての要求が、クラスターに保存される前に、事前定義された一連のベスト プラクティス (13 個のセキュリティ推奨事項として表示されます) を基準にして監視されます。 その後、ベスト プラクティスを適用し、それを将来のワークロードに対して要求するように構成できます。

たとえば、特権コンテナーが作成されないように要求することができます。また、今後の特権コンテナー作成要求はすべてブロックされます。

詳細については、[Kubernetes 受付制御を使用したワークロード保護のベスト プラクティス](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)に関するページを参照してください。

> [!NOTE]
> 推奨事項がプレビュー段階の間、これらによって AKS クラスター リソースが異常な状態にあると表示されることはありませんでした。これらの推奨事項は、セキュリティ スコアの計算には含まれていませんでした。 このたびの GA の発表により、これらがスコア計算に含まれるようになります。 その修復がまだ済んでいない場合、これが原因でセキュア スコアに若干の影響が生じる可能性があります。 「[Azure Security Center の修復レコメンデーション](implement-security-recommendations.md)」の説明に従って、可能な限りそれらを修復してください。


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Azure Defender に統合した Microsoft Defender for Endpoint が、Windows Server 2019 および Windows 10 Virtual Desktop (WVD) をサポート (プレビュー)

Microsoft Defender for Endpoint は、クラウドで提供される包括的なエンドポイント セキュリティ ソリューションです。 リスクベースによる脆弱性の管理と評価、およびエンドポイントでの検出と対応 (EDR) を提供します。 Defender for Endpoint を Azure Security Center と併用するメリットの全容については、「[Security Center に統合された EDR ソリューション Microsoft Defender for Endpoint でエンドポイントを保護する](integration-defender-for-endpoint.md)」を参照してください。

Windows サーバー上のサーバーに Azure Defender を有効にした場合、プランに Defender for Endpoint のライセンスが含まれています。 サーバーに対して既に Azure Defender を有効にしており、サブスクリプションに Windows 2019 サーバーが存在する場合には、今回の更新により自動的に Defender for Endpoint が追加されます。 特に何か操作をする必要はありません。 

このたびサポートが拡張され、Windows Server 2019 と [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) が含まれるようになりました。

> [!NOTE]
> Windows Server 2019 のマシンで Defender for Endpoint を有効にする場合には、マシンが「[Microsoft Defender for Endpoint 統合を有効にする](integration-defender-for-endpoint.md#enable-the-microsoft-defender-for-endpoint-integration)」に記載されている前提条件を満たしていることを確認してください。

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>[推奨事項の詳細] ページからポリシーへの直接リンク

推奨事項の詳細を確認しているときに、基になるポリシーを参照できると有益な場合が多くあります。 ポリシーによってサポートされているすべての推奨事項について、[推奨事項の詳細] ページに新しいリンクが追加されました。

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="推奨事項をサポートする特定のポリシーが記載されている Azure Policy ページへのリンク。":::

ポリシー定義を表示し、評価ロジックを確認するには、このリンクを使用します。 

[セキュリティの推奨事項に関するリファレンス ガイド](recommendations-reference.md)に記載されている推奨事項の一覧を確認している場合も、ポリシー定義のページへのリンクが表示されます。

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Azure Security Center 推奨事項のリファレンス ページから、特定のポリシーに関する Azure Policy ページへの直接アクセス。" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>SQL データ分類の推奨事項がセキュリティ スコアに影響しなくなった
**SQL データベースの機密データを分類する必要がある** という推奨事項が、セキュリティ スコアに影響することはなくなりました。 これは、**データ分類の適用** セキュリティ コントロールの唯一の推奨事項であり、このコントロールのセキュリティ スコアの値は 0 になりました。

Security Center のセキュリティ コントロールとそのスコアの一覧、およびそれぞれに対する推奨事項の一覧については、[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)に関するページを参照してください。


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>規制コンプライアンス評価の変更によって、ワークフロー自動化をトリガー可能 (プレビュー)
ワークフロー自動化のトリガー オプションに、規制コンプライアンス評価の変更という 3 つ目のデータの種類が追加されました。

ワークフローの自動化ツールの使い方については、「[Security Center のトリガーへの応答を自動化する](workflow-automation.md)」を参照してください。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="規制コンプライアンス評価の変更を使用した、ワークフロー自動化のトリガー。" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>資産インベントリ ページの拡充
Security Center の [資産インベントリ] ページに次の改良が加えられています。

- ページの上部にあるサマリーに **[Unregistered subscriptions]\(未登録のサブスクリプション\)** が追加され、Security Center が有効になっていないサブスクリプションの数を把握できるようになりました。

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="[資産インベントリ] ページ上部のサマリーに表示される未登録のサブスクリプション数。":::

- フィルターが拡張され、次の項目が追加されました。
    - **カウント** - 各カテゴリの条件を満たすリソースの数がフィルターごとに表示されます。

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Azure Security Center の [資産インベントリ] ページのフィルターに表示されるカウント。":::

    - **Contains exemptions (適用除外を含む) フィルター** (省略可) - 適用除外が設定されている (または設定されていない) リソースに結果を絞り込みます。 このフィルターは既定では表示されませんが、 **[フィルターの追加]** ボタンで追加できます。

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Azure Security Center の [資産インベントリ] ページに &quot;Contains Exemption (適用除外を含む)&quot; フィルターを追加する":::

詳細については、「[資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)」を参照してください。



## <a name="january-2021"></a>2021 年 1 月

1 月の更新プログラムには次のものが含まれます。

- [Azure セキュリティ ベンチマークが Azure Security Center の既定のポリシー イニシアティブに](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [オンプレミスおよびマルチクラウド マシンの脆弱性評価が一般提供 (GA) リリース](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [管理グループのセキュア スコア (プレビュー) が利用可能に](#secure-score-for-management-groups-is-now-available-in-preview)
- [Secure Scores API が一般提供 (GA) リリース](#secure-score-api-is-released-for-general-availability-ga)
- [Azure Defender for App Service に未解決の DNS 保護を追加](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [マルチクラウド コネクタが一般提供 (GA) リリース](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [サブスクリプションと管理グループのセキュア スコアから推奨事項全体を除外する](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [テナント全体の可視性をユーザーがグローバル管理者にリクエスト可能に](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Azure セキュリティ ベンチマークのカバレッジを広げるために追加される、35 個のプレビュー推奨事項](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [フィルター処理された推奨事項一覧の CSV エクスポート](#csv-export-of-filtered-list-of-recommendations)
- [Azure Policy の評価で "適用除外" であったリソースが "準拠" とレポートされるように](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [セキュア スコアと規制コンプライアンス データの週単位のスナップショットを連続エクスポートでエクスポート (プレビュー)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure セキュリティ ベンチマークが Azure Security Center の既定のポリシー イニシアティブに

Azure セキュリティ ベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。

ここ数か月で、Security Center の組み込みのセキュリティ推奨事項が大幅に拡充され、このベンチマークのカバー範囲が広がってきています。

このリリースからは、そのベンチマークが Security Center の推奨事項の基盤となり、既定のポリシー イニシアティブとして完全に統合されます。 

すべての Azure サービスには、対応するドキュメントにセキュリティ ベースライン ページが用意されています。 それらのベースラインは、Azure セキュリティ ベンチマークに基づいて作成されています。

Security Center の規制コンプライアンス ダッシュボードを使用している場合、切り替え期間中は、ベンチマークのインスタンスが 2 つ表示されます。

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure セキュリティ ベンチマークが表示されている Azure Security Center の規制コンプライアンス ダッシュボード":::

既存の推奨事項は影響を受けず、ベンチマークが増えるにつれて、Security Center 内に変更が自動的に反映されます。 

詳細については、次のページを参照してください。

- [Azure セキュリティ ベンチマークについての詳細情報](/security/benchmark/azure/introduction)
- [規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>オンプレミスおよびマルチクラウド マシンの脆弱性評価が一般提供 (GA) リリース

Microsoft では 10 月に、[Azure Defender for servers](defender-for-servers-introduction.md) の統合脆弱性評価スキャナー (Qualys を使用) による、Azure Arc 対応サーバーのスキャンのプレビューを発表していました。

このほど、それが一般提供 (GA) リリースとなりました。

Azure 以外のマシンで Azure Arc を有効にした場合、Security Center では、統合された脆弱性スキャナーをそれらに手動で大規模にデプロイできるようにします。

この更新では、**Azure Defender for servers** の能力を最大限に活用して、すべての Azure および Azure 以外の資産にわたって脆弱性管理プログラムを統合できます。

主な機能は、次のとおりです。

- Azure Arc マシンでの VA (脆弱性評価) スキャナーのプロビジョニング状態を監視する
- 保護されていない Windows および Linux Azure Arc マシンに、統合された VA エージェントをプロビジョニングする (手動で大規模に)
- デプロイされたエージェントで検出された脆弱性を受け取って分析する (手動で大規模に)
- Azure VM と Azure Arc マシンでエクスペリエンスを統一する

[ハイブリッド マシンへの統合された Qualys 脆弱性スキャナーのデプロイについての詳細を参照してください](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[Azure Arc 対応サーバーについての詳細を参照してください](../azure-arc/servers/index.yml)。


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>管理グループのセキュア スコア (プレビュー) が利用可能に

セキュア スコア ページに、サブスクリプション レベルに加え、管理グループの集計セキュア スコアが表示されるようになりました。 そのため、組織内の管理グループと各管理グループのスコアを一覧表示することが可能になりました。

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="管理グループのセキュア スコアを表示する。":::

詳細については、[Azure Security Center のセキュリティ スコアとセキュリティ コントロール](secure-score-security-controls.md)に関するページを参照してください。

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Secure Scores API が一般提供 (GA) リリース

[Secure Scores API](/rest/api/securitycenter/securescores/) を使用してスコアにアクセスできるようになりました。 この API メソッドを使用すると、データに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築したりするための柔軟性が得られます。 次に例を示します。

- **Secure Scores** API を使用して、特定のサブスクリプションのスコアを取得する
- **Secure Score Controls** API を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示する

Secure Scores API で実現可能な外部ツールについては、[GitHub コミュニティのセキュリティ スコアの領域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)を参照してください。

詳細については、[Azure Security Center のセキュリティ スコアとセキュリティ コントロール](secure-score-security-controls.md)に関するページを参照してください。


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Azure Defender for App Service に未解決の DNS 保護を追加

サブドメインの乗っ取りは、組織にとって重大度の高い一般的な脅威です。 サブドメインの乗っ取りは、プロビジョニング解除された Web サイトを参照する DNS レコードがある場合に発生する可能性があります。 このような DNS レコードは、"未解決の DNS" エントリとも呼ばれます。 CNAME レコードは、この脅威に対して特に脆弱です。 

サブドメインの乗っ取りが発生すると、脅威アクターが悪意のあるアクティビティを実行しているサイトに、組織のドメイン向けのトラフィックをリダイレクトできるようになります。

今後は、App Service Web サイトの使用を停止する際、Azure Defender for App Service によって未解決の DNS エントリが検出されます。 このような場合、DNS エントリの参照先リソースが存在せず、Web サイトがサブドメインの乗っ取りに対して無防備になります。 これらの保護は、ドメインの管理に Azure DNS を使用しているか、外部のドメイン レジストラーを使用しているかに関係なく利用でき、Windows 上の App Service と Linux 上の App Service の両方に適用されます。

詳細情報:

- [App Service のアラート参照テーブル](alerts-reference.md#alerts-azureappserv) - 未解決の DNS エントリが検出されたときにトリガーされる新しい Azure Defender アラートが 2 つ追加されています
- [未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する](../security/fundamentals/subdomain-takeover.md) - サブドメインの乗っ取りの脅威と未解決の DNS に関する事柄について説明しています
- [Azure Defender for App Service の概要](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>マルチクラウド コネクタが一般提供 (GA) リリース

通常、クラウド ワークロードは複数のクラウド プラットフォームにまたがるため、クラウド セキュリティサービスもそうである必要があります。

Azure Security Center は、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードを保護します。

AWS アカウントまたは GCP アカウントを接続すると、そのネイティブ セキュリティ ツール (AWS Security Hub、GCP Security Command Center など) が Azure Security Center に統合されます。

この機能は、あらゆる大手クラウド環境が、Security Center によってもたらされる可視性と保護の対象になることを意味します。 この統合のメリットをいくつか紹介します。

- エージェントの自動プロビジョニング - Security Center は Azure Arc を使用して、Log Analytics エージェントをお使いの AWS インスタンスにデプロイします
- ポリシー管理
- 脆弱性の管理
- 埋め込まれたエンドポイント検出と応答 (EDR)
- セキュリティ構成ミスの検出
- すべてのクラウド プロバイダーからのセキュリティに関する推奨事項を一元的に表示
- Security Center のセキュリティ スコアの計算へのすべてのリソースの組み込み
- AWS リソースと GCP リソースの規制コンプライアンスの評価

Defender for Cloud のメニューから [マルチクラウド コネクタ] **を** 選択すると、新しいコネクタを作成するためのオプションが表示されます：

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="アプリのマルチクラウド コネクタ Security Center AWS アカウントの追加 ボタン":::

詳細情報:
- [Azure Security Center への AWS アカウントの接続](quickstart-onboard-aws.md)
- [Azure Security Center への GCP アカウントの接続](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>サブスクリプションと管理グループのセキュア スコアから推奨事項全体を除外する

現在、Microsoft では、推奨事項全体を除外できるように機能を拡張しています。 サブスクリプション、管理グループ、またはリソースに対して Security Center が行うセキュリティの推奨事項を微調整できるよう、さまざまなオプションを追加しているところです。

まれに、サードパーティ ツールによって問題が解決済みであるにもかかわらず、Security Center で検出されず、リソースが異常として表示されてしまうことがあります。 または、推奨事項が属していないと思われるスコープに表示されます。 この推奨事項は、特定のサブスクリプションに適していない可能性があります。 または、ご自分の組織が、特定のリソースまたは推奨事項に関連するリスクを受け入れることにしたのかもしれません。

このプレビュー機能では、推奨事項の除外を作成できるようになりました。

- **リソースを除外** して、将来、問題のあるリソースと共に一覧に表示されないようにします。セキュリティ スコアには影響しません。 リソースは適用外として表示され、その理由は "除外" として、選択した特定の正当な理由と共に示されます。

- **サブスクリプションまたは管理グループを除外** して、推奨事項がセキュリティ スコアに影響を与えないようにします。サブスクリプションまたは管理グループには今後表示されません。 これは、既存のリソースと、今後作成するすべてのリソースに関係してきます。 推奨事項は、選択したスコープに対して選択した特定の理由でマークされます。

詳細については、「[セキュリティ スコアからのリソースと推奨事項の除外](exempt-resource.md)」を参照してください。



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>テナント全体の可視性をユーザーがグローバル管理者にリクエスト可能に

Security Center のデータを表示するアクセス許可がないユーザーには、その組織のグローバル管理者にアクセス許可をリクエストするためのリンクが表示されるようになりました。 リクエストには、希望するロールとそれが必要な理由を添えることになります。

:::image type="content" source="media/management-groups-roles/request-tenant-permissions.png" alt-text="テナント全体のアクセス許可を要求できることをユーザーに通知するバナー。":::

詳細については、「[テナント全体のアクセス許可が不十分な場合に要求する](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)」を参照してください。


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure セキュリティ ベンチマークのカバレッジを広げるために追加される、35 個のプレビュー推奨事項

[Azure セキュリティ ベンチマーク](/security/benchmark/azure/introduction)が Azure Security Center の既定のポリシー イニシアティブになりました。 

このベンチマークのカバー範囲を拡大するために、次の 35 個のプレビュー推奨事項が Security Center に追加されています。

> [!TIP]
> プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](implement-security-recommendations.md)」を参照してください。

| セキュリティ コントロール                     | 新しい推奨事項                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 保存時の暗号化を有効にする            | - Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある<br>- MySQL サーバーに対して Bring Your Own Key データ保護を有効にする必要がある<br>- PostgreSQL サーバーに対して Bring Your Own Key データ保護を有効にする必要がある<br>- Cognitive Services アカウントでカスタマー マネージド キー (CMK) によるデータ暗号化を有効にする必要がある<br>- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある<br>- SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある<br>- ストレージ アカウントでは暗号化にカスタマー マネージド キー (CMK) を使用する必要がある                                                                                                                                                              |
| セキュリティのベストプラクティスを実装する    | - サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある<br> - 自分のサブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある<br> - 重要度が高いアラートの電子メール通知を有効にする必要がある<br> - 重要度が高いアラートについて、サブスクリプション所有者に対する電子メール通知を有効にする必要がある<br> - キー コンテナーで消去保護が有効になっている必要がある<br> - キー コンテナーで論理的な削除が有効になっている必要がある |
| アクセスおよびアクセス許可の管理        | - 関数アプリで "クライアント証明書 (着信クライアント証明書)" を有効にする必要がある |
| DDoS 攻撃からアプリケーションを保護する | - Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある<br> - Azure Front Door Service サービスに対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある |
| 承認されていないネットワーク アクセスの制限 | - キー コンテナーでファイアウォールを有効にする必要がある<br> - キー コンテナー用にプライベート エンドポイントを構成する必要がある<br> - App Configuration ではプライベート リンクを使用する必要がある<br> - Azure Cache for Redis は仮想ネットワーク内に存在しなければならない<br> - Azure Event Grid ドメインではプライベート リンクを使用する必要がある<br> - Azure Event Grid トピックではプライベート リンクを使用する必要がある<br> - Azure Machine Learning ワークスペースではプライベート リンクを使用する必要がある<br> - Azure SignalR Service ではプライベート リンクを使用する必要がある<br> - Azure Spring Cloud ではネットワークの挿入を使用する必要がある<br> - コンテナー レジストリでは無制限のネットワーク アクセスを許可しない<br> - コンテナー レジストリではプライベート リンクを使用する必要がある<br> - MariaDB サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - MySQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - PostgreSQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある<br> - ストレージ アカウントではプライベート リンク接続を使用する必要がある<br> - ストレージ アカウントでは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある<br> - VM Image Builder テンプレートでは、プライベート リンクを使用する必要がある|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

関連リンク:

- [Azure セキュリティ ベンチマークについての詳細情報](/security/benchmark/azure/introduction)
- [Azure Database for MariaDB についての詳細情報](../mariadb/overview.md)
- [Azure Database for MySQL についての詳細情報](../mysql/overview.md)
- [Azure Database for PostgreSQL についての詳細情報](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>フィルター処理された推奨事項一覧の CSV エクスポート 

2020 年 11 月には、推奨事項のページにフィルターを追加しました (「[推奨事項の一覧にフィルターを追加](release-notes-archive.md#recommendations-list-now-includes-filters)」を参照してください)。 12 月には、それらのフィルターを拡張しました (「[環境、重大度、利用可能な応答用の新しいフィルターが推奨事項のページに追加](release-notes-archive.md#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)」を参照してください)。 

今回は、フィルター処理された一覧に表示されている推奨事項のみが CSV エクスポートの対象となるよう、 **[Download to CSV]\(CSV にダウンロード\)** ボタンの動作を変更しています。 

たとえば、以下の画像を見ると、一覧がフィルター処理されて 2 つの推奨事項が表示されていることがわかります。 生成された CSV ファイルには、それらの 2 つの推奨事項に関係する各リソースの状態情報が含まれています。   

:::image type="content" source="media/managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="フィルター処理された推奨事項を CSV ファイルにエクスポートする。":::

詳細については、[Azure Security Center でのセキュリティに関する推奨事項](review-security-recommendations.md)を参照してください。


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Azure Policy の評価で "適用除外" であったリソースが "準拠" とレポートされるように

以前は、Azure Policy の推奨事項で **適用除外** と評価されたリソースが "非準拠" と表示されていました。 ユーザー アクションによって状態を "準拠" に変更することはできませんでした。 今回の変更以降、わかりやすくするためにこれらは "準拠" としてレポートされます。

Azure Policy への影響は 1 つだけで、準拠しているリソースの数が増えます。 Azure Security Center のセキュア スコアには影響はありません。


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>セキュア スコアと規制コンプライアンス データの週単位のスナップショットを連続エクスポートでエクスポート (プレビュー)

セキュア スコアと規制コンプライアンス データの週単位のスナップショットをエクスポートするための新しいプレビュー機能を[連続エクスポート](continuous-export.md) ツールに追加しました。

エクスポートの頻度は、連続エクスポートを定義するときに設定します。

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="連続エクスポートの頻度を選択する。":::

- **ストリーミング** – リソースの正常性状態が更新されると、評価が送信されます (更新がなければ、データは送信されません)。
- **スナップショット** - 規制コンプライアンスの全評価を対象に現在の状態のスナップショットが毎週送信されます (セキュア スコアと規制コンプライアンス データの週単位のスナップショットに関するプレビュー機能)。

この機能の全容については、「[Security Center のデータを連続的にエクスポートする](continuous-export.md)」を参照してください。

## <a name="december-2020"></a>2020 年 12 月

12 月の更新プログラムには次のものが含まれます。

- [マシン上の SQL サーバー向け Azure Defender の一般提供開始](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Synapse Analytics の専用 SQL プールに対する Azure Defender for SQL のサポートの一般提供開始](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [グローバル管理者が自分にテナントレベルのアクセス許可を付与できるようになりました](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [2 つの新しい Azure Defender プラン: Azure Defender for DNS と Azure Defender for Resource Manager (プレビュー段階)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Azure portal の新しいセキュリティ アラート ページ (プレビュー)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Azure SQL Database と SQL Managed Instance における Security Center エクスペリエンスの刷新](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [資産インベントリ ツールとフィルターの更新](#asset-inventory-tools-and-filters-updated)
- [SSL 証明書を要求する Web アプリに関する推奨事項がセキュリティ スコアの対象から除外](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [環境、重大度、利用可能な応答用の新しいフィルターが推奨事項のページに追加](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [連続エクスポートで新しいデータ型が利用可能になり、deployifnotexist ポリシーが改善](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>マシン上の SQL サーバー向け Azure Defender の一般提供開始

Azure Security Center には、2 つの SQL Server 用 Azure Defender プランが用意されています。

- **Azure SQL データベース サーバー向け Azure Defender** - Azure ネイティブの SQL Server を保護します 
- **マシン上の SQL サーバー向け Azure Defender** - ハイブリッド、マルチクラウド、およびオンプレミスの環境で SQL サーバーに対して同様の保護を拡張します

本発表以降は、**Azure Defender for SQL** によって、データベースとそのデータが場所にかかわらず保護されます。

Azure Defender for SQL には、脆弱性評価機能が用意されています。 脆弱性評価ツールには、次の高度な機能が含まれています。

- **ベースライン構成** (新機能) を使用すると、脆弱性スキャンの結果を、実際のセキュリティ問題を示す可能性があるものにインテリジェントに絞り込むことができます。 ベースラインのセキュリティ状態を確立した後は、脆弱性評価ツールによって、そのベースラインからの逸脱のみがレポートされます。 ベースラインに一致する結果は、それ以降のスキャンで合格と見なされます。 これにより、ユーザーとアナリストは、重要な問題に注意を集中できます。
- **詳細なベンチマーク情報** は、検出された結果や、それらとリソースの関連性を "*把握する*" のに役立ちます。
- **修復スクリプト** は、特定されたリスクを軽減するのに役立ちます。

[Azure Defender for SQL](defender-for-sql-introduction.md) についてさらに詳しく学習します。


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Synapse Analytics の専用 SQL プールに対する Azure Defender for SQL のサポートの一般提供開始

Azure Synapse Analytics (以前の SQL DW) は、エンタープライズ データ ウェアハウスとビッグ データの分析を兼ね備えた分析サービスです。 専用 SQL プールは、Azure Synapse のエンタープライズ データ ウェアハウス機能です。 詳細については、[Azure Synapse Analytics (以前の SQL DW) の概要](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)に関するページを参照してください。

Azure Defender for SQL では、次のものを使用して専用 SQL プールを保護します。

- **高度な脅威保護** - 脅威と攻撃を検出します 
- **脆弱性評価機能** - セキュリティに関する構成ミスを特定して修復します

Azure Defender for SQL での Azure Synapse Analytics SQL プールのサポートは、Azure Security Center 内の Azure SQL データベース バンドルに自動的に追加されます。 Azure portal の [Synapse ワークスペース] ページに、新しい [Azure Defender for SQL] タブが表示されます。

[Azure Defender for SQL](defender-for-sql-introduction.md) についてさらに詳しく学習します。


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>グローバル管理者が自分にテナントレベルのアクセス許可を付与できるようになりました

Azure Active Directory ロールが **グローバル管理者** であるユーザーは、テナント全体の責任を持つ場合がありますが、Azure Security Center でその組織全体の情報を表示するための Azure アクセス許可がありません。 

テナントレベルのアクセス許可を自分に割り当てるには、「[テナント全体のアクセス許可を自分に付与する](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)」セクションの手順に従います。


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>2 つの新しい Azure Defender プラン: Azure Defender for DNS と Azure Defender for Resource Manager (プレビュー段階)

Azure 環境向けに、2 つの新しいクラウドネイティブ範囲の脅威防止機能が追加されました。

これらの新しい保護により、脅威アクターからの攻撃に対する回復性が強化され、Azure Defender によって保護される Azure リソースの数が大幅に増加します。

- **Azure Defender for Resource Manager** - 組織で実行されるすべてのリソース管理操作を自動的に監視します。 詳細については、次を参照してください。
    - [Azure Defender for Resource Manager の概要](defender-for-resource-manager-introduction.md)
    - [Azure Defender for Resource Manager アラートに対応する](defender-for-resource-manager-usage.md)
    - [Azure Defender for Resource Manager で提供されるアラートの一覧](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender for DNS** - Azure リソースからのすべての DNS クエリを継続的に監視します。 詳細については、次を参照してください。
    - [Azure Defender for DNS の概要](defender-for-dns-introduction.md)
    - [Azure Defender for DNS アラートに対応する](defender-for-dns-usage.md)
    - [Azure Defender for DNS で提供されるアラートの一覧](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Azure portal の新しいセキュリティ アラート ページ (プレビュー)

Azure Security Center のセキュリティ アラート ページは、次の機能を提供できるように再設計されました。

- **アラートのトリアージ エクスペリエンスの向上** - アラートに対応する労力を減らし、最も関連性の高い脅威に焦点を絞りやすくするために、一覧にはカスタマイズ可能なフィルターとグループ化のオプションが追加されました
- **アラート一覧への情報の追加** - MITRE ATT & ACK の戦術など
- **サンプル アラートを作成するためのボタン** - Azure Defender の機能を評価し、アラートの構成 (SIEM 統合、メール通知、ワークフローの自動化) をテストします。すべての Azure Defender プランでサンプル アラートを作成できます
- **Azure Sentinel のインシデント エクスペリエンスとの連携** - 両方の製品を使用するお客様は、より簡単に 2 つを切り替えることによって、どちらの製品からも、もう一方の製品が提供する情報を取得しやすくなりました
- 長大なアラート一覧の **パフォーマンス向上**
- アラート一覧の **キーボードによるナビゲーション**
- **Azure Resource Graph のアラート** - Azure Resource Graph (すべてのリソースを対象とする、Kusto に似た API) のアラートにクエリを実行できます。 これは、独自のアラート ダッシュボードを作成している場合にも役立ちます。 [Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/index.yml)。

新しいエクスペリエンスにアクセスするには、セキュリティ アラート ページの上部にあるバナーの "今すぐ試す" リンクを使用します。

:::image type="content" source="media/managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="新しいアラート エクスペリエンス (プレビュー) へのリンクが表示されたバナー。":::

新しいアラート エクスペリエンスでサンプル アラートを作成するには、「[Azure Defender のサンプル アラートを生成する](alert-validation.md#generate-sample-security-alerts)」を参照してください。


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance における Security Center エクスペリエンスの刷新 

SQL 内の Security Center エクスペリエンスでは、Security Center と Azure Defender for SQL の次の機能にアクセスできます。

- **セキュリティに関する推奨事項** - Security Center では、接続されているすべての Azure リソースのセキュリティの状態を定期的に分析して、潜在的なセキュリティの構成ミスを特定します。 そのうえで、それらの脆弱性を修復し、組織のセキュリティ体制を改善する方法に関する推奨事項を提供します。
- **セキュリティ アラート** - SQL インジェクション、ブルートフォース攻撃、特権の悪用などの脅威がないか、Azure SQL アクティビティを継続的に監視する検出サービス。 このサービスは、Security Center で詳細なアクション指向のセキュリティ アラートをトリガーし、Microsoft の Azure ネイティブ SIEM ソリューションである Azure Sentinel を使用して調査を継続するためのオプションを提供します。
- **調査結果** – Azure SQL 構成を継続的に監視し、脆弱性を修復するのに役立つ脆弱性評価サービスです。 評価スキャンでは、Azure SQL のセキュリティ状態の概要が詳細なセキュリティ調査結果と一緒に示されます。     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure Security Center の SQL 用セキュリティ機能が Azure SQL 内から利用可能":::


### <a name="asset-inventory-tools-and-filters-updated"></a>資産インベントリ ツールとフィルターの更新

Azure Security Center のインベントリ ページが更新され、次の変更が加えられました。

- **[Guides and feedback]\(ガイドとフィードバック\)** がツール バーに追加されました。 これにより、関連する情報やツールへのリンクがあるペインが開きます。 
- **サブスクリプション フィルター** が、リソースで使用可能な既定のフィルターに追加されました。
- 現在のフィルター オプションを Azure Resource Graph クエリとして開くための **[クエリを開く]** リンク (以前は "Resource Graph エクスプローラーで表示" という名前でした)。
- 各フィルターの **演算子オプション**。 "=" に加えて他の論理演算子も選択できるようになりました。 たとえば、タイトルに "encrypt" という文字列が含まれているアクティブな推奨事項があるすべてのリソースを検索することができます。 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="資産インベントリのフィルターでの演算子オプションのコントロール":::

インベントリの詳細については、「[資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)」を参照してください。


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>SSL 証明書を要求する Web アプリに関する推奨事項がセキュリティ スコアの対象から除外

推奨事項 "Web apps should request an SSL certificate for all incoming requests (Web アプリではすべての受信要求に対して SSL 証明書を要求する必要がある)" が、 **[Manage access and permissions]\(アクセスおよびアクセス許可の管理\)** (最大 4 ポイントに相当) セキュリティ コントロールから **[Implement security best practices]\(セキュリティのベスト プラクティスを実装する\)** (ポイントなしに相当) に移動されました。 

Web アプリが証明書を要求するようにすることで、確実にセキュリティが向上します。 ただし、公開されている Web アプリの場合は関係ありません。 HTTPS ではなく HTTP 経由でサイトにアクセスする場合は、クライアント証明書を受信しません。 したがって、アプリケーションにクライアント証明書が必要な場合は、HTTP 経由でのアプリケーションへの要求を許可しないでください。 詳細については、「[Azure App Service に対する TLS 相互認証の構成](../app-service/app-service-web-configure-tls-mutual-auth.md)」を参照してください。

この変更により、この推奨事項は、スコアに影響を与えない、推奨のベスト プラクティスになりました。 

各セキュリティ コントロールに含まれる推奨事項については、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>環境、重大度、利用可能な応答用の新しいフィルターが推奨事項のページに追加

Azure Security Center では、接続されているすべてのリソースを監視し、セキュリティの推奨事項を生成します。 これらの推奨事項を使用して、ハイブリッド クラウドの体制を強化し、組織、業界、および国に関連するポリシーと標準へのコンプライアンス状況を追跡します。

Security Center のカバレッジと機能が拡張されるにつれて、セキュリティに関する推奨事項の一覧が毎月増えていきます。 たとえば、「[Azure セキュリティ ベンチマークのカバレッジを広げるために追加された、29 個のプレビュー推奨事項](release-notes-archive.md#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)」を参照してください。

一覧が増加するのに伴い、最も関心のあるものを見つけることができるように、フィルターを使用して推奨事項を絞り込む機能が求められるようになりました。 11 月に、推奨事項のページにフィルターを追加しました (「[推奨事項の一覧にフィルターを追加](release-notes-archive.md#recommendations-list-now-includes-filters)」を参照してください)。

この月に追加されたフィルターには、次の条件で推奨事項の一覧を絞り込むためのオプションがあります。

- **環境** - AWS、GCP、または Azure リソース (または任意の組み合わせ) の推奨事項を表示します
- **重大度** - Security Center によって設定された重大度分類に従って推奨事項を表示します
- **応答アクション** - Security Center の応答オプションの可用性に応じて推奨事項を表示します (修正、拒否、強制)

    > [!TIP]
    > 応答アクション フィルターは、 **[Quick fix available (Yes/No)]\(クイック修正を使用できます (はい/いいえ)\)** フィルターに置き換わるものです。 
    > 
    > これらの応答オプションの詳細については、次を参照してください。
    > - [修正ボタン](implement-security-recommendations.md#fix-button)
    > - [適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="セキュリティ コントロールにグループ化された推奨事項。" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>連続エクスポートで新しいデータ型が利用可能になり、deployifnotexist ポリシーが改善

Azure Security Center の連続エクスポート ツールを使用すると、環境内の他の監視ツールで使用するために Security Center の推奨事項とアラートをエクスポートできます。

連続エクスポートを使用して、エクスポートする "内容" とエクスポート先の "場所" を完全にカスタマイズできます。 詳細については、「[Security Center のデータを連続的にエクスポートする](continuous-export.md)」を参照してください。

これらのツールは、次の点で強化および拡張されました。

- **連続エクスポートの deployifnotexist ポリシーの強化**。 これらのポリシーで、以下のことが可能になりました。

    - **構成が有効になっているかどうかの確認。** 有効になっていない場合、ポリシーによって非準拠として表示され、準拠しているリソースが作成されます。 提供されている Azure Policy テンプレートの詳細については、「[連続エクスポートを設定する](continuous-export.md#set-up-a-continuous-export)」の [Azure ポリシーを使用して大規模にデプロイする] タブを参照してください。

    - **セキュリティ調査結果のエクスポートのサポート。** Azure Policy テンプレートを使用する場合、調査結果が含まれるように連続エクスポートを構成できます。 これは、"サブ" 推奨事項 (たとえば、脆弱性評価スキャナーからの調査結果) や、"親" 推奨事項 "システム更新プログラムをマシンにインストールする必要がある" に対する特定のシステム更新プログラムを含む推奨事項をエクスポートする場合に関連します。
    
    - **セキュリティ スコア データのエクスポートのサポート。**

- **規制コンプライアンス評価データの追加 (プレビュー)。** 任意のカスタム イニシアティブを含め、規制コンプライアンス評価の更新を Log Analytics ワークスペースまたはイベント ハブに連続的にエクスポートできるようになりました。 この機能は、各国のクラウドでは使用できません。

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="連続エクスポート データに規制コンプライアンス評価情報を含めるためのオプション。":::

## <a name="november-2020"></a>2020 年 11 月

11 月の更新プログラムには次のものが含まれます。

- [Azure セキュリティ ベンチマークのカバレッジを広げるために追加された、29 個のプレビュー推奨事項](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Security Center の法令遵守ダッシュボードに追加された NIST SP 800 171 R2](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [推奨事項の一覧にフィルターを追加](#recommendations-list-now-includes-filters)
- [自動プロビジョニング エクスペリエンスの向上と拡張](#auto-provisioning-experience-improved-and-expanded)
- [連続エクスポートでセキュア スコアが利用可能に (プレビュー)](#secure-score-is-now-available-in-continuous-export-preview)
- ["システム更新プログラムをマシンにインストールする必要がある" 推奨事項にサブ推奨事項を追加](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Azure portal のポリシー管理ページに、既定のポリシー割り当ての状態を表示](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure セキュリティ ベンチマークのカバレッジを広げるために追加された、29 個のプレビュー推奨事項

Azure セキュリティ ベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 [Azure セキュリティ ベンチマークの詳細を確認してください](/security/benchmark/azure/introduction)。

ベンチマークのカバレッジを拡大するために、次の 29 個のプレビュー推奨事項が Security Center に追加されています。

プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](implement-security-recommendations.md)」を参照してください。

| セキュリティ コントロール                     | 新しい推奨事項                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 転送中のデータを暗号化する              | - PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない<br>- MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない<br>- API アプリに対して TLS を最新バージョンに更新する必要がある<br>- 関数アプリに対して TLS を最新バージョンに更新する必要がある<br>- Web アプリに対して TLS を最新バージョンに更新する必要がある<br>- API アプリでは FTPS を必須とする<br>- 関数アプリでは FTPS を必須とする<br>- Web アプリでは FTPS を必須とする                                                                                                                                                                                                                                                                                                                                                                                                                           |
| アクセスおよびアクセス許可の管理        | - Web アプリではすべての受信要求に対して SSL 証明書を要求する必要がある<br>- API アプリではマネージド ID を使用する必要がある<br>- 関数アプリではマネージド ID を使用する必要がある<br>- Web アプリではマネージド ID を使用する必要がある                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 承認されていないネットワーク アクセスの制限 | - PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある<br>- MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある<br>- MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 監査とログ記録を有効にする          | - App Services の診断ログを有効にする必要があります                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| セキュリティのベストプラクティスを実装する    | - 仮想マシンに対して Azure Backup を有効にする必要がある<br>- Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある<br>- Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある<br>- Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある<br>- API アプリに対して PHP を最新バージョンに更新する必要がある<br>- Web アプリに対して PHP を最新バージョンに更新する必要がある<br>- API アプリに対して Java を最新バージョンに更新する必要がある<br>- 関数アプリに対して Java を最新バージョンに更新する必要がある<br>- Web アプリに対して Java を最新バージョンに更新する必要がある<br>- API アプリに対して Python を最新バージョンに更新する必要がある<br>- 関数アプリに対して Python を最新バージョンに更新する必要がある<br>- Web アプリに対して Python を最新バージョンに更新する必要がある<br>- SQL サーバーの監査のリテンション期間は少なくとも 90 日に設定する必要がある |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

関連リンク:

- [Azure セキュリティ ベンチマークについての詳細情報](/security/benchmark/azure/introduction)
- [Azure API アプリについての詳細情報](../app-service/app-service-web-tutorial-rest-api.md)
- [Azure 関数アプリについての詳細情報](../azure-functions/functions-overview.md)
- [Azure Web アプリについての詳細情報](../app-service/overview.md)
- [Azure Database for MariaDB についての詳細情報](../mariadb/overview.md)
- [Azure Database for MySQL についての詳細情報](../mysql/overview.md)
- [Azure Database for PostgreSQL についての詳細情報](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Security Center の法令遵守ダッシュボードに追加された NIST SP 800 171 R2

NIST SP 800-171 R2 標準が、Azure Security Center の法令遵守ダッシュボードで使用するための組み込みイニシアティブとして使用できるようになりました。 コントロールのマッピングの詳細については、「[NIST SP 800-171 R2 規制コンプライアンスの組み込みイニシアチブの詳細](../governance/policy/samples/nist-sp-800-171-r2.md)」を参照してください。 

標準をサブスクリプションに適用し、コンプライアンス状態を継続的に監視するには、「[規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ](update-regulatory-compliance-packages.md)」の手順を使用します。

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Security Center の法令遵守ダッシュボードでの NIST SP 800 171 R2 標準":::

このコンプライアンス標準の詳細については、[NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final) に関するページを参照してください。


### <a name="recommendations-list-now-includes-filters"></a>推奨事項の一覧にフィルターを追加

さまざまな条件に従って、セキュリティに関する推奨事項の一覧をフィルター処理できるようになりました。 次の例では、以下の条件を満たす推奨事項を表示するように、推奨事項の一覧がフィルター処理されています。

- **一般提供** されている (つまり、プレビューではない)
- **ストレージ アカウント** を対象とする
- **クイック修正** による修復をサポートしている

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="推奨事項の一覧のフィルター。":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>自動プロビジョニング エクスペリエンスの向上と拡張

自動プロビジョニング機能は、Security Center の保護によるメリットが得られるように、必要な拡張機能を新規および既存の Azure VM にインストールすることで管理オーバーヘッドの削減を支援します。 

Azure Security Center の拡大に伴って、より多くの拡張機能が開発されています。Security Center では、リソースの種類の大規模な一覧を監視できます。 Azure Policy の機能を活用することで、自動プロビジョニング ツールが拡張され、他の拡張機能とリソースの種類がサポートされるようになりました。

次の自動プロビジョニングを構成できるようになりました。

- Log Analytics エージェント
- (新) Kubernetes 用の Azure Policy アドオン
- (新) Microsoft Dependency Agent

詳細については、「[Azure Security Center からのエージェントと拡張機能の自動プロビジョニング](enable-data-collection.md)」をご覧ください。


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>連続エクスポートでセキュア スコアが利用可能に (プレビュー)

セキュア スコアの連続エクスポートを使用すると、スコアの変更を Azure Event Hubs または Log Analytics ワークスペースにリアルタイムでストリーム配信することができます。 この機能を使用すると、次のことができます。

- 動的レポートを使用し、一定期間にわたってセキュア スコアを追跡する
- Azure Sentinel (または他の SIEM) にセキュア スコア データをエクスポートする
- そのデータを、組織内でセキュア スコアを監視する目的で既に使用しているプロセスに統合する

[Security Center のデータを連続的にエクスポートする](continuous-export.md)方法についての詳しい情報をご覧ください。


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"システム更新プログラムをマシンにインストールする必要がある" 推奨事項にサブ推奨事項を追加

**システム更新プログラムをマシンにインストールする必要がある** 推奨事項が強化されています。 新しいバージョンには、不足している更新プログラムごとのサブ推奨事項が含まれているほか、次の点が改善されています。

- Azure portal の Azure Security Center ページでの再設計されたエクスペリエンス。 "**システム更新プログラムをマシンにインストールする必要がある**" に関する推奨事項の詳細ページには、次に示すような結果の一覧が表示されます。 1 つの結果を選択すると、詳細ウィンドウが開き、修復情報および影響を受けるリソースの一覧へのリンクが表示されます。

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="更新された推奨事項のポータル エクスペリエンスで、サブ推奨事項のいずれかを開く。":::

- Azure Resource Graph (ARG) からの推奨事項用に強化されたデータ。 ARG は、効率的なリソース探索を実現するように設計されている Azure のサービスです。 ARG を使用すると、特定のサブスクリプション セットの大規模なクエリを実行し、環境を効果的に管理できます。 

    Azure Security Center では、ARG および [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/) を使用して、幅広いセキュリティ態勢データに対してクエリを実行できます。

    以前は、この推奨事項を ARG で照会した場合、取得できるのは、マシン上でその推奨事項を修復する必要があるという情報のみでした。 次に示す強化されたバージョンのクエリを実行すると、不足している各システム更新プログラムがマシン別にグループ化されて返されます。

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Azure portal のポリシー管理ページに、既定のポリシー割り当ての状態を表示

サブスクリプションに既定の Security Center ポリシーが割り当てられているかどうかを確認できます。これは、Azure portal の Security Center の **[セキュリティ ポリシー]** ページに表示されます。

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="既定のポリシー割り当てを示す Azure Security Center の [ポリシー管理] ページ。":::



## <a name="october-2020"></a>2020 年 10 月

10 月の更新プログラムには次のものが含まれます。
- [オンプレミスおよびマルチクラウド マシンの脆弱性評価 (プレビュー)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall の推奨事項の追加 (プレビュー)](#azure-firewall-recommendation-added-preview)
- ["Kubernetes Services では承認された IP 範囲を定義する必要があります" という推奨事項のクイック修正による更新](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [規制コンプライアンス ダッシュボードへの、標準を削除するオプションの追加](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Azure Resource Graph (ARG) からの Microsoft.Security/securityStatuses テーブルの削除](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>オンプレミスおよびマルチクラウド マシンの脆弱性評価 (プレビュー)

[Azure Defender for servers](defender-for-servers-introduction.md) の統合された脆弱性評価スキャナー (Qualys を使用) により、Azure Arc 対応サーバーがスキャンされるようになりました。

Azure 以外のマシンで Azure Arc を有効にした場合、Security Center では、統合された脆弱性スキャナーをそれらに手動で大規模にデプロイできるようにします。

この更新では、**Azure Defender for servers** の能力を最大限に活用して、すべての Azure および Azure 以外の資産にわたって脆弱性管理プログラムを統合できます。

主な機能は、次のとおりです。

- Azure Arc マシンでの VA (脆弱性評価) スキャナーのプロビジョニング状態を監視する
- 保護されていない Windows および Linux Azure Arc マシンに、統合された VA エージェントをプロビジョニングする (手動で大規模に)
- デプロイされたエージェントで検出された脆弱性を受け取って分析する (手動で大規模に)
- Azure VM と Azure Arc マシンでエクスペリエンスを統一する

[ハイブリッド マシンへの統合された Qualys 脆弱性スキャナーのデプロイについての詳細を参照してください](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[Azure Arc 対応サーバーについての詳細を参照してください](../azure-arc/servers/index.yml)。


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall の推奨事項の追加 (プレビュー)

Azure Firewall を使用してすべての仮想ネットワークを保護するための新しい推奨事項が追加されました。

**仮想ネットワークは Azure Firewall によって保護する必要がある** という推奨事項では、仮想ネットワークへのアクセスを制限し、Azure Firewall を使用して潜在的な脅威を防止することを勧めています。

[Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) の詳細を参照します。


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>"Kubernetes Services では承認された IP 範囲を定義する必要があります" という推奨事項のクイック修正による更新

**Kubernetes Services では承認された IP 範囲を定義する必要があります** という推奨事項に、クイック修正オプションが追加されました。

この推奨事項の詳細と、Security Center の他のすべての推奨事項については、「[セキュリティの推奨事項 - リファレンス ガイド](recommendations-reference.md)」を参照してください。

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="クイック修正オプションがある &quot;Kubernetes Services では承認された IP 範囲を定義する必要があります&quot; という推奨事項。":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>規制コンプライアンス ダッシュボードへの、標準を削除するオプションの追加

Security Center の規制コンプライアンス ダッシュボードでは、特定のコンプライアンスのコントロールと要件をどのように満たしているかに基づいて、コンプライアンス体制に関する分析情報が提供されます。

ダッシュボードには、規制標準の既定のセットが含まれています。 提供されている標準に、組織に関連していないものが含まれている場合、簡単なプロセスでサブスクリプションの UI から削除できるようになりました。 標準は、"*サブスクリプション*" レベルだけで削除できます。管理グループのスコープでは、削除できません。

詳細については、「[ダッシュボードから標準を削除する](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)」を参照してください。


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Azure Resource Graph (ARG) からの Microsoft.Security/securityStatuses テーブルの削除

Azure Resource Graph は、効率的なリソース探索を提供するように設計されている、Azure のサービスです。環境を効果的に管理できるように、特定のサブスクリプションのセットにわたって大規模なクエリを実行する機能を備えています。 

Azure Security Center では、ARG および [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/) を使用して、幅広いセキュリティ態勢データに対してクエリを実行できます。 次に例を示します。

- 資産インベントリが使用します (ARG)
- [多要素認証 (MFA) が有効になっていないアカウントを識別する](multi-factor-authentication-enforcement.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)方法を示すために、サンプル ARG クエリのドキュメントを作成しました

ARG 内には、クエリで使用できるデータのテーブルがあります。

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph エクスプローラーと利用可能なテーブル。":::

> [!TIP]
> ARG ドキュメントの「[Azure Resource Graph のテーブルとリソースの種類のリファレンス](../governance/resource-graph/reference/supported-tables-resources.md)」には、使用可能なすべてのテーブルの一覧があります。

この更新から、**Microsoft.Security/securityStatuses** テーブルが削除されました。 securityStatuses API は引き続き使用できます。

データ置換は、Microsoft.Security/Assessments テーブルで使用できます。

Microsoft.Security/securityStatuses と Microsoft.Security/Assessments の大きな違いは、前者が評価の集計を示すのに対して、後者はそれぞれの単一のレコードを保持することです。

たとえば、Microsoft.Security/securityStatuses は、次のように 2 つの policyAssessments の配列で結果を返します。

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
一方、Microsoft.Security/Assessments では、次のような各ポリシー評価のレコードが保持されます。

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**securityStatuses を使用する既存の ARG クエリを、評価テーブルを使用するように変換する例:**

SecurityStatuses を参照するクエリ:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Assessments テーブル用の置換クエリ:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

詳細については、以下のリンクを参照してください。
- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)
- [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020 年 9 月

9 月の更新プログラムには次のものが含まれます。
- [Security Center の外観の変更](#security-center-gets-a-new-look)
- [Azure Defender のリリース](#azure-defender-released)
- [Azure Defender for Key Vault の一般提供開始](#azure-defender-for-key-vault-is-generally-available)
- [Files と ADLS Gen2 を対象とした Azure Defender for Storage 保護の一般提供開始](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [資産インベントリ ツールの一般提供開始](#asset-inventory-tools-are-now-generally-available)
- [コンテナー レジストリと仮想マシンのスキャンに対する特定の脆弱性の検出結果の無効化](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [推奨事項からリソースを除外する](#exempt-a-resource-from-a-recommendation)
- [Security Center の AWS および GCP コネクタによるマルチクラウド エクスペリエンスの実現](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes ワークロード保護の推奨事項バンドル](#kubernetes-workload-protection-recommendation-bundle)
- [連続エクスポートで脆弱性評価の結果が利用可能](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [新しいリソースを作成するときに推奨事項を適用してセキュリティ構成ミスを防止](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [ネットワーク セキュリティ グループ推奨事項の改善](#network-security-group-recommendations-improved)
- [プレビューの AKS 推奨事項 "Kubernetes Services でポッドのセキュリティ ポリシーを定義する必要がある" の非推奨化](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Azure Security Center からの電子メールの改善](#email-notifications-from-azure-security-center-improved)
- [セキュリティ スコアからのプレビューの推奨事項の除外](#secure-score-doesnt-include-preview-recommendations)
- [推奨事項への重大度インジケーターと更新間隔の追加](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center の外観の変更

Security Center のポータル ページの UI が更新されました。 新しいページには、新しい概要ページと、セキュリティ スコア、資産インベントリ、および Azure Defender の各ダッシュボードが含まれています。

新しいデザインの概要ページには、セキュリティ スコア、資産インベントリ、Azure Defender の各ダッシュボードにアクセスするためのタイルが追加されました。 規制コンプライアンス ダッシュボードにリンクしているタイルもあります。

詳細については、[概要ページ](overview-page.md)に関するページをご覧ください。


### <a name="azure-defender-released"></a>Azure Defender のリリース

**Azure Defender** は、Azure とハイブリッド ワークロードを高度かつインテリジェントに保護するために Security Center 内に統合された、クラウド ワークロード保護プラットフォーム (CWPP) です。 これは Azure Security Center の Standard 価格レベル オプションに代わるものです。 

Azure Security Center の **価格と設定** の領域から Azure Defender を有効にすると、次の Defender プランが同時にすべて有効になり、環境のコンピューティング、データ、およびサービス レイヤーに対する包括的な防御が提供されます。

- [Azure Defender for servers](defender-for-servers-introduction.md)
- [Azure Defender for App Service](defender-for-app-service-introduction.md)
- [Azure Defender for Storage](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Azure Defender for Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender for container registries](defender-for-container-registries-introduction.md)

これらのプランについてはそれぞれ、Security Center のドキュメントで個別に説明されています。

Azure Defender では、専用ダッシュボードによって、仮想マシン、SQL データベース、コンテナー、Web アプリケーション、ネットワークなどに対して、セキュリティ アラートと高度な脅威防止が提供されます。

[Azure Defender に関する詳細情報](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender for Key Vault の一般提供開始

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 

**Azure Defender for Key Vault** には、Azure Key Vault 用に Azure ネイティブの高度な脅威防止機能が用意されており、セキュリティ インテリジェンスのレイヤーが追加されます。 結果的に、Azure Defender for Key Vault は、拡張機能によって Key Vault アカウントに依存する多くのリソースを保護します。

このオプション プランの一般提供が開始されました。 この機能は、プレビューでは "Azure Key Vault 向けの高度な脅威防止機能" として利用できました。

また、Azure portal の Key Vault ページには、**Security Center** の推奨事項とアラートの専用 **セキュリティ** ページが追加されています。

詳細については、「[Azure Defender for Key Vault](defender-for-key-vault-introduction.md)」を参照してください。


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Files と ADLS Gen2 を対象とした Azure Defender for Storage 保護の一般提供開始 

**Azure Defender for Storage** では、Azure Storage アカウント上の潜在的に有害なアクティビティが検出されます。 BLOB コンテナー、ファイル共有、またはデータ レイクのいずれに格納されているデータでも保護できます。

[Azure Files](../storage/files/storage-files-introduction.md) および [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) サポートの一般提供が開始されました。

2020 年 10 月 1 日から、これらのサービスのリソースの保護に対して課金が開始されます。

詳細については、「[Azure Defender for Storage](defender-for-storage-introduction.md)」を参照してください。


### <a name="asset-inventory-tools-are-now-generally-available"></a>資産インベントリ ツールの一般提供開始

Azure Security Center の資産インベントリ ページを使用すると、Security Center に接続したリソースのセキュリティ態勢が 1 つのページに表示されます。

Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。 その後、これらの脆弱性を修正する方法に関する推奨事項が提供されます。

いずれかのリソースに未処理の推奨事項がある場合は、インベントリに表示されます。

詳細については、「[資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)」を参照してください。



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>コンテナー レジストリと仮想マシンのスキャンに対する特定の脆弱性の検出結果の無効化

Azure Defender には、Azure Container Registry とお使いの仮想マシンのイメージをスキャンするための脆弱性スキャナーが含まれています。

組織のニーズとして、検出結果を修復するのではなく無視する必要がある場合は、必要に応じて検出結果を無効にできます。 無効化された検出結果は、セキュリティ スコアに影響を与えたり、不要なノイズを生成したりすることはありません。

無効化のルールで定義した条件と一致する検出結果は、検出結果の一覧には表示されません。

このオプションは、次の推奨事項の詳細ページから使用できます。

- **Azure Container Registry イメージの脆弱性を修復する必要があります**
- **仮想マシンの脆弱性を修復する必要がある**

詳細については、[コンテナー イメージの特定の検出結果の無効化](defender-for-container-registries-usage.md#disable-specific-findings-preview)と[仮想マシンの特定の検出結果の無効化](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)に関するページを参照してください。


### <a name="exempt-a-resource-from-a-recommendation"></a>推奨事項からリソースを除外する

場合によっては、特定の推奨事項に関するリソースが、異常ではないはずなのに、異常として表示されることがあります (これによりセキュリティ スコアが低くなります)。 それは、Security Center によって追跡されていないプロセスによって修復された可能性があります。 あるいは、ご自身の組織が、その特定のリソースのリスクを受け入れることにしたのかもしれません。 

このような場合は、除外規則を作成して、今後そのリソースが異常なリソースの一覧に表示されないようにすることができます。 これらの規則には、以下で説明するように、文書化された妥当性を含めることができます。

詳細については、「[推奨事項とセキュリティ スコアからリソースを除外する](exempt-resource.md)」を参照してください。


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Security Center の AWS および GCP コネクタによるマルチクラウド エクスペリエンスの実現

通常、クラウド ワークロードは複数のクラウド プラットフォームにまたがるため、クラウド セキュリティサービスもそうである必要があります。

Azure Security Center は、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードを保護するようになりました。

AWS および GCP アカウントを Security Center にオンボードすると、AWS Security Hub、GCP Security Command Center、および Azure Security Center が統合されます。 

詳細については、[Azure Security Center への AWS アカウントの接続](quickstart-onboard-aws.md)および [Azure Security Center への GCP アカウントの接続](quickstart-onboard-gcp.md)に関するページを参照してください。


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes ワークロード保護の推奨事項バンドル

Kubernetes ワークロードが既定で確実に保護されるように、Security Center によって、Kubernetes 受付制御を含む適用オプションなど、Kubernetes レベルの強化の推奨事項が追加されます。

AKS クラスターに Kubernetes 用 Azure Policy アドオンをインストールすると、Kubernetes API サーバーに対するすべての要求が、クラスターに保存される前に、事前定義された一連のベスト プラクティスを基準にして監視されます。 その後、ベスト プラクティスを適用し、それを将来のワークロードに対して要求するように構成できます。

たとえば、特権コンテナーが作成されないように要求することができます。また、今後の特権コンテナー作成要求はすべてブロックされます。

詳細については、[Kubernetes 受付制御を使用したワークロード保護のベスト プラクティス](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)に関するページを参照してください。


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>連続エクスポートで脆弱性評価の結果が利用可能

連続エクスポートを使用して、アラートと推奨事項を Azure Event Hubs、Log Analytics ワークスペース、または Azure Monitor にストリーム配信します。 そこから、このデータを SIEM (Azure Sentinel、Power BI、Azure Data Explorer など) と統合できます。

Security Center の統合された脆弱性評価ツールは、"仮想マシンの脆弱性を修復する必要がある" などの "親" の推奨事項内で、ご自身のリソースに関する結果を実行可能な推奨事項として返します。 

推奨事項を選択し **[セキュリティに関する調査結果を含める]** オプションを有効にすることで、連続エクスポートを介して、セキュリティに関する調査結果をエクスポートに利用できるようにるようになりました。

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="連続エクスポート構成でのセキュリティに関する調査結果トグルを含める。" :::

関連するページ:

- [Azure 仮想マシンに対する Security Center の統合された Qualys 脆弱性評価](deploy-vulnerability-assessment-vm.md)
- [Azure Container Registry に対する Security Center の統合された脆弱性評価ソリューション](defender-for-container-registries-usage.md)
- [連続エクスポート](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>新しいリソースを作成するときに推奨事項を適用してセキュリティ構成ミスを防止

セキュリティ構成ミスは、セキュリティ インシデントの大きな原因です。 Security Center には、特定の推奨事項に関する新しいリソースの構成ミスを "*防止*" する際に役立つ機能が追加されました。 

この機能は、ワークロードの安全性を確保し、ご自身のセキュリティ スコアを安定させるうえで役立ちます。

特定の推奨事項に基づいたセキュリティで保護された構成は、次の 2 つのモードで提供されます。

- Azure Policy の **Deny** 効果を使用して、異常なリソースが作成されるのを防ぐことができます

- **適用** オプションを使用すると、Azure Policy の **DeployIfNotExist** 効果を利用して、作成時に非対応リソースを自動的に修復できます
 
これは、選択したセキュリティの推奨事項で利用でき、リソースの詳細ページの上部にあります。

詳細については、「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照してください。

###  <a name="network-security-group-recommendations-improved"></a>ネットワーク セキュリティ グループ推奨事項の改善

ネットワーク セキュリティ グループに関連する次のセキュリティ推奨事項が、一部の擬陽性のインスタンスを減らすために改善されました。

- すべてのネットワーク ポートは、VM に関連付けられた NSG で制限する必要があります
- 仮想マシンの管理ポートを閉じておく必要がある
- インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある
- サブネットはネットワーク セキュリティ グループに関連付けられている必要がある


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>プレビューの AKS 推奨事項 "Kubernetes Services でポッドのセキュリティ ポリシーを定義する必要がある" の非推奨化

プレビューの推奨事項 "Kubernetes Services でポッドのセキュリティ ポリシーを定義する必要がある" は、[Azure Kubernetes Service](../aks/use-pod-security-policies.md) のドキュメントで説明されているように非推奨とされます。

ポッド セキュリティ ポリシー (プレビュー) 機能は非推奨になる予定です。2020 年 10 月 15 日を過ぎると使用できなくなるため、AKS 用の Azure Policy が推奨されます。

ポッド セキュリティ ポリシー (プレビュー) が非推奨となった後、今後のクラスター アップグレードを実行し、Azure サポート内に留まるには、非推奨の機能を使用する既存のクラスターでその機能を無効にする必要があります。


### <a name="email-notifications-from-azure-security-center-improved"></a>Azure Security Center からの電子メールの改善

セキュリティ アラートに関する次の電子メール領域が改善されました。 

- すべての重大度レベルのアラートに関する電子メール通知の送信機能を追加
- サブスクリプションのさまざまな Azure ロールのユーザーに通知する機能を追加
- (本当の侵害である可能性が高い) 重大度が高いアラートについては、サブスクリプションの所有者に事前に送信されます
- 電子メール通知の構成ページから電話番号フィールドが削除されました

詳細については、「[セキュリティ アラートのメール通知を設定する](configure-email-notifications.md)」を参照してください。


### <a name="secure-score-doesnt-include-preview-recommendations"></a>セキュリティ スコアからのプレビューの推奨事項の除外 

Security Center は、セキュリティの問題について、リソース、サブスクリプション、および組織を継続的に評価します。 その後、すべての結果を 1 つのスコアに集約して、現在のセキュリティの状況を一目で確認できるようにします。スコアが高くなるほど、識別されたリスク レベルが低下します。

新しい脅威が発見されると、新しいセキュリティに関するアドバイスが、新しい推奨事項を介して Security Center で利用できるようになります。 突然セキュリティ スコアが変わることがないように、また、スコアが影響を受ける前に、新しい推奨事項を調べる猶予期間を確保できるように、**プレビュー** のフラグが設定されている推奨事項が、セキュリティ スコアの計算から除外されるようになりました。 それでも、その推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復しておく必要があります。

また、**プレビュー** の推奨事項によって、リソースの "異常" がレンダリングされることはありません。

プレビューの推奨事項の例を次に示します。

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="プレビュー フラグが設定された推奨事項。":::

[セキュリティ スコアの詳細](secure-score-security-controls.md)。


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>推奨事項への重大度インジケーターと更新間隔の追加

推奨事項の詳細ページに、更新間隔インジケーター (関連する場合) が追加され、推奨事項の重大度が明確に表示されるようになりました。

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="更新間隔と重大度が表示されている推奨事項ページ。":::


## <a name="august-2020"></a>2020 年 8 月

8 月の更新プログラムには次のものが含まれます。

- [資産インベントリ - 資産のセキュリティ態勢の強力な新しいビュー](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Azure Active Directory のセキュリティの既定値群 (多要素認証用) のサポートの追加](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [サービス プリンシパルに関する推奨事項の追加](#service-principals-recommendation-added)
- [VM の脆弱性評価 - 推奨事項とポリシーの統合](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [ASC_default イニシアティブに追加された新しい AKS セキュリティ ポリシー – プライベート プレビューのお客様のみ使用](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>資産インベントリ - 資産のセキュリティ態勢の強力な新しいビュー

Security Center の資産インベントリ (現在プレビュー) を使用すると、Security Center に接続したリソースのセキュリティ態勢を確認することができます。

Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。 その後、これらの脆弱性を修正する方法に関する推奨事項が提供されます。 いずれかのリソースに未処理の推奨事項がある場合は、インベントリに表示されます。

ビューとそのフィルターを使用して、セキュリティの態勢データを調査し、結果に基づいてさらにアクションを実行できます。

[資産インベントリ](asset-inventory.md)の詳細を確認してください。


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Azure Active Directory のセキュリティの既定値群 (多要素認証用) のサポートの追加

Security Center に、Microsoft の無料の ID セキュリティ保護である[セキュリティの既定値群](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)の完全なサポートが追加されました。

セキュリティの既定値群により、構成済みの ID セキュリティ設定が提供され、組織は一般的な ID 関連の攻撃から保護されます。 セキュリティの既定値群により、500 万個以上のテナント全体が既に保護されています。50,000 個のテナントも Security Center によって保護されています。

Security Center では現在、セキュリティの既定値群が有効になっていない Azure サブスクリプションを識別した時点で、セキュリティの推奨事項を提供します。 Security Center ではこれまで、Azure Active Directory (AD) Premium ライセンスの一部である条件付きアクセスを使用した多要素認証の有効化を推奨していました。 Azure AD Free をご利用のお客様には、セキュリティの既定値群を有効にすることを現在お勧めしています。 

私たちの目標は、より多くのお客様に対して、MFA を使用してクラウド環境をセキュリティで保護し、[セキュリティ スコア](secure-score-security-controls.md)に対しても最もインパクトの高いリスクの 1 つを軽減することを推奨することです。

[セキュリティの既定値群](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)の詳細を確認してください。


### <a name="service-principals-recommendation-added"></a>サービス プリンシパルに関する推奨事項の追加

管理証明書を使用してサブスクリプションを管理している Security Center のお客様に対してサービス プリンシパルへの切り替えを推奨するための、新しい推奨事項が追加されました。

推奨事項 **[管理証明書の代わりにサブスクリプションを保護するために、サービス プリンシパルを使用する必要があります]** は、サービスプリンシパルまたは Azure Resource Manager を使用してサブスクリプションをより安全に管理することをお勧めするものです。 

「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)」で詳細を確認してください。


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>VM の脆弱性評価 - 推奨事項とポリシーの統合

Security Center では、VM を調べて脆弱性評価ソリューションが実行されているかどうかを検出します。 脆弱性評価ソリューションが検出されない場合、Security Center によって、展開を簡略化するための推奨事項が示されます。

脆弱性が発見された場合、調査結果をまとめた推奨事項が Security Center によって示され、必要に応じて調査および修復できるようにします。

使用されているスキャナーの種類に関係なく、すべてのユーザーに一貫したエクスペリエンスを提供できるように、4 つの推奨事項を次の 2 つに統合しました。

|統合された推奨事項|変更の説明|
|----|:----|
|**脆弱性評価ソリューションを仮想マシンで有効にする必要がある**|次の 2 つの推奨事項の代わりとなります。<br> ***** 組み込みの脆弱性評価ソリューションを仮想マシンで有効にする (Qualys を利用) (現在は非推奨) (Standard レベルに組み込み)<br> ***** 脆弱性評価ソリューションを仮想マシンにインストールする必要がある (現在は非推奨) (Standard と Free レベル)|
|**仮想マシンの脆弱性を修復する必要がある**|次の 2 つの推奨事項の代わりとなります。<br>***** 仮想マシンで検出された脆弱性を修復する (Qualys を利用) (現在は非推奨)<br>***** 脆弱性評価ソリューションによって脆弱性を修復する必要がある (現在は非推奨)|
|||

これで、同じ推奨事項を利用して、Security Center の脆弱性評価拡張機能、または Qualys や Rapid7 などの、パートナーからプライベートにライセンス供与されたソリューション ("BYOL") をデプロイすることになります。

また、脆弱性が検出され、Security Center に報告された場合、その脆弱性を特定した脆弱性評価ソリューションに関係なく、1 つの推奨事項によって調査結果がアラートとして通知されます。

#### <a name="updating-dependencies"></a>依存関係の更新

以前の推奨事項またはポリシーのキー/名前を参照するスクリプト、クエリ、または自動化がある場合は、次の表を利用してその参照を更新します。

##### <a name="before-august-2020"></a>2020 年 8 月より前

| 推奨|Scope|
|----|:----|
|**組み込みの脆弱性評価ソリューションを仮想マシンで有効にする (Qualys を利用)**<br>キー:550e890b-e652-4d22-8274-60b3bdb24c63|組み込み|
|**仮想マシンで検出された脆弱性を修復する (Qualys を利用)**<br>キー:1195afff-c881-495e-9bc5-1486211ae03f|組み込み|
|**脆弱性評価ソリューションを仮想マシンにインストールする必要があります**<br>キー:01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**脆弱性評価ソリューションによって脆弱性を修復する必要がある**<br>キー:71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|ポリシー|Scope|
|----|:----|
|**仮想マシンで脆弱性評価を有効にする必要がある**<br>ポリシー ID:501541f7-f7e7-4cd6-868c-4190fdad3ac9|組み込み|
|**脆弱性評価ソリューションによって脆弱性を修復する必要がある**<br>ポリシー ID:760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>2020 年 8 月以降

|推奨|Scope|
|----|:----|
|**脆弱性評価ソリューションを仮想マシンで有効にする必要がある**<br>キー: ffff0522-1e88-47fc-8382-2a80ba848f5d|組み込み + BYOL|
|**仮想マシンの脆弱性を修復する必要がある**<br>キー:1195afff-c881-495e-9bc5-1486211ae03f|組み込み + BYOL|
|||

|ポリシー|Scope|
|----|:----|
|[**仮想マシンで脆弱性評価を有効にする必要がある**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ポリシー ID:501541f7-f7e7-4cd6-868c-4190fdad3ac9 |組み込み + BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>ASC_default イニシアティブに追加された新しい AKS セキュリティ ポリシー – プライベート プレビューのお客様のみ使用

Kubernetes ワークロードが既定で確実に保護されるように、Security Center によって、Kubernetes 受付制御を含む適用オプションなど、Kubernetes レベル ポリシーが追加され、レコメンデーションが強化されます。

このプロジェクトの早期段階には、プライベート プレビューが含まれ、(既定では無効になっている) 新しいポリシーが ASC_default イニシアティブに追加されます。

このようなポリシーは無視しても問題ありません。お使いの環境に影響が出ることはありません。 有効にする場合、 https://aka.ms/SecurityPrP でプレビューに新規登録し、次のオプションから選択します。

1. **シングル プレビュー** – このプライベート プレビューのみに参加します。 参加するプレビューとして "ASC Continuous Scan" を明示的に伝えます。
1. **継続的プログラム** – これと将来のプライベート プレビューに追加します。 プロファイルとプライバシー契約を完了する必要があります。


## <a name="july-2020"></a>2020 年 7 月

7 月の更新プログラムには次のものが含まれます。
- [仮想マシンの脆弱性評価をマーケットプレース以外のイメージで使用可能に](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure Files と Azure Data Lake Storage Gen2 を含むように拡張された Azure Storage の脅威の防止 (プレビュー)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [脅威の防止機能を有効にするための 8 つの新しい推奨事項](#eight-new-recommendations-to-enable-threat-protection-features)
- [コンテナーのセキュリティ強化 - レジストリのスキャンの高速化とドキュメントの更新](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [新しい推奨事項とパス規則でのワイルドカードのサポートによって適応型アプリケーション制御を更新](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [非推奨となった SQL の高度なデータ セキュリティの 6 つのポリシー](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>仮想マシンの脆弱性評価をマーケットプレース以外のイメージで使用可能に

脆弱性評価ソリューションを展開する際、以前は展開前に Security Center によって検証チェックが実行されていました。 このチェックは、接続先の仮想マシンのマーケットプレース SKU を確認するために行われていました。 

今回の更新プログラムから、このチェックは削除され、脆弱性評価ツールを 'カスタム' Windows および Linux マシンに展開できるようになりました。 カスタム イメージは、マーケットプレースの既定値からユーザーが変更したものです。

統合された脆弱性評価拡張機能 (Qualys を使用) をより多くのマシンに展開できるようになりましたが、サポートは、「[統合された脆弱性スキャナーを Standard レベルの VM にデプロイする](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)」に記載されている OS を使用している場合にのみ受けることができます

詳細については、[仮想マシン向けの統合された脆弱性スキャナー (Azure Defender が必要)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) に関するページを参照してください。

Qualys または Rapid7 からプライベートにライセンス提供された独自の脆弱性評価ソリューションを使用する方法の詳細については、「[パートナーの脆弱性スキャン ソリューションをデプロイする](deploy-vulnerability-assessment-vm.md)」を参照してください。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure Files と Azure Data Lake Storage Gen2 を含むように拡張された Azure Storage の脅威の防止 (プレビュー)

Azure Storage の脅威の防止では、Azure Storage アカウント上の潜在的に有害なアクティビティを検出します。 Security Center では、ストレージ アカウントへのアクセスまたはストレージ アカウントの悪用の試行が検出されたときにアラートが表示されます。 

BLOB コンテナー、ファイル共有、またはデータ レイクのいずれに格納されているデータでも保護できます。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>脅威の防止機能を有効にするための 8 つの新しい推奨事項

次のリソース タイプに対して Azure Security Center の脅威の防止機能を簡単に提供するため、新たに 8 つの推奨事項が追加されました: 仮想マシン、App Service プラン、Azure SQL Database サーバー、マシン上の SQL Server、Azure Storage アカウント、Azure Kubernetes Service クラスター、Azure Container Registry レジストリ、および Azure Key Vault ボールト。

新しい推奨事項は次のとおりです。

- **Azure SQL Database サーバーで Advanced Data Security を有効にする必要がある**
- **マシン上の SQL サーバーで Advanced Data Security を有効にする必要がある**
- **Azure App Service プランで Advanced Threat Protection を有効にする必要がある**
- **Azure Container Registry レジストリで Advanced Threat Protection を有効にする必要がある**
- **Azure Key Vault コンテナーで Advanced Threat Protection を有効にする必要がある**
- **Azure Kubernetes Service クラスターで Advanced Threat Protection を有効にする必要がある**
- **Azure Storage アカウントで Advanced Threat Protection を有効にする必要がある**
- **仮想マシンで Advanced Threat Protection を有効にする必要がある**

これらの新しい推奨事項は、 **[Azure Defender を有効にする]** セキュリティ コントロールに属しています。

推奨事項には、クイック修正機能も含まれています。 

> [!IMPORTANT]
> これらの推奨事項を 1 つでも修復すると、関連するリソースを保護するための料金が発生します。 現在のサブスクリプションに関連するリソースがある場合、直ちに料金が発生します。 後で追加した場合は、後で行われます。
> 
> たとえば、サブスクリプションに Azure Kubernetes Service クラスターがない状態で脅威の防止を有効にした場合、料金は発生しません。 将来、同じサブスクリプションにクラスターを追加すると、そのクラスターは自動的に保護され、その時点で料金が発生します。

各項目の詳細については、[セキュリティに関する推奨事項のリファレンス ページ](recommendations-reference.md)を参照してください。

詳細については、「[Azure Security Center での脅威の防止](azure-defender.md)」を参照してください。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>コンテナーのセキュリティ強化 - レジストリのスキャンの高速化とドキュメントの更新

コンテナーのセキュリティ ドメインへの継続的な投資の一環として、Security Center では Azure Container Registry に保存されているコンテナー イメージの動的スキャンにおける大幅なパフォーマンス向上を実現しました。 スキャンは、通常約 2 分で完了するようになりました。 場合によっては、最大 15 分かかることがあります。

Azure Security Center のコンテナーのセキュリティ機能に関する明確さとガイダンスを改善するために、コンテナーのセキュリティに関するドキュメントのページも更新しました。 

Security Center でのコンテナー セキュリティの詳細については、次の記事を参照してください。

- [Security Center のコンテナーのセキュリティ機能の概要](container-security.md)
- [Azure Container Registry との統合の詳細](defender-for-container-registries-introduction.md)
- [Azure Kubernetes Service との統合の詳細](defender-for-kubernetes-introduction.md)
- [レジストリをスキャンして Docker ホストを強化する方法](container-security.md)
- [Azure Kubernetes Service クラスターの脅威防止機能からのセキュリティ アラート](alerts-reference.md#alerts-k8scluster)
- [Azure Kubernetes Service ホストの脅威防止機能からのセキュリティ アラート](alerts-reference.md#alerts-containerhost)
- [コンテナーに関するセキュリティの推奨事項](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>新しい推奨事項とパス規則でのワイルドカードのサポートによって適応型アプリケーション制御を更新

適応型アプリケーション制御の機能に、2 つの重要な更新プログラムが適用されています。

* 新しい推奨事項により、以前は許可されていなかった、正当である可能性のある動作が識別されます。 新しい推薦事項の「**適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある**」により、既存のポリシーに新しいルールを追加して、適応型アプリケーション制御違反アラートの擬陽性の数を減らすよう求めるメッセージが表示されます。

* パス規則でワイルドカードがサポートされるようになっています。 この更新プログラムから、ワイルドカードの使用が許可されたパス規則を構成できます。 サポートされているシナリオは 2 つあります。

    * パスの末尾でワイルドカードを使用し、そのフォルダーとサブフォルダー内のすべての実行可能ファイルを許可します

    * パスの途中でワイルドカードを使用し、変化するフォルダー名を備えた既知の実行可能ファイルの名前 (既知の実行可能ファイルを含む個人ユーザー フォルダー、自動生成されたフォルダー名など) を有効にします。


適応型アプリケーション制御の詳細については、[こちら](adaptive-application-controls.md)をご覧ください。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>非推奨となった SQL の高度なデータ セキュリティの 6 つのポリシー

SQL マシンの高度なデータ セキュリティに関連する 6 つのポリシーが非推奨になります。

- SQL マネージド インスタンスの高度なデータ セキュリティ設定で、Advanced Threat Protection の種類を [すべて] に設定する必要がある
- SQL Server の高度なデータ セキュリティ設定で、Advanced Threat Protection の種類を [すべて] に設定する必要がある
- SQL マネージド インスタンスの高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL Server の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL マネージド インスタンスの Advanced Data Security 設定で管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある
- SQL Server の Advanced Data Security 設定で、管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある

詳細については、[組み込みのポリシー](./policy-reference.md)に関するページを参照してください。



## <a name="june-2020"></a>2020 年 6 月

6 月の更新プログラムには次のものが含まれます。

- [セキュリティ スコア API (プレビュー)](#secure-score-api-preview)
- [SQL マシン向けの Advanced Data Security (Azure、他のクラウド、オンプレミス) (プレビュー)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Log Analytics エージェントを Azure Arc マシンにデプロイするための 2 つの新しい推奨事項 (プレビュー)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [連続エクスポートとワークフローの自動化の構成を大規模に作成するための新しいポリシー](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [NSG を使用してインターネットに接続していない仮想マシンを保護するための新しい推奨事項](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [脅威の防止と Advanced Data Security を有効にするための新しいポリシー](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>セキュリティ スコア API (プレビュー)

[セキュリティ スコア API](/rest/api/securitycenter/securescores/) (現在プレビュー段階) を使用して、スコアにアクセスできるようになりました。 この API メソッドにより、より柔軟にデータに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築できるようになります。 たとえば、**Secure Scores** API を使用して、特定のサブスクリプションのスコアを取得できます。 また、**Secure Score Controls** API を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示できます。

セキュリティ スコア API で実現可能な外部ツールの例については、「[GitHub コミュニティのセキュリティ スコア エリア](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)」を参照してください。

詳細については、[Azure Security Center のセキュリティ スコアとセキュリティ コントロール](secure-score-security-controls.md)に関するページを参照してください。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>SQL マシン向けの Advanced Data Security (Azure、他のクラウド、オンプレミス) (プレビュー)

Azure Security Center の SQL マシン向けの Advanced Data Security により、Azure、他のクラウド環境、さらにはオンプレミスのマシン でホストされている SQL Server も保護されるようになりました。 これにより、Azure ネイティブの SQL Server の保護が拡張され、ハイブリッド環境を完全にサポートできるようになります。

Advanced Data Security では、SQL マシンに対して、場所によらず脆弱性評価と高度な脅威の防止が提供されます。

セットアップには、次の 2 つの手順があります。

1. Log Analytics エージェントを SQL Server のホスト マシンにデプロイして、Azure アカウントへの接続を提供します。

1. Security Center の [価格と設定] ページで、オプションのバンドルを有効にします。

詳細については、[SQL マシン向けの Advanced Data Security](defender-for-sql-usage.md) に関するページを参照してください。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Log Analytics エージェントを Azure Arc マシンにデプロイするための 2 つの新しい推奨事項 (プレビュー)

[Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)を Azure Arc マシンにデプロイし、Azure Security Center によって保護されていることを確認する、2 つの新しい推奨事項が追加されました。

- **Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要がある (プレビュー)**
- **Log Analytics エージェントを Linux ベースの Azure Arc マシンにインストールする必要がある (プレビュー)**

これらの新しい推奨事項は、既存の (関連する) 推奨事項である、「**お使いのマシンに監視エージェントをインストールする必要があります**」と同じ、セキュリティ構成の修復、適応型アプリケーション制御の適用、システムの更新プログラムの適用、Endpoint Protection を有効にするという 4 つのセキュリティ コントロールに表示されます。

推奨事項には、デプロイ プロセスを高速化するためのクイック修復機能も含まれています。 

これら 2 つの新しい推奨事項の詳細については、「[コンピューティングとアプリの推奨事項](recommendations-reference.md#recs-compute)」テーブルを参照してください。

Azure Security Center でエージェントを使用する方法の詳細については、「[Log Analytics エージェントとは](./faq-data-collection-agents.yml#what-is-the-log-analytics-agent-)」を参照してください。

詳細については、[Azure Arc マシンの拡張機能](../azure-arc/servers/manage-vm-extensions.md)に関するページを参照してください。


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>連続エクスポートとワークフローの自動化の構成を大規模に作成するための新しいポリシー

組織の監視とインシデント対応プロセスを自動化すると、セキュリティ インシデントの調査と軽減にかかる時間を大幅に短縮できます。

組織全体に自動化の構成をデプロイするには、次の組み込みの "DeployIfdNotExist" Azure ポリシーを使用して、[連続エクスポート](continuous-export.md)および[ワークフローの自動化](workflow-automation.md)手順を作成して構成します。

ポリシー定義は Azure Policy で確認できます。


|目標  |ポリシー  |ポリシー ID  |
|---------|---------|---------|
|イベント ハブへの連続エクスポート|[Azure Security Center アラートおよび推奨事項についてイベント ハブへのエクスポートをデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Log Analytics ワークスペースへの連続エクスポート|[Azure Security Center アラートおよび推奨事項について Log Analytics ワークスペースへのエクスポートをデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|セキュリティ アラートのワークフローの自動化|[Azure Security Center アラートに対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|セキュリティに関する推奨事項のワークフローの自動化|[Azure Security Center 推奨事項に対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

[ワークフローの自動化テンプレート](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)を使用して開始するには、こちらを参照してください。

2 つのエクスポート ポリシーの使用の詳細については、「[提供されているポリシーを使用してワークフローの自動化を大規模に構成する](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies)」と「[連続エクスポートを設定する](continuous-export.md#set-up-a-continuous-export)」を参照してください。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>NSG を使用してインターネットに接続していない仮想マシンを保護するための新しい推奨事項

"セキュリティのベスト プラクティスの実装" セキュリティ コントロールに、次の新しい推奨事項が含まれるようになりました。

- **インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要がある**

既存の推奨事項である「**インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある**」では、インターネットに接続された仮想マシンとインターネットに接続されていない仮想マシンが区別されていませんでした。 どちらの場合でも、VM がネットワーク セキュリティ グループに割り当てられていないと、重大度の高い推奨事項が生成されていました。 この新しい推奨事項では、インターネットに接続されていないマシンを分離することで擬陽性を減らし、不必要な重要度の高いアラートを回避します。

詳細については、「[ネットワークの推奨事項](recommendations-reference.md#recs-networking)」テーブルを参照してください。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>脅威の防止と Advanced Data Security を有効にするための新しいポリシー

次の新しいポリシー定義が、ASC の既定のイニシアティブに追加されました。これらは関連するリソースの種類に対して、脅威の防止または Advanced Data Security の有効化を促進するよう設計されています。

ポリシー定義は Azure Policy で確認できます。


| ポリシー                                                                                                                                                                                                                                                                | ポリシー ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Azure SQL Database サーバーで Advanced Data Security を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [マシン上の SQL サーバーで Advanced Data Security を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Azure Storage アカウントで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Azure Key Vault コンテナーで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Azure App Service プランで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Azure Container Registry レジストリで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Azure Kubernetes Service クラスターで Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Virtual Machines で Advanced Threat Protection を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

詳細については、「[Azure Security Center での脅威の防止](azure-defender.md)」を参照してください。


## <a name="may-2020"></a>2020 年 5 月

5 月の更新プログラムには次のものが含まれます。
- [アラート抑制ルール (プレビュー)](#alert-suppression-rules-preview)
- [仮想マシンに対する脆弱性評価の一般提供の開始](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Just-In-Time (JIT) 仮想マシン (VM) へのアクセスの変更](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [別のセキュリティ コントロールへのカスタム推奨事項の移動](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [推奨事項をコントロールまたは単純なリストで表示するためのトグルの追加](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [拡張されたセキュリティ コントロール "セキュリティのベスト プラクティスの実装"](#expanded-security-control-implement-security-best-practices)
- [カスタム メタデータを使用したカスタム ポリシーの一般提供の開始](#custom-policies-with-custom-metadata-are-now-generally-available)
- [ファイルレス攻撃検出へのクラッシュ ダンプ分析機能の移行](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>アラート抑制ルール (プレビュー)

この新機能 (現在プレビュー段階) は、アラート疲れを減らすのに役立ちます。 ルールを使用して、無害であることが判明している、または組織内の通常の活動に関連しているアラートを自動的に非表示にします。 これにより、最も重要な脅威に集中できます。 

有効にした抑制ルールと一致するアラートは、引き続き生成されますが、その状態は "無視" に設定されます。 状態は Azure portal でも確認できますが、Security Center のセキュリティ アラートにアクセスして確認することもできます。

抑制ルールでは、アラートが自動的に無視される条件を定義します。 通常は、次のような場合に抑制ルールを使用します。

- ユーザーが擬陽性と判断したアラートを抑制する

- あまりにも頻繁にトリガーされるため有用ではないアラートを抑制する

詳しくは、「[Azure Security Center の脅威防止からのアラートの抑制](alerts-suppression-rules.md)」を参照してください。


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>仮想マシンに対する脆弱性評価の一般提供の開始

Security Center の Standard レベルに、追加料金なしで仮想マシンの統合脆弱性評価が追加されました。 この拡張機能には Qualys が利用されていますが、結果は Security Center に直接レポートされます。 Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Security Center 内でシームレスに処理されます。

この新しいソリューションでは、お客様の仮想マシンを継続的にスキャンし、脆弱性を検出してその結果を Security Center に表示できます。 

ソリューションをデプロイするには、新しいセキュリティの推奨事項を使用します。

"組み込みの脆弱性評価ソリューションを仮想マシンで有効にする (Qualys を利用)"

詳しくは、[仮想マシンに対する Security Center の統合脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)に関するページを参照してください。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Just-In-Time (JIT) 仮想マシン (VM) へのアクセスの変更

Security Center には、VM の管理ポートを保護するオプションの機能が備わっています。 これにより、最も一般的な形式のブルート フォース攻撃を防ぐことができます。

この更新では、この機能に次の変更が加えられました。

- VM での JIT の有効化を勧める推奨事項の名前が変更されました。 以前の "仮想マシンに Just-In-Time ネットワーク アクセス制御を適用する必要があります" から次のように変わりました: "仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります"。

- この推奨事項は、オープンな管理ポートがある場合にのみトリガーされます。

詳しくは、[JIT アクセス機能](just-in-time-access-usage.md)に関するページを参照してください。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>別のセキュリティ コントロールへのカスタム推奨事項の移動

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つが、"セキュリティのベスト プラクティスの実装" です。 お客様のサブスクリプションで作成されたカスタム推奨事項は自動的に、このコントロールに配置されていました。 

カスタム推奨事項は見つけやすいように、専用のセキュリティ コントロールである "カスタム推奨事項" に移行しました。 このコントロールは、お客様のセキュア スコアに影響しません。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>推奨事項をコントロールまたは単純なリストで表示するためのトグルの追加

セキュリティ コントロールは、関連するセキュリティ推奨事項の論理グループです。 これらはお客様の脆弱な攻撃対象領域を反映します。 コントロールは、セキュリティに関する一連の推奨事項と、これらの推奨事項を実装するための指示内容です。

組織が個々の攻撃対象領域をどの程度セキュリティで保護しているかをすぐに確認するには、各セキュリティ コントロールのスコアを確認します。

既定では、推奨事項がセキュリティ コントロールに表示されます。 この更新プログラムから、それらをリストとしても表示できるようになりました。 影響を受けるリソースの正常性状態で並べ替えられた単純なリストとして表示するには、新しいトグル "コントロールによるグループ化" を使用します。 このトグルはポータル内のリストの上にあります。

セキュリティ コントロール (およびこのトグル) は、新しいセキュア スコア エクスペリエンスの一部です。 ポータル内からフィードバックをお送りいただけますとさいわいです。

セキュリティ コントロールの詳細については、「[Azure Security Center の強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md)」を参照してください。

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="推奨設定の &quot;コントロールによるグループ化&quot; トグル。":::

### <a name="expanded-security-control-implement-security-best-practices"></a>拡張されたセキュリティ コントロール "セキュリティのベスト プラクティスの実装" 

強化されたセキュア スコアと共に導入されたセキュリティ コントロールの 1 つが、"セキュリティのベスト プラクティスの実装" です。 このコントロールに推奨事項があっても、セキュア スコアに影響はありません。 

この更新により、3 つの推奨事項が当初配置されていたコントロールからこのベスト プラクティス コントロールに移動されました。 この操作が行われたのは、これら 3 つの推奨事項のリスクが当初考えていたよりも低いことが判明したためです。

さらに、2 つの新しい推奨事項が導入され、このコントロールに追加されました。

移動された 3 つの推奨事項は次のとおりです。

- **ご利用のサブスクリプションに対して読み取りアクセス許可があるアカウントでは、MFA を有効にする必要があります** (当初は "MFA の有効化" コントロール内)
- **読み取りアクセス許可がある外部アカウントは、ご使用のサブスクリプションから削除する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)
- **お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります** (当初は "アクセスとアクセス許可の管理" コントロール内)

コントロールに追加された 2 つの新しい推奨事項は次のとおりです。

- **ゲスト構成拡張機能が Windows 仮想マシンにインストールされている必要がある (プレビュー)** - [Azure Policy ゲスト構成](../governance/policy/concepts/guest-configuration.md)を使用することで、サーバーとアプリケーションの設定に対して仮想マシンが可視化されます (Windows のみ)。

- **お使いのマシンで Windows Defender Exploit Guard を有効にする必要がある (プレビュー)** - Windows Defender Exploit Guard では、Azure Policy ゲスト構成エージェントが利用されます。 Exploit Guard には、さまざまな攻撃ベクトルに対してデバイスをロックダウンし、マルウェア攻撃でよく使用される動作をブロックするよう設計された 4 つのコンポーネントがありますが、企業がセキュリティ リスクと生産性の要件のバランスをとれるようになっています (Windows のみ)。

Windows Defender Exploit Guard の詳細については、「[Exploit Guard ポリシーの作成と展開](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)」を参照してください。

セキュリティ コントロールの詳細については、[強化されたセキュリティ スコア (プレビュー)](secure-score-security-controls.md) に関するページを参照してください。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>カスタム メタデータを使用したカスタム ポリシーの一般提供の開始

カスタム ポリシーは、Security Center の推奨事項エクスペリエンス、セキュア スコア、規制コンプライアンス標準ダッシュボードに含まれました。 この機能の一般提供が開始され、お客様は Security Center で組織のセキュリティ評価の範囲を拡大できるようになりました。 

Azure Policy でカスタム イニシアチブを作成し、それにポリシーを追加して Azure Security Center にオンボードし、推奨事項として視覚化します。

さらに、カスタム推奨事項のメタデータを編集するオプションも追加されました。 メタデータ オプションには、重要度、修復手順、脅威情報などが含まれます。  

詳しくは、「[詳細情報でのカスタム推奨事項の拡張](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)」を参照してください。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>ファイルレス攻撃検出へのクラッシュ ダンプ分析機能の移行 

Microsoft は Windows クラッシュ ダンプ (CDA) 検出機能を[ファイルレス攻撃検出](defender-for-servers-introduction.md#what-are-the-benefits-of-microsoft-defender-for-servers)に移行しています。 ファイルレス攻撃検出分析では、Windows マシンで次のセキュリティ アラートの高度なバージョンを利用できます。コード インジェクションの検出、Windows モジュールの偽装の検出、シェル コードの検出、疑わしいコード セグメントの検出。

この移行のメリットをいくつか紹介します。

- **プロアクティブかつタイムリーなマルウェア検出** - CDA のアプローチでは、クラッシュが発生して初めて分析を実行し、悪意のあるアーティファクトを検出する必要がありました。 ファイルレス攻撃検出を使用すると、メモリ内の脅威が活動しているのをプロアクティブに特定できます。 

- **強化されたアラート** - ファイルレス攻撃検出のセキュリティ アラートには、CDA では利用できない強化が加わっています (例: アクティブなネットワーク接続情報)。 

- **アラートの集計** - CDA では、単一のクラッシュ ダンプ内で複数の攻撃パターンを検出した場合に、複数のセキュリティ アラートをトリガーしていました。 ファイルレス攻撃検出では、同じプロセスから特定された攻撃パターンをすべて単一のアラートにまとめて、複数のアラートを関連付ける必要をなくしています。

- **Log Analytics ワークスペースでの要件の減少** - 機密データを含んでいる可能性があるクラッシュ ダンプは、Log Analytics ワークスペースにアップロードされなくなります。






## <a name="april-2020"></a>2020 年 4 月

4 月の更新プログラムには次のものが含まれます。
- [動的コンプライアンス パッケージの一般提供の開始](#dynamic-compliance-packages-are-now-generally-available)
- [Azure Security Center Free レベルへの ID に関する推奨事項の追加](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>動的コンプライアンス パッケージの一般提供の開始

Azure Security Center の規制コンプライアンス ダッシュボードに、追加の業界および規制の標準を追跡するための **動的コンプライアンス パッケージ** が追加されました (一般提供開始)。

動的コンプライアンス パッケージは、Security Center のセキュリティ ポリシー ページからサブスクリプションまたは管理グループに追加できます。 標準またはベンチマークをオンボードすると、評価として、マップされた関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 オンボードされた標準の概要レポートがダウンロードできるようになります。

次のような標準を追加できるようになりました。

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK OFFICIAL および UK NHS**
- **カナダ連邦の PBMM**
- **Azure CIS 1.1.0 (新規)** (Azure CIS 1.1.0 のより完全な表現)

さらに、[Azure セキュリティ ベンチマーク](/security/benchmark/azure/introduction)が最近追加されました。これは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 追加の標準が使用可能になると、ダッシュボードでサポートされます。  
 
詳しくは、「[規制コンプライアンス ダッシュボードでの動的コンプライアンス パッケージへの更新](update-regulatory-compliance-packages.md)」を参照してください。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Azure Security Center Free レベルへの ID に関する推奨事項の追加

Azure Security Center Free レベルで、ID とアクセスのセキュリティに関する推奨事項の一般提供が開始されました。 これは、クラウドのセキュリティ体制管理 (CSPM) 機能を無料化する取り組みの一環です。 これまで、これらの推奨事項を利用できるのは、Standard 価格レベルだけでした。

ID とアクセスに関する推奨事項の例を次に示します。

- "ご利用のサブスクリプションに対して所有者アクセス許可があるアカウントでは、MFA を有効にする必要があります。"
- "お使いのサブスクリプションに、最大 3 人の所有者を指定する必要があります。"
- "非推奨のアカウントは、ご利用のサブスクリプションから削除する必要があります。"

Free 価格レベルのサブスクリプションをお持ちのお客様は、ID とアクセスのセキュリティについて評価されることがなかったため、この変更により、セキュア スコアに影響が生じます。

詳しくは、「[ID とアクセスの推奨事項](recommendations-reference.md#recs-identityandaccess)」を参照してください。

詳細については、「[サブスクリプションでの多要素認証 (MFA) の実施を管理する](multi-factor-authentication-enforcement.md)」を参照してください。



## <a name="march-2020"></a>2020 年 3 月

3 月の更新プログラムには次のものが含まれます。

- [ワークフロー自動化の一般提供の開始](#workflow-automation-is-now-generally-available)
- [Azure Security Center と Windows Admin Center の統合](#integration-of-azure-security-center-with-windows-admin-center)
- [Azure Kubernetes Service の保護](#protection-for-azure-kubernetes-service)
- [Just-In-Time エクスペリエンスの改善](#improved-just-in-time-experience)
- [Web アプリケーションに関する 2 つのセキュリティ推奨事項の非推奨化](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>ワークフロー自動化の一般提供の開始

Azure Security Center のワークフロー自動化機能の一般提供が開始されました。 これを使用すると、セキュリティ アラートと推奨事項が出されたときにロジック アプリを自動でトリガーできます。 さらに、クイック修復オプションが利用可能なすべての推奨事項とアラートでは、手動のトリガーが使用可能です。

すべてのセキュリティ プログラムには、インシデント対応のための複数のワークフローが含まれています。 これらのプロセスには、直接の利害関係者への通知、変更管理プロセスの開始、および特定の修復手順の適用が含まれます。 これらのプロシージャの手順をできるだけ多く自動化することがセキュリティの専門家によって推奨されています。 自動化を行うと、オーバーヘッドが減少します。また、迅速かつ一貫した方法で、定義済みの要件に従ってプロセスの手順が実行されるようにして、セキュリティを向上させることができます。

ワークフローを実行するための Security Center の自動および手動機能の詳細については、「[ワークフローの自動化](workflow-automation.md)」を参照してください。

詳しくは、[ロジック アプリの作成](../logic-apps/logic-apps-overview.md)に関するページを参照してください。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure Security Center と Windows Admin Center の統合

Windows Admin Center から Azure Security Center にオンプレミスの Windows サーバーを直接移行できるようになりました。 これで、オンプレミスのサーバー、仮想マシン、追加の PaaS ワークロードなど、すべての Windows Admin Center リソースのセキュリティ情報を Security Center で一元的に確認できるようになります。

Windows Admin Center から Azure Security Center にサーバーを移行すると、以下が可能になります。

- Windows Admin Center の Security Center 拡張機能で、セキュリティに関するアラートと推奨事項を確認する。
- Azure portal 内の Security Center で (または API を使用して)、セキュリティ体制を確認し、Windows Admin Center で管理されているサーバーの追加の詳細情報を取得する。

詳しくは、[Azure Security Center と Windows Admin Center を統合する方法](windows-admin-center-integration.md)に関するページを参照してください。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes Service の保護

Azure Security Center では、Azure Kubernetes Service (AKS) を保護するコンテナー セキュリティ機能を拡張しています。

Kubernetes は人気のオープンソース プラットフォームとして広く普及しており、現在コンテナー オーケストレーションの業界標準となっています。 その実装が広まっているにもかかわらず、Kubernetes 環境をセキュリティで保護する方法については、まだ十分に理解されていません。 コンテナー化されたアプリケーションの攻撃対象領域を保護するには、潜在的な脅威を確実かつ継続的に監視するようインフラストラクチャを構成するための専門知識が必要です。

Security Center には以下の防御機能があります。

- **検出と可視化** - Security Center に登録されているサブスクリプション内のマネージド AKS インスタンスの継続的な検出。
- **セキュリティに関する推奨事項** - AKS のセキュリティ ベストプラクティスに準拠するのに役立つ、実行可能な推奨事項。 組織のセキュリティ体制の一環として確認できるように、これらの推奨事項はお客様のセキュア スコアに含まれています。 AKS に関する推奨事項の例としては、"Kubernetes Service クラスターへのアクセスを制限するには、ロールベースのアクセス制御を使用する必要があります" などがあります。
- **脅威の防止** - Security Center では、AKS デプロイの継続的な分析によって、ホストおよび AKS クラスター レベルで検出された脅威や悪意のあるアクティビティについてアラートを通知します。

詳しくは、「[Azure Kubernetes Service と Security Center の統合](defender-for-kubernetes-introduction.md)」を参照してください。

詳しくは、[Security Center のコンテナー セキュリティ機能](container-security.md)に関するページを参照してください。


### <a name="improved-just-in-time-experience"></a>Just-In-Time エクスペリエンスの改善

お客様の管理ポートを保護する Azure Security Center の Just-In-Time ツールの機能、操作、UI が、次のように強化されました。 

- **理由のフィールド** - Azure portal の Just-In-Time ページを使用して仮想マシン (VM) へのアクセスを要求する際に、要求の理由を入力するためにオプションの新しいフィールドを使用できます。 このフィールドに入力された情報は、アクティビティ ログで追跡できます。 
- **冗長な Just-In-Time (JIT) 規則の自動クリーンアップ** - JIT ポリシーを更新するたびに、クリーンアップ ツールが自動的に実行され、規則セット全体の有効性がチェックされます。 このツールでは、ポリシー内の規則と NSG 内の規則の不一致が検索されます。 クリーンアップ ツールで不一致が検出されると、原因が特定され、削除した方が安全と見なされる場合には不要な組み込み規則が削除されます。 ユーザーが作成した規則がこのツールによって削除されることはありません。 

詳しくは、[JIT アクセス機能](just-in-time-access-usage.md)に関するページを参照してください。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Web アプリケーションに関する 2 つのセキュリティ推奨事項の非推奨化

Web アプリケーションに関する 2 つのセキュリティ推奨事項が非推奨になります。 

- IaaS NSG 上の Web アプリケーションに対する規則を強化する必要があります。
    (関連ポリシー:IaaS 上の Web アプリケーションに対する NSG 規則を強化する必要がある)

- App Services へのアクセスを制限する必要があります。
    (関連ポリシー:App Services へのアクセスを制限する必要があります (プレビュー))

これらの推奨事項は、Security Center の推奨事項リストに表示されなくなります。 関連ポリシーは、"Security Center の既定" という名前のイニシアチブに含まれなくなります。

詳しくは、[セキュリティに関する推奨事項](recommendations-reference.md)についてのページを参照してください。




## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-preview"></a>Linux 向けのファイルレス攻撃検出 (プレビュー)

検出を回避するためにステルス性の高い方法を利用する攻撃者が増えているので、Azure Security Center では、Windows 向けに加えて Linux 向けのファイルレス攻撃検出を拡張しています。 ファイルレス攻撃では、ソフトウェアの脆弱性が悪用されて、悪意のあるペイロードが正常なシステム プロセスに挿入され、メモリ内に隠されます。 これらの手法には以下のようなものがあります。

- ディスク上のマルウェアの痕跡を最小化または排除する
- ディスクベースのマルウェア スキャン ソリューションによる検出の可能性を大幅に下げる

この脅威に対処するために、Azure Security Center では 2018 年 10 月にファイルレス攻撃検出をリリースしました。そしてこのたび、ファイルレス攻撃検出は Linux にも拡張されました。 



## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score-preview"></a>強化されたセキュア スコア (プレビュー)

Azure Security Center のセキュア スコア機能の強化バージョンが、プレビュー版で利用できるようになりました。 このバージョンでは、複数の推奨事項が、脆弱な攻撃対象領域をより正確に反映するセキュリティ コントロールにグループ化されています (例: 管理ポートへのアクセスの制限)。

プレビュー段階でセキュア スコアの変更に慣れていただけるほか、ご自分の環境のセキュリティを高めるのに役立つその他の修復機能をご確認いただけます。

詳細については、[強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md) に関するページを参照してください。



## <a name="november-2019"></a>2019 年 11 月

11 月の更新プログラムには次のものが含まれます。
 - [北米リージョンで Azure Key Vault 用 Threat Protection (プレビュー)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Azure Storage 用 Threat Protection にマルウェア評価スクリーニングを追加](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Logic Apps を使用したワークフローの自動化 (プレビュー)](#workflow-automation-with-logic-apps-preview)
 - [大量のリソースのクイック修正の一般提供を開始](#quick-fix-for-bulk-resources-generally-available)
 - [コンテナー イメージの脆弱性スキャン (プレビュー)](#scan-container-images-for-vulnerabilities-preview)
 - [規制コンプライアンス標準の追加 (プレビュー)](#additional-regulatory-compliance-standards-preview)
 - [Azure Kubernetes Service 用 Threat Protection (プレビュー)](#threat-protection-for-azure-kubernetes-service-preview)
 - [仮想マシンの脆弱性評価 (プレビュー)](#virtual-machine-vulnerability-assessment-preview)
 - [Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [カスタム ポリシーのサポート (プレビュー)](#support-for-custom-policies-preview)
 - [コミュニティおよびパートナー向けのプラットフォームで Azure Security Center のカバレッジを拡大](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [推奨事項とアラートのエクスポートによる高度な統合 (プレビュー)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Windows Admin Center から Security Center へのオンプレミス サーバーのオンボード (プレビュー)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>北米リージョンで Azure Key Vault 用 Threat Protection (プレビュー)

Azure Key Vault は、キー、シークレット、暗号化キー、ポリシーをクラウドで一元管理する機能を提供することによって、データを保護し、クラウド アプリケーションのパフォーマンスを向上させる非常に重要なサービスです。 Azure Key Vault には、機密性の高い、ビジネスに不可欠なデータが格納されているため、キー コンテナーとそこに格納されるデータの最大限のセキュリティが必要となります。

Azure Security Center による Azure Key Vault 用 Threat Protection のサポートにより、キー コンテナーへのアクセスやキー コンテナーの悪用を試みる、害を及ぼす可能性のある通常とは異なる試行を検出する、セキュリティ インテリジェンスのレイヤーが追加されます。 この新しい保護レイヤーにより、お客様は、セキュリティ専門家でなくてもキー コンテナーに対する脅威に対処することが可能となり、セキュリティ監視システムを管理できるようになります。 この機能は、北米リージョンでパブリック プレビュー段階にあります。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure Storage 用 Threat Protection にマルウェア評価スクリーニングを追加

Azure Storage 用 Threat Protection は、Microsoft 脅威インテリジェンスを利用した新しい検出機能を提供します。これらの機能では、ハッシュ評価分析を使用して Azure Storage へのマルウェアのアップロードを検出したり、アクティブな Tor 出口ノード (匿名化プロキシ) からの不審なアクセスを検出したりすることができます。 Azure Security Center を使用して、ストレージ アカウント全体で検出されたマルウェアを表示できるようになりました。


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps を使用したワークフローの自動化 (プレビュー)

セキュリティと IT/運用が一元管理されている組織では、環境で不一致が見つかった場合に組織内で必要なアクションを実行する内部ワークフロー プロセスを実装しています。 多くの場合、これらのワークフローは反復可能なプロセスであるため、自動化によって組織内のプロセスを大幅に効率化できます。

このたび、Azure Logic Apps を利用して自動化構成を作成し、推奨事項やアラートなど、ASC の特定の結果に基づいてそれらを自動的にトリガーするポリシーを作成できる新しい機能が Security Center に導入されます。 Azure Logic App は、Logic App コネクタの広範なコミュニティによってサポートされているカスタム アクションを実行するように構成することも、メールの送信や ServiceNow&trade; チケットのオープンなど、Security Center が提供するテンプレートのいずれかを使用するように構成することもできます。

ワークフローを実行するための Security Center の自動および手動機能の詳細については、「[ワークフローの自動化](workflow-automation.md)」をご覧ください。

Logic Apps の作成方法については、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) に関する記事をご覧ください。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>大量のリソースのクイック修正の一般提供を開始

セキュリティ スコアの一部として、ユーザーには多くのタスクが示されるため、大規模なフリートで問題を効果的に修復するのが困難になる可能性があります。

セキュリティの構成の誤りを簡単に修復し、大量のリソースに関する推奨事項をすばやく修復できるようにして、セキュリティ スコアを向上させるには、クイック修正を使用します。

この操作により、ユーザーは修復を適用するリソースを選択し、ユーザーに代わって設定を構成する修復アクションを起動できます。

このたび、Security Center の推奨事項ページの一部として、クイック修正の一般提供が開始されました。

クイック修正が有効になっている推奨事項については、[セキュリティの推奨事項のリファレンス ガイド](recommendations-reference.md)をご覧ください。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>コンテナー イメージの脆弱性スキャン (プレビュー)

Azure Security Center で、Azure Container Registry にあるコンテナー イメージの脆弱性をスキャンできるようになりました。

イメージのスキャンでは、コンテナー イメージ ファイルを解析し、(Qualys を利用して) 既知の脆弱性が存在するかどうかが確認されます。

スキャン自体は、新しいコンテナー イメージを Azure Container Registry にプッシュすると自動的にトリガーされます。 検出された脆弱性は Security Center の推奨事項として表示され、修正プログラムを適用してそれらの脆弱性による攻撃対象領域を削減する方法に関する情報と共にセキュリティ スコアに含められます。


### <a name="additional-regulatory-compliance-standards-preview"></a>規制コンプライアンス標準の追加 (プレビュー)

規制コンプライアンス ダッシュボードは、Security Center の評価に基づいて、コンプライアンス体制に関する分析情報を提供します。 ダッシュボードでは、お使いの環境が特定の規制基準や業界ベンチマークで指定されたコントロールと要件にどのように準拠しているかが示され、これらの要件に対処する方法に関する規範的な推奨事項が提供されます。

これまで、規制コンプライアンス ダッシュボードでは、Azure CIS 1.1.0、PCI-DSS、ISO 27001、SOC-TSP の 4 つの組み込み標準がサポートされていました。 このたび、Microsoft は、サポート対象となる追加の標準として、NIST SP 800-53 R4、SWIFT CSP CSCF v2020、Canada Federal PBMM、UK Official および UK NHS のパブリック プレビュー リリースを発表しました。 また、Azure CIS 1.1.0 の更新バージョンもリリースし、この標準のより多くのコントロールに対応して拡張性を強化します。

規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズの詳細については、[こちら](update-regulatory-compliance-packages.md)をご覧ください。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service 用 Threat Protection (プレビュー)

Kubernetes は、ソフトウェアをクラウドにデプロイして管理するための新しい標準になりつつあります。 Kubernetes の豊富な経験を持つ人はほとんどおらず、多くの人が一般的なエンジニアリングと管理にのみ注目し、セキュリティ面を見落としています。 コンテナーに焦点を合わせた攻撃対象領域が無防備な状態で攻撃者にさらされないように、Kubernetes 環境のセキュリティを慎重に構成する必要があります。 Security Center では、コンテナー領域でのサポートを、Azure で最も急成長しているサービスの 1 つである Azure Kubernetes Service (AKS) まで拡大しています。

このパブリック プレビュー リリースの新しい機能は次のとおりです。

- **検出と可視化** - Security Center の登録済みサブスクリプション内のマネージド AKS インスタンスの継続的な検出。
- **セキュリティ スコアの推奨事項** - お客様が AKS のセキュリティに関するベストプラクティスに準拠し、セキュリティ スコアを上げるのに役立つ実用的な項目。 「Kubernetes Service クラスターへのアクセスを制限するには、ロールベースの Access Control を使用する必要がある」などの項目を含む推奨事項。
- **脅威の検出** - ホストおよびクラスターベースの分析 (例: "特権コンテナーが検出されました")。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>仮想マシンの脆弱性評価 (プレビュー)

多くの場合、仮想マシンにインストールされているアプリケーションには、仮想マシンの侵害につながるおそれのある脆弱性が存在する可能性があります。 Microsoft は、Security Center Standard レベルに、追加料金なしで仮想マシンの脆弱性評価を組み込むことを発表しました。 Qualys を利用した脆弱性評価 (パブリック プレビュー) を使用すると、仮想マシンにインストールされているすべてのアプリケーションを継続的にスキャンして脆弱なアプリケーションを検出し、Security Center ポータルのエクスペリエンスに結果を表示できます。 Security Center がすべてのデプロイ操作を処理するため、ユーザーが追加の作業を行う必要はありません。 今後、お客様固有のビジネス ニーズに対応する脆弱性評価オプションを提供する予定です。

Azure Virtual Machines の脆弱性評価の詳細については、[こちら](deploy-vulnerability-assessment-vm.md)をご覧ください。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)

IaaS VM 上で実行されている SQL DB の脅威からの保護と脆弱性評価の、Azure Security Center によるサポートのプレビューが開始されました。

[脆弱性評価](../azure-sql/database/sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティ スコアの一部としてセキュリティ体制が可視化され、セキュリティの問題を解決してデータベースの防御機能を強化するための手順が含まれます。

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) では、SQL Server へのアクセスや SQL Server の悪用を試みる、害を及ぼす可能性のある通常とは異なる試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、異常なデータベース アクセス パターンに対してアクション指向のセキュリティ通知を提供します。 このようなアラートからは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨されるアクションが提示されます。


### <a name="support-for-custom-policies-preview"></a>カスタム ポリシーのサポート (プレビュー)

Azure Security Center でカスタム ポリシーがサポートされるようになりました (プレビュー)。

お客様は、Azure Policy で作成したポリシーに基づく独自のセキュリティ評価によって、Security Center の現在のセキュリティ評価の対象範囲を拡張することを求めてきました。 カスタム ポリシーのサポートにより、これが可能になりました。

これらの新しいポリシーは、Security Center の推奨事項エクスペリエンス、セキュリティ スコア、規制コンプライアンス標準ダッシュボードに含まれるようになります。 カスタム ポリシーのサポートにより、Azure Policy でカスタム イニシアチブを作成し、Security Center にポリシーとして追加して、推奨事項として表示できるようになりました。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>コミュニティおよびパートナー向けのプラットフォームで Azure Security Center のカバレッジを拡大

Security Center を使用して、Microsoft だけでなく、Check Point、Tenable、CyberArk などのパートナーの既存のソリューションからも推奨事項を受け取ることができます。今後、さらに多くの統合が提供される予定です。  Security Center のシンプルなオンボード フローにより、既存のソリューションを Security Center に接続することで、セキュリティ体制に関する推奨事項を 1 か所で表示し、統合レポートを実行することが可能になります。組み込みの推奨事項とパートナーの推奨事項の両方に対して、Security Center のすべての機能を活用することもできます。 また、Security Center の推奨事項をパートナー製品にエクスポートすることもできます。

Microsoft インテリジェント セキュリティ アソシエーションの詳細については、[こちら](https://www.microsoft.com/security/partnerships/intelligent-security-association)をご覧ください。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>推奨事項とアラートのエクスポートによる高度な統合 (プレビュー)

Security Center 上でエンタープライズ レベルのシナリオに対応するために、Azure portal または API 以外の場所でも Security Center のアラートと推奨事項を使用できるようになりました。 これらは、イベント ハブおよび Log Analytics ワークスペースに直接エクスポートできます。 これらの新しい機能を使用して作成できるワークフローは次のとおりです。

- Log Analytics ワークスペースにエクスポートすることにより、PowerBI でカスタム ダッシュボードを作成できます。
- イベント ハブにエクスポートすることにより、Security Center のアラートと推奨事項をサードパーティの SIEM、サードパーティ ソリューション、または Azure Data Explorer にエクスポートできるようになります。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Windows Admin Center から Security Center へのオンプレミス サーバーのオンボード (プレビュー)

Windows Admin Center は、Azure にデプロイされていない Windows サーバー用の管理ポータルであり、バックアップやシステム更新などの Azure 管理機能を提供します。 最近、これらの非 Azure サーバーをオンボードして、Windows Admin Center のエクスペリエンスから直接 ASC で保護する機能が追加されました。

この新しいエクスペリエンスにより、ユーザーは WAC サーバーを Azure Security Center にオンボードし、Windows Admin Center のエクスペリエンスでセキュリティのアラートや推奨事項を直接表示できるようになります。


## <a name="september-2019"></a>2019 年 9 月

9 月の更新プログラムには次のものが含まれます。

 - [適応型アプリケーション制御のルール管理の改善](#managing-rules-with-adaptive-application-controls-improvements)
 - [Azure Policy を使用してコンテナーのセキュリティの推奨事項を制御](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>適応型アプリケーション制御のルール管理の改善

適応型アプリケーション制御を使用した仮想マシンのルール管理のエクスペリエンスが改善されました。 Azure Security Center の適応型アプリケーション制御を使用すると、仮想マシン上で実行できるアプリケーションを制御できます。 ルール管理の全般的な改善に加え、新たに追加される機能により、新しいルールを追加するときに保護するファイルの種類を制御できるようになります。

適応型アプリケーション制御の詳細については、[こちら](adaptive-application-controls.md)をご覧ください。


### <a name="control-container-security-recommendation-using-azure-policy"></a>Azure Policy を使用してコンテナーのセキュリティの推奨事項を制御

コンテナーのセキュリティの脆弱性を修復するための Azure Security Center の推奨事項を、Azure Policy を使用して有効または無効にすることができるようになりました。

有効なセキュリティ ポリシーを表示するには、Security Center から [セキュリティ ポリシー] ページを開きます。


## <a name="august-2019"></a>2019 年 8 月

8 月の更新プログラムには次のものが含まれます。

 - [Azure Firewall 用 Just-In-Time (JIT) VM アクセス](#just-in-time-jit-vm-access-for-azure-firewall)
 - [セキュリティ体制を強化するシングル クリック修復 (プレビュー)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [テナント間の管理](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure Firewall 用 Just-In-Time (JIT) VM アクセス 

Azure Firewall 用 Just-In-Time (JIT) VM アクセスの一般提供が開始されました。 これを使用することで、NSG で保護された環境に加え、Azure Firewall で保護された環境もセキュリティで保護できます。

JIT VM アクセスでは、NSG と Azure Firewall の規則を使用して、必要な場合にのみ、VM への制御されたアクセスを提供することで、ネットワーク帯域幅消費型攻撃にさらされるリスクを低減します。

VM で JIT を有効にする場合は、保護するポート、ポートを開放しておく時間、これらのポートにアクセスできる承認済み IP アドレスを指定したポリシーを作成します。 このポリシーは、ユーザーがアクセスを要求したときに実行できる操作を制御するのに役立ちます。

要求は Azure アクティビティ ログに記録されるので、アクセスの監視と監査を簡単に行うことができます。 Just-In-Time ページは、JIT が有効になっている既存の VM や JIT が推奨される VM をすばやく特定する際にも役立ちます。

Azure Firewall の詳細については、[こちら](../firewall/overview.md)をご覧ください。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>セキュリティ体制を強化するシングル クリック修復 (プレビュー)

セキュリティ スコアは、ワークロードのセキュリティに対する姿勢を評価するのに役立つツールです。 セキュリティの推奨事項が見直され、優先順位が自動的に示されるので、どの推奨事項を最初に実行すべきかがわかります。 これは、最も重大なセキュリティの脆弱性を見つけて、調査に優先順位を付けるのに役立ちます。

セキュリティの構成の誤りを簡単に修復し、セキュリティ スコアを速やかに向上させるために、大量のリソースに関する推奨事項をシングル クリックで修復できる新しい機能が追加されました。

この操作により、ユーザーは修復を適用するリソースを選択し、ユーザーに代わって設定を構成する修復アクションを起動できます。

クイック修正が有効になっている推奨事項については、[セキュリティの推奨事項のリファレンス ガイド](recommendations-reference.md)をご覧ください。


### <a name="cross-tenant-management"></a>テナント間の管理

Security Center では、Azure Lighthouse の一部として、テナント間の管理のシナリオがサポートされるようになりました。 これにより、Security Center で複数のテナントのセキュリティ体制を可視化し、管理できるようになります。 

テナント間の管理エクスペリエンスの詳細については、[こちら](cross-tenant-management.md)をご覧ください。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>ネットワークの推奨事項の更新

Azure Security Center (ASC) で新しいネットワークの推奨事項が導入され、既存の推奨事項が改善されました。 Security Center を使用することで、リソースのネットワーク保護をさらに強化できます。 

ネットワークの推奨事項の詳細については、[こちら](recommendations-reference.md#recs-networking)をご覧ください。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>アダプティブ ネットワークのセキュリティ強化機能 - 一般提供の開始

パブリック クラウドで実行されているワークロードの最大の攻撃対象領域の 1 つは、パブリック インターネットとの間の接続です。 Azure ワークロードを必要なソース範囲でのみ利用できるようにするために、どのネットワーク セキュリティ グループ (NSG) 規則を設定すればよいかをお客様が把握するのは困難です。 この機能を使用すると、Security Center が Azure ワークロードのネットワーク トラフィックと接続のパターンを学習し、インターネットに接続する仮想マシンに対して、NSG 規則の推奨事項を提供します。 これにより、お客様は、ネットワーク アクセス ポリシーをより適切に構成し、攻撃にさらされるリスクを抑えることができます。 

アダプティブ ネットワークのセキュリティ強化機能の詳細については、[こちら](adaptive-network-hardening.md)をご覧ください。
