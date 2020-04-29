---
title: Azure セキュリティ ベンチマーク ブループリント サンプルのコントロール
description: Azure セキュリティ ベンチマーク ブループリント サンプルの Azure Policy に対するコントロール マッピング。
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538664"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Azure セキュリティ ベンチマーク ブループリント サンプルのコントロール マッピング

以下の記事は、Azure Blueprints Azure セキュリティ ベンチマーク ブループリント サンプルが Azure セキュリティ ベンチマーク コントロールにどのようにマップされているかを説明したものです。 コントロールの詳細については、[Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照してください。

以下のマッピングは、**Azure セキュリティ ベンチマーク**のコントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップされたコントロールは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されます。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 続いて、次を探して選択します: **[\[Preview\]:Audit Azure Security Benchmark recommendations and deploy specific supporting VM Extensions]\(Azure セキュリティ ベンチマークの推奨事項を監査し、特定のサポート VM 拡張機能をデプロイする\)** 組み込みポリシー イニシアティブ。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../../../policy/overview.md) 定義に関連します。 これらのポリシーは、コントロールに対する[コンプライアンスを評価](../../../policy/how-to/get-compliance-data.md)するのに役立つ場合があります。ただし、多くの場合、コントロールと 1 つまたは複数のポリシーとの間に 1:1 または完全な一致はありません。 そのため、Azure Policy での**準拠**は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれていることがあります。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス ブループリント サンプルのコントロールと Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md)に関するページを参照してください。

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

- サブネットはネットワーク セキュリティ グループに関連付けられている必要がある
- アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある
- 仮想マシンは、承認された仮想ネットワークに接続する必要がある
- インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある
- Service Bus は仮想ネットワーク サービス エンドポイントを使用する必要がある
- App Service は仮想ネットワーク サービス エンドポイントを使用する必要がある
- SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要がある
- イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要がある
- Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要がある
- Key Vault で仮想ネットワーク サービス エンドポイントを使用する必要がある
- ストレージ アカウントに対する制限のないネットワーク アクセスの監査
- ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要がある
- コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある
- 仮想ネットワークは、指定された仮想ネットワーク ゲートウェイを使用する必要があります
- Kubernetes Services では、許可する IP の範囲を定義する必要がある
- \[プレビュー\]:仮想マシンでの IP 転送を無効にする必要がある
- インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある
- 仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある
- 仮想マシンの管理ポートを閉じておく必要がある

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Vnet、サブネット、NIC の構成とトラフィックを監視してログに記録する

- Network Watcher を有効にする必要がある

## <a name="13-protect-critical-web-applications"></a>1.3 重要な Web アプリケーションを保護する

- Web アプリで "クライアント証明書 (着信クライアント証明書)" が [オン] に設定されていることを確認する
- CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない
- CORS で関数アプリへのアクセスをすべてのリソースには許可しない
- すべてのリソース API アプリにアクセスすることを CORS で許可しない
- Web アプリケーションのリモート デバッグを無効にする
- 関数アプリでリモート デバッグを無効にする必要がある
- API アプリでリモート デバッグを無効にする必要がある

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 既知の悪意のある IP アドレスとの通信を拒否する

- DDoS Protection Standard を有効にする必要がある
- 仮想マシンで Just-In-Time ネットワーク アクセス制御を適用する必要がある
- アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 ネットワーク パケットとフロー ログを記録する

- Network Watcher を有効にする必要がある

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

- "セキュリティ オプション - ネットワーク アクセス" の Windows VM 構成を監査するための前提条件をデプロイする
- "セキュリティ オプション - Microsoft ネットワーク クライアント" の Windows VM 構成の監査結果を表示する
- "セキュリティ オプション - ネットワーク セキュリティ" の Windows VM 構成を監査するための前提条件をデプロイする
- "セキュリティ オプション - ネットワーク セキュリティ" の Windows VM 構成の監査結果を表示する
- "セキュリティ オプション - Microsoft ネットワーク サーバー" の Windows VM 構成を監査するための前提条件をデプロイする
- "セキュリティ オプション - Microsoft ネットワーク サーバー" の Windows VM 構成の監査結果を表示する
- "管理用テンプレート - ネットワーク" の Windows VM 構成を監査するための前提条件をデプロイする
- "管理用テンプレート - ネットワーク" の Windows VM 構成の監査結果を表示する

## <a name="22-configure-central-security-log-management"></a>2.2 セキュリティ ログの一元管理を構成する

- 仮想マシンに Log Analytics エージェントをインストールする必要がある
- 仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある
- Log Analytics エージェントが適切に接続されていない Windows VM を監査するための前提条件をデプロイする
- Log Analytics エージェントが適切に接続されていない Windows VM からの監査結果を表示する
- Azure Monitor ログ プロファイルで、"書き込み"、"削除"、"アクション" の各カテゴリのログを収集する必要がある
- Azure Monitor ですべてのリージョンからアクティビティ ログを収集する必要がある
- 自分のサブスクリプションで Log Analytics 監視エージェントの自動プロビジョニングを有効にする必要がある

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Azure リソースの監査ログ記録を有効にする

- Azure Data Lake Store の診断ログを有効にする必要がある
- Logic Apps で診断ログを有効にする必要がある
- IoT Hub の診断ログを有効にする必要がある
- Batch アカウントで診断ログを有効にする必要がある
- 仮想マシン スケール セットの診断ログを有効にする必要がある
- イベント ハブの診断ログを有効にする必要がある
- Search サービスにおける診断ログを有効にする必要がある
- App Services の診断ログを有効にする必要がある
- Data Lake Analytics の診断ログを有効にする必要がある
- Key Vault で診断ログを有効にする必要がある
- Service Bus で診断ログを有効にする必要がある
- Azure Stream Analytics で診断ログを有効にする必要がある
- SQL Server の監査を有効にする必要があります
- 診断設定の監査

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 オペレーティング システムからセキュリティ ログを収集する

- 自分のサブスクリプションで Log Analytics 監視エージェントの自動プロビジョニングを有効にする必要がある
- 仮想マシンに Log Analytics エージェントをインストールする必要がある
- 仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある
- Log Analytics エージェントが適切に接続されていない Windows VM を監査するための前提条件をデプロイする
- Log Analytics エージェントが適切に接続されていない Windows VM からの監査結果を表示する

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 異常なアクティビティについてのアラートを有効にする

- Security Center の Standard 価格レベルを選択する必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- Advanced Data Security を SQL マネージド インスタンス上で有効にする必要がある
- SQL Server の Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある
- SQL マネージド インスタンスの Advanced Data Security 設定で、[Advanced Threat Protection の種類] を [すべて] に設定する必要がある

## <a name="28-centralize-anti-malware-logging"></a>2.8 マルウェア対策のログ記録を一元管理する

- Azure 向け Microsoft Antimalware は保護定義を自動的に更新するように構成する必要がある
- Endpoint Protection の欠落の Azure Security Center での監視
- エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 管理アカウントのインベントリを維持する

- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 専用管理者アカウントを使用する

- 指定されたメンバーだけで Administrators グループが構成されているわけではない Windows VM を監査する前提条件をデプロイする
- 指定されたメンバーだけで Administrators グループが構成されているわけではない Windows VM からの監査結果を表示する
- 指定されたメンバーの一部が Administrators グループに含まれていない Windows VM を監査する前提条件をデプロイする
- 指定されたメンバーの一部が Administrators グループに含まれていない Windows VM の監査結果を表示する
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM を監査する前提条件をデプロイする
- 指定されたメンバーのいずれかが Administrators グループに含まれている Windows VM からの監査結果を表示する
- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある
- 複数の所有者がサブスクリプションに割り当てられている必要がある

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 管理者アカウントからの疑わしいアクティビティに関するログとアラート

- Security Center の Standard 価格レベルを選択する必要がある

## <a name="39-use-azure-active-directory"></a>3.9 Azure Active Directory を使用する

- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある
- Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある
- API アプリで [Azure Active Directory に登録する] が有効になっていることを確認する
- Web アプリで [Azure Active Directory に登録する] が有効になっていることを確認する
- 関数アプリで [Azure Active Directory に登録する] が有効になっていることを確認する

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 ユーザー アクセスを定期的に確認して調整する

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 機密情報のインベントリを維持する

- SQL データベースの機密データを分類する必要がある

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 転送中のすべての機密情報を暗号化する

- ストレージ アカウントへの安全な転送を有効にする必要がある
- API アプリでは最新の TLS バージョンを使用する必要がある
- Web アプリでは最新の TLS バージョンを使用する必要がある
- 関数アプリでは最新の TLS バージョンを使用する必要がある
- Function App には HTTPS 経由でのみアクセスできるようにする
- Web アプリケーションには HTTPS を介してのみアクセスできるようにする
- API アプリには HTTPS を介してのみアクセスできるようにする
- MySQL データベース サーバーで [SSL 接続を強制する] が有効でなければならない
- PostgreSQL データベース サーバーで [SSL 接続を強制する] が有効でなければならない
- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 アクティブ検出ツールを使用して機密データを特定する

- SQL データベースの機密データを分類する必要がある
- Advanced Data Security を、SQL サーバー上で有効にする必要がある
- Advanced Data Security を SQL マネージド インスタンス上で有効にする必要がある

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Azure RBAC を使用してリソースへのアクセスを制御する

- Kubernetes サービスでロールベースのアクセス制御 (RBAC) を使用する必要がある
- カスタム RBAC 規則の使用監査

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 機密情報を保存時に暗号化する

- SQL データベースで Transparent Data Encryption を有効にする必要がある
- 仮想マシンでディスク暗号化を適用する必要がある
- アタッチされていないディスクを暗号化する必要がある
- SQL Server の TDE 保護機能を自分のキーを使用して暗号化する必要がある
- SQL マネージド インスタンスの TDE 保護機能を自分のキーを使用して暗号化する必要がある
- Automation アカウント変数は、暗号化する必要がある
- Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 重要な Azure リソースへの変更に関するログとアラート

- Azure Monitor ですべてのリージョンからアクティビティ ログを収集する必要がある

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 自動化された脆弱性スキャン ツールを実行する

- 脆弱性評価を SQL サーバー上で有効にする必要がある
- 脆弱性評価を SQL マネージド インスタンス上で有効にする必要がある
- \[プレビュー\] Virtual Machines で脆弱性評価を有効にする必要がある
- SQL サーバーの脆弱性評価の設定には、スキャン レポートを受信するためのメール アドレスが含まれている必要がある

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

- システム更新プログラムをマシンにインストールする必要がある
- 仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある
- 関数アプリの一部として使用された ".NET Framework" のバージョンが最新であることを確認する
- Web アプリの一部として使用された ".NET Framework" のバージョンが最新であることを確認する
- API アプリの一部として使用された ".NET Framework" のバージョンが最新であることを確認する

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 自動化されたサードパーティ ソフトウェア修正プログラム管理ソリューションをデプロイする

- API アプリの一部として使用された "PHP のバージョン" が最新であることを確認する
- Web アプリの一部として使用された "PHP のバージョン" が最新であることを確認する
- 関数アプリの一部として使用された "PHP のバージョン" が最新であることを確認する
- Web アプリの一部として使用された "Java のバージョン" が最新であることを確認する
- 関数アプリの一部として使用された "Java のバージョン" が最新であることを確認する
- API アプリの一部として使用された "Java のバージョン" が最新であることを確認する
- Web アプリの一部として使用された "Python のバージョン" が最新であることを確認する
- 関数アプリの一部として使用された "Python のバージョン" が最新であることを確認する
- API アプリの一部として使用された "Python のバージョン" が最新であることを確認する
- Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

- 脆弱性評価ソリューションによって脆弱性を修復する必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- SQL データベースの脆弱性を修復する必要がある

## <a name="68-use-only-approved-applications"></a>6.8 承認されたアプリケーションのみを使用する

- Security Center の Standard 価格レベルを選択する必要がある
- 適応型アプリケーション制御を仮想マシンで有効にする必要がある

## <a name="69-use-only-approved-azure-services"></a>6.9 承認された Azure サービスのみを使用する

- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある
- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある

## <a name="610-implement-approved-application-list"></a>6.10 承認されたアプリケーションの一覧を実装する

- Security Center の Standard 価格レベルを選択する必要がある
- 適応型アプリケーション制御を仮想マシンで有効にする必要がある

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 セキュリティで保護された Azure リソースの構成を維持する

- \[プレビュー\]:Kubernetes Services ではポッドのセキュリティ ポリシーを定義する必要がある

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 セキュリティで保護されたオペレーティング システムの構成を維持する

- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Azure サービスの自動構成監視を実装する

- \[プレビュー\]:Kubernetes Services ではポッドのセキュリティ ポリシーを定義する必要がある

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 オペレーティング システムの自動構成監視を実装する

- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- コンテナーのセキュリティ構成の脆弱性を修復する必要がある
- 仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある

## <a name="711-manage-azure-secrets-securely"></a>7.11 Azure シークレットを安全に管理する

- キー コンテナー オブジェクトが回復可能でなければならない

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 ID を安全かつ自動的に管理する

- 関数アプリではマネージド ID を使用する必要がある
- Web アプリではマネージド ID を使用する必要がある
- API アプリではマネージド ID を使用する必要がある

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 一元管理されるマルウェア対策ソフトウェアを使用する

- Endpoint Protection の欠落の Azure Security Center での監視
- エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

- Security Center の Standard 価格レベルを選択する必要がある

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 マルウェア対策ソフトウェアと署名が確実に更新されるようにする

- Azure 向け Microsoft Antimalware は保護定義を自動的に更新するように構成する必要がある

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 定期的な自動バックアップを保証する

- Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある
- Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある
- Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある
- Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある
- Virtual Machines に対して Azure Backup を有効にする必要がある

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

- Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある
- Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある
- Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある
- Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある
- Virtual Machines に対して Azure Backup を有効にする必要がある

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 バックアップとカスタマー マネージド キーの保護を保証する

- キー コンテナー オブジェクトが回復可能でなければならない

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 インシデントのスコアリングと優先順位付けの手順を作成する

- Security Center の Standard 価格レベルを選択する必要がある

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成する

- 自分のサブスクリプションに対し、セキュリティ連絡先のメール アドレスを指定する必要がある
- 自分のサブスクリプションに対し、セキュリティ連絡先の電話番号を指定する必要がある
- SQL Server の高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL マネージド インスタンスの高度なデータ セキュリティ設定に、セキュリティ アラートを受信するためのメール アドレスが含まれている必要がある
- SQL Server の Advanced Data Security 設定で、管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある
- SQL マネージド インスタンスの Advanced Data Security 設定で管理者とサブスクリプションの所有者に対するメール通知を有効にする必要がある

## <a name="next-steps"></a>次のステップ

Azure セキュリティ ベンチマーク ブループリントのコントロール マッピングを確認したので、Azure portal の Azure Policy にアクセスしてイニシアティブを割り当ててください。

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。