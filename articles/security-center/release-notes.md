---
title: Azure Security Center のリリース ノート
description: Azure Security Center の新機能と変更点の説明
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 06/14/2021
ms.author: memildin
ms.openlocfilehash: 62fb48fb7517f25ceaf2b1e922ece83538157f90
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238044"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center の最新情報

Security Center のセキュリティは精力的な開発の下、継続的に改善されています。 常に最新の開発情報を把握していただけるよう、このページでは新しい機能、バグの修正、非推奨になった機能に関する情報を提供します。

このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 

Security Center で近日中に公開を "*予定されている*" 変更については、「[Azure Security Center への今後予定されている重要な変更](upcoming-changes.md)」を参照してください。 

> [!TIP]
> 6 か月以上前の項目を探す場合は、「[Azure Security Center の最新情報のアーカイブ](release-notes-archive.md)」をご覧ください。


## <a name="june-2021"></a>2021 年 6 月

6 月の更新プログラムには次のものが含まれます。

- [Azure Defender for Key Vault の新しいアラート](#new-alert-for-azure-defender-for-key-vault)
- [既定では無効になっているカスタマー マネージド キー (CMK) を使用して暗号化するための推奨事項](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Kubernetes アラートのプレフィックスを "AKS_" から "K8S_" に変更](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Azure Defender for Key Vault の新しいアラート

Azure Defender for Key Vault で提供される脅威の保護を拡張するために、次のアラートを追加しました。

| アラート (アラートの種類)                                                                 | 説明                                                                                                                                                                                                                                                                                                                                                      | MITRE の方針 | 重大度 |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| Access from a suspicious IP address to a key vault (疑わしい IP アドレスからのキー コンテナーへのアクセス)<br>(KV_SuspiciousIPAccess)  | キー コンテナーが、Microsoft 脅威インテリジェンスによって疑わしい IP アドレスとして識別された IP によって正常にアクセスされました。 これは、お使いのインフラストラクチャが侵害を受けたことを示しているおそれがあります。 さらに詳しく調査することをお勧めします。 [Microsoft の脅威インテリジェンス機能](https://go.microsoft.com/fwlink/?linkid=2128684)の詳細に関するページを参照してください。 | 資格情報アクセス                            | Medium   |
|||

詳細については、次を参照してください。
- [Azure Defender for Key Vault の概要](defender-for-resource-manager-introduction.md)
- [Azure Defender の Key Vault アラートに対応する](defender-for-key-vault-usage.md)
- [Azure Defender for Key Vault で提供されるアラートの一覧](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>既定では無効になっているカスタマー マネージド キー (CMK) を使用して暗号化するための推奨事項

Security Center には、カスタマー マネージド キーを使用して保存データを暗号化するための次のような複数の推奨事項があります。

- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある

Azure のデータは、プラットフォーム マネージド キーを使用して自動的に暗号化されるため、カスタマー マネージド キーの使用は、組織が適用を選択した特定のポリシーに準拠するために必要な場合にのみ適用する必要があります。

この変更に伴い、CMK を使用するための推奨事項は、**既定では無効** になりました。 組織に関係がある場合は、対応するセキュリティ ポリシーの *Effect* パラメーターを **AuditIfNotExists** または **Enforce** に変更することにより、有効にすることができます。 詳細については、「[セキュリティ ポリシーの有効化](tutorial-security-policy.md#enable-a-security-policy)」を参照してください。

この変更は、次の例で示すように、新しいプレフィックス **[必要に応じて有効にする]** を使用して推奨事項の名前に反映されています。

- [必要に応じて有効にする] ストレージ アカウントでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある
- [必要に応じて有効にする] コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- [必要に応じて有効にする] Cosmos DB アカウントでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="Security Center の CMK に関する推奨事項は、既定では無効になります。" lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Kubernetes アラートのプレフィックスを "AKS_" から "K8S_" に変更

オンプレミスおよびマルチ クラウド環境でホストされている Kubernetes クラスターを保護するため、Azure Defender for Kubernetes が最近拡張されました。 詳細については、「[Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護 (プレビュー)](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)」をご覧ください。

Azure Defender for Kubernetes によって提供されるセキュリティ アラートが Azure Kubernetes Service 上のクラスターに制限されなくなったという事実を反映するために、アラートの種類のプレフィックスを "AKS_" から "K8S_" に変更しました。 必要に応じて、名前と説明も更新されています。 たとえば、このアラートです。

|アラート (アラートの種類)|説明|
|----|----|
|Kubernetes penetration testing tool detected (Kubernetes 侵入テスト ツールが検出されました)<br>(**AKS** _PenTestToolsKubeHunter)|Kubernetes 監査ログ分析によって、**AKS** クラスターでの Kubernetes 侵入テスト ツールの使用が検出されました。 この動作は、正当である可能性もありますが、攻撃者はこのような公開ツールを悪意のある目的で使用する可能性があります。
|||

変更後:

|アラート (アラートの種類)|説明|
|----|----|
|Kubernetes penetration testing tool detected (Kubernetes 侵入テスト ツールが検出されました)<br>(**K8S** _PenTestToolsKubeHunter)|Kubernetes 監査ログ分析によって、**Kubernetes** クラスターでの Kubernetes 侵入テスト ツールの使用が検出されました。 この動作は、正当である可能性もありますが、攻撃者はこのような公開ツールを悪意のある目的で使用する可能性があります。|
|||

"AKS_" から始まるアラートを参照する抑制ルールは自動的に変換されています。 SIEM エクスポート、またはアラートの種類別に Kubernetes アラートを参照するカスタム自動化スクリプトを設定している場合は、それらを新しいアラートの種類で更新する必要があります。

Kubernetes アラートの完全な一覧については、[Kubernetes クラスターのアラート](alerts-reference.md#alerts-k8scluster)に関するセクションをご覧ください。

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化

次の 2 つの推奨事項は非推奨になりました。

- **クラウド サービス ロールの OS バージョンを更新する必要がある** - Azure では既定で、ゲスト OS は、サービス構成 (.cscfg) に指定した Windows Server 2016 などの OS ファミリ内でサポートされている最新のイメージに定期的に更新されます。
- **Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある** - この推奨事項の評価は、目標とする広範囲には及んでいません。 推奨事項は、よりお客様のセキュリティ ニーズに合った強化バージョンに置き換えられる予定です。


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
- **脅威インテリジェンスベースの検出** - Azure Defender では、Microsoft の脅威インテリジェンスと膨大なナレッジ ベースを活用して脅威アラートが表示されるため、それらに対処することができます。

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

Security Center のすべての推奨事項には、 **[クエリを開く]** から Azure Resource Graph を使用して、影響を受けるリソースの状態に関する情報を表示するためのオプションがあります。 この強力な機能の詳細については、「[Azure Resource Graph Explorer (ARG) で推奨事項データを確認する](security-center-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg)」を参照してください。

Security Center には、VM、SQL サーバーとそのホスト、コンテナー レジストリでセキュリティ上の脆弱性をスキャンするための脆弱性スキャンが組み込まれています。 結果は、各リソースの種類ごとに個別のすべての結果が単一のビューに収集され、推奨事項として返されます。 推奨事項は次のとおりです。

- Azure Container Registry イメージの脆弱性を修復する必要がある (Qualys を利用)
- 仮想マシンの脆弱性を修復する必要がある
- SQL データベースでは脆弱性の検出結果を解決する必要がある
- マシン上の SQL サーバーでは脆弱性の検出結果を解決する必要がある

この変更に伴い、 **[クエリを開く]** ボタンを使用して、セキュリティの結果を示すクエリを開くこともできます。

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="[クエリを開く] ボタンを使用して、より詳細なクエリを実行し、脆弱性スキャナー関連の推奨事項に関するセキュリティの結果を表示できるようになりました。":::

**[クエリを開く]** ボタンを使用すると、関連するその他の推奨事項に関する追加のオプションも表示できます。

Security Center の脆弱性スキャナーの詳細については、次のページを参照してください。

- [Azure およびハイブリッドのマシンに対する Azure Defender の統合された脆弱性評価スキャナー](deploy-vulnerability-assessment-vm.md)
- [SQL サーバーに対して Azure Defender の統合された脆弱性評価スキャナーを使用する](defender-for-sql-on-machines-vulnerability-assessment.md)
- [コンテナー レジストリ向け Azure Defender の統合された脆弱性評価スキャナー](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>SQL データ分類の推奨事項の重要度を変更

推奨事項 "**SQL データベースの機密データを分類する必要がある**" の重要度が **高** から **低** に変更されました。

これは、「[SQL データ分類の推奨事項を改善](upcoming-changes.md#enhancements-to-sql-data-classification-recommendation)」で発表された、この推奨事項に対する継続的な変更の一環として行われたものです。 


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

リソースに関する詳細情報と、そのリソースに適用されるすべての推奨事項を確認できます。 また、[Azure Defender](azure-defender.md) を使用している場合は、その特定のリソースに関する未処理のセキュリティ アラートも表示されます。

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

Azure Defender for Kubernetes は、クラスターがどこにデプロイされていても防御できるように、脅威保護機能が拡張されています。 これは、[Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) とその新しい[拡張機能](../azure-arc/kubernetes/extensions.md)との統合によって実現されています。 

非 Azure Kubernetes クラスターで Azure Arc を有効にすると、Azure Security Center の新しい推奨事項として、わずか数回のクリックで Azure Defender 拡張機能をデプロイすることが提案されます。

推奨事項 (**Azure Arc 対応 Kubernetes クラスターには Azure Defender の拡張機能がインストールされている必要がある**) と拡張機能を使用して、(マネージド Kubernetes サービス上ではなく) 他のクラウド プロバイダーにデプロイされた Kubernetes クラスターを保護します。

Azure Security Center、Azure Defender、および Azure Arc 対応 Kubernetes の間のこの統合により、次のことが実現されます。

- 保護されていない Azure Arc 対応 Kubernetes クラスターに対して Azure Defender 拡張機能を簡単にプロビジョニングする (手動および大規模)
- Azure Arc ポータルから Azure Defender 拡張機能とそのプロビジョニングの状態を監視する
- Security Center からのセキュリティに関する推奨事項が、Azure Arc ポータルの新しい [セキュリティ] ページでレポートされる
- Azure Defender で特定されたセキュリティ上の脅威が、Azure Arc ポータルの新しい [セキュリティ] ページでレポートされる
- Azure Arc 対応 Kubernetes クラスターが、Azure Security Center プラットフォームおよびエクスペリエンスに統合される

詳細については、[オンプレミスおよびマルチクラウドの Kubernetes クラスターでの Azure Defender for Kubernetes の使用](defender-for-kubernetes-azure-arc.md)に関する記事をご覧ください。

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 対応 Kubernetes クラスター用の Azure Defender 拡張機能をデプロイするための Azure Security Center の推奨事項。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>Azure Defender と Microsoft Defender for Endpoint の統合で、Windows Server 2019 および Windows 10 Virtual Desktop (WVD) をサポート (一般提供 (GA) リリース)

Microsoft Defender for Endpoint は、クラウドで提供される包括的なエンドポイント セキュリティ ソリューションです。 リスクベースによる脆弱性の管理と評価、およびエンドポイントでの検出と対応 (EDR) を提供します。 Defender for Endpoint を Azure Security Center と併用するメリットの全容については、「[Security Center に統合された EDR ソリューション Microsoft Defender for Endpoint でエンドポイントを保護する](security-center-wdatp.md)」を参照してください。

Windows サーバー上のサーバーに Azure Defender を有効にした場合、プランに Defender for Endpoint のライセンスが含まれています。 サーバーに対して既に Azure Defender を有効にしており、サブスクリプションに Windows 2019 サーバーが存在する場合には、今回の更新により自動的に Defender for Endpoint が追加されます。 特に何か操作をする必要はありません。 

このたびサポートが拡張され、Windows Server 2019 と [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) が含まれるようになりました。

> [!NOTE]
> Windows Server 2019 のマシンで Defender for Endpoint を有効にする場合には、マシンが「[Microsoft Defender for Endpoint 統合を有効にする](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)」に記載されている前提条件を満たしていることを確認してください。


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>Azure Defender for DNS および Azure Defender for Resource Manager を有効にする際の推奨事項 (プレビュー)

[Azure defender For Resource Manager](defender-for-resource-manager-introduction.md) と [Azure Defender for DNS](defender-for-dns-introduction.md) を有効にするプロセスを簡略化するために、2 つの新しい推奨事項が追加されました。

- **Azure Defender for Resource Manager を有効にする必要がある** - Defender for Resource Manager は組織内のリソース管理操作を自動的に監視します。 Azure Defender では、脅威を検出し、疑わしいアクティビティについて警告します。
- **Azure Defender for DNS を有効にする必要がある** - Defender for DNS では、Azure リソースからのすべての DNS クエリを継続的に監視することにより、クラウド リソースに対して追加の保護層を提供します。 Azure Defender では、DNS 層での不審なアクティビティについて警告します。

Azure Defender プランを有効にすると、料金が発生します。 リージョンごとの料金の詳細については、Security Center の価格に関するページ https://aka.ms/pricing-security-center を参照してください。

> [!TIP]
> プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」を参照してください。


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>追加された 3 つの規制コンプライアンス標準: Azure CIS 1.3.0、CMMC レベル 3、New Zealand ISM Restricted

Azure Security Center で使用するための 3 つの標準が追加されました。 規制コンプライアンス ダッシュボードを使用して、次の標準への準拠を追跡できるようになりました。

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC レベル 3](../governance/policy/samples/cmmc-l3.md)
- [New Zealand ISM Restricted](../governance/policy/samples/new-zealand-ism.md)

「[規制コンプライアンス ダッシュボードで標準セットをカスタイマイズする](update-regulatory-compliance-packages.md)」で説明するように、これらをサブスクリプションに割り当てることができます。

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Azure Security Center の規制コンプライアンス ダッシュボードで使用するために追加された 3 つの標準。" lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

詳細情報:
- [規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ](update-regulatory-compliance-packages.md)
- [チュートリアル:規制に対するコンプライアンスの向上](security-center-compliance-dashboard.md)
- [FAQ - 規制コンプライアンス ダッシュボード](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

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

この新しい統合を使用すると、Security Center のギャラリーから面倒な設定の要らないテンプレートを使えるようになります。 ブック テンプレートを使用すると、組織のセキュリティ体制を追跡するための動的なビジュアル レポートを閲覧したり、作成したりできます。 また、Security Center のデータをはじめとするサポートされているデータ型に基づいて新しいブックを作成したり、Security Center の GitHub コミュニティから入手したコミュニティ ワークブックをすばやくデプロイしたりすることもできます。

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

詳細については、[Azure Security Center でのセキュリティに関する推奨事項](security-center-recommendations.md)を参照してください。


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
- **サンプルのアラート機能の作成** - 新しいアラート エクスペリエンスでサンプル アラートを作成するには、「[Azure Defender アラートのサンプルを生成する](security-center-alert-validation.md#generate-sample-azure-defender-alerts)」を参照してください。

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center のセキュリティ アラートの一覧":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes ワークロード保護の推奨事項が一般提供 (GA) リリース

Kubernetes ワークロード保護に関する一連の推奨事項が一般提供 (GA) されたことをお知らせします。

Security Center では、Kubernetes ワークロードが既定で確実に保護されるように、Kubernetes 受付制御を含む適用オプションなど、Kubernetes レベルの強化の推奨事項が追加されました。

Azure Kubernetes Service (AKS) クラスターに Kubernetes 用 Azure Policy アドオンをインストールすると、Kubernetes API サーバーに対するすべての要求が、クラスターに保存される前に、事前定義された一連のベスト プラクティス (13 個のセキュリティ推奨事項として表示されます) を基準にして監視されます。 その後、ベスト プラクティスを適用し、それを将来のワークロードに対して要求するように構成できます。

たとえば、特権コンテナーが作成されないように要求することができます。また、今後の特権コンテナー作成要求はすべてブロックされます。

詳細については、[Kubernetes 受付制御を使用したワークロード保護のベスト プラクティス](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)に関するページを参照してください。

> [!NOTE]
> 推奨事項がプレビュー段階の間、これらによって AKS クラスター リソースが異常な状態にあると表示されることはありませんでした。これらの推奨事項は、セキュリティ スコアの計算には含まれていませんでした。 このたびの GA の発表により、これらがスコア計算に含まれるようになります。 その修復がまだ済んでいない場合、これが原因でセキュア スコアに若干の影響が生じる可能性があります。 「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」の説明に従って、可能な限りそれらを修復してください。


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Azure Defender に統合した Microsoft Defender for Endpoint が、Windows Server 2019 および Windows 10 Virtual Desktop (WVD) をサポート (プレビュー)

Microsoft Defender for Endpoint は、クラウドで提供される包括的なエンドポイント セキュリティ ソリューションです。 リスクベースによる脆弱性の管理と評価、およびエンドポイントでの検出と対応 (EDR) を提供します。 Defender for Endpoint を Azure Security Center と併用するメリットの全容については、「[Security Center に統合された EDR ソリューション Microsoft Defender for Endpoint でエンドポイントを保護する](security-center-wdatp.md)」を参照してください。

Windows サーバー上のサーバーに Azure Defender を有効にした場合、プランに Defender for Endpoint のライセンスが含まれています。 サーバーに対して既に Azure Defender を有効にしており、サブスクリプションに Windows 2019 サーバーが存在する場合には、今回の更新により自動的に Defender for Endpoint が追加されます。 特に何か操作をする必要はありません。 

このたびサポートが拡張され、Windows Server 2019 と [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md) が含まれるようになりました。

> [!NOTE]
> Windows Server 2019 のマシンで Defender for Endpoint を有効にする場合には、マシンが「[Microsoft Defender for Endpoint 統合を有効にする](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)」に記載されている前提条件を満たしていることを確認してください。

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

すべての Azure サービスには、対応するドキュメントにセキュリティ ベースライン ページが用意されています。 たとえば、[こちらは Security Center のベースライン](security-baseline.md)です。 それらのベースラインは、Azure セキュリティ ベンチマークに基づいて作成されています。

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

[ハイブリッド マシンへの統合された脆弱性スキャナーのデプロイについての詳細を参照してください](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[Azure Arc 対応のサーバーについての詳細を参照してください](../azure-arc/servers/index.yml)。


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

Security Center のメニューから、 **[マルチ クラウド コネクタ]** を選択すると、新しいコネクタを作成するためのオプションが表示されます。

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Security Center のマルチ クラウド コネクタ ページの [Add AWS account]\(AWS アカウントの追加\) ボタン":::

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

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="テナント全体のアクセス許可を要求できることをユーザーに通知するバナー。":::

詳細については、「[テナント全体のアクセス許可が不十分な場合に要求する](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)」を参照してください。


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure セキュリティ ベンチマークのカバレッジを広げるために追加される、35 個のプレビュー推奨事項

[Azure セキュリティ ベンチマーク](/security/benchmark/azure/introduction)が Azure Security Center の既定のポリシー イニシアティブになりました。 

このベンチマークのカバー範囲を拡大するために、次の 35 個のプレビュー推奨事項が Security Center に追加されています。

> [!TIP]
> プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」を参照してください。

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

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="フィルター処理された推奨事項を CSV ファイルにエクスポートする。":::

詳細については、[Azure Security Center でのセキュリティに関する推奨事項](security-center-recommendations.md)を参照してください。


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