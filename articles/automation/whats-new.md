---
title: Azure Automation の新着情報
description: 毎月更新される Azure Automation に対する重要な更新。
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 84644759a3f2f887662faae686814f032410266b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123107935"
---
# <a name="whats-new-in-azure-automation"></a>Azure Automation の新着情報

Azure Automation には継続的に改善が施されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[Azure Automation の新着情報のアーカイブ](whats-new-archive.md)」を参照してください。

## <a name="august-2021"></a>2021 年 8 月

### <a name="azure-policy-guest-configuration"></a>Azure Policy ゲスト構成

**種類:** 変更の計画

お客様は、Azure Automation State Configuration から Azure Policy ゲスト構成への移行を評価し、計画する必要があります。 詳細については、[Azure Policy ゲストの構成](../governance/policy/concepts/guest-configuration.md)に関するページを参照してください。

## <a name="july-2021"></a>2021 年 7 月

### <a name="preview-support-for-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID のプレビュー サポート

**種類:** 新機能

Azure Automation で、Azure グローバル、Azure Government、Azure China リージョンのクラウド ジョブに対して、[ユーザー割り当てマネージド ID](automation-secure-asset-encryption.md) がサポートされるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/)を参照してください。

### <a name="general-availability-of-customer-managed-keys-for-azure-automation"></a>Azure Automation のカスタマー マネージド キーの一般提供

**種類:** 新機能

Azure Automation 資産の暗号化を、お客様が各自のマネージド キーを使用して管理および保護することができます。 カスタマー マネージド キーの導入により、Azure Key Vault 内で作成して管理するキーを使用すると、既定の暗号化を追加の暗号化レイヤーで補完することができます。 この追加の暗号化は、組織の規制またはコンプライアンスのニーズを満たすのに役立ちます。

詳細については、[カスタマー マネージド キーの使用](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)に関するセクションを参照してください。

## <a name="june-2021"></a>2021 年 6 月

### <a name="security-update-for-log-analytics-contributor-role"></a>Log Analytics 共同作成者のセキュリティ更新プログラム

**種類:** 変更の計画

Microsoft は、Log Analytics 共同作成者ロールから Automation アカウントの権限を削除する予定です。 現在、組み込みの [Log Analytics 共同作成者](./automation-role-based-access-control.md#log-analytics-contributor) ロールは特権をサブスクリプションの [Contributor](./../role-based-access-control/built-in-roles.md#contributor) ロールに昇格させることができます。 Automation アカウントの実行アカウントは最初、サブスクリプションの共同作成者権限で構成されるため、攻撃者がそれを利用し、サブスクリプションの共同作成者として新しい Runbook を作成し、コードを実行できます。

このセキュリティ リスクの結果として、Log Analytics 共同作成者ロールを利用して Automation ジョブを実行しないことをお勧めします。 代わりに、Azure Automation 共同作成者カスタム ロールを作成し、Automation アカウント関連のアクションに利用してください。 実装手順については、[Azure Automation 共同作成者カスタム ロール](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)に関するページを参照してください。

### <a name="support-for-automation-and-state-configuration-available-in-west-us-3"></a>米国西部 3 で利用可能な Automation と State Configuration のサポート

**種類:** 新機能

詳細については、「[Azure でのデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)」を参照して、ドロップダウン リストから該当する地域を選択してください。

## <a name="may-2021"></a>2021 年 5 月

### <a name="startstop-vms-during-off-hours-v1"></a>Start/Stop VMs during off-hours (v1)

**種類:** 変更の計画

Start/Stop VMs during off-hours (v1) は、2022 年 5 月 21 日に非推奨となる予定です。 お客様は、Start/Stop VMs v2 (プレビュー) への移行を評価し、計画を立てる必要があります。 詳細については、「[Start/Stop VMs v2 (プレビュー) の概要](../azure-functions/start-stop-vms/overview.md)」を参照してください。

## <a name="april-2021"></a>2021 年 4 月

### <a name="support-for-update-management-and-change-tracking"></a>Update Management と Change Tracking のサポート

**種類:** 新機能

リージョン マッピングが更新され、ノルウェー東部、アラブ首長国連邦北部、米国中北部、ブラジル南部、韓国中部で Update Management と Change Tracking がサポートされるようになりました。 詳細については、「[サポートされるマッピング](./how-to/region-mappings.md#supported-mappings)」を参照してください。

### <a name="support-for-system-assigned-managed-identities"></a>システム割り当てマネージド ID のサポート

**種類:** 新機能

Azure Automation で、Azure グローバルおよび Azure Government リージョンのクラウドとハイブリッド ジョブに対して、[システム割り当てマネージド ID](./automation-security-overview.md#managed-identities-preview) がサポートされるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/)を参照してください。

## <a name="march-2021"></a>2021 年 3 月

### <a name="new-azure-automation-built-in-policy-definitions"></a>Azure Automation の新しい組み込みポリシー定義

**種類:** 新機能

Azure Automation に 5 つの新しい組み込みポリシー定義が追加されました。

- Automation アカウントで公衆ネットワーク アクセスを無効にする必要がある
- Azure Automation アカウントでは、保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- 公衆ネットワーク アクセスを無効にするように Azure Automation アカウントを構成する
- Azure Automation アカウントでのプライベート エンドポイント接続の構成
- Automation アカウントでプライベート エンドポイント接続を有効にする必要がある。

詳細については、[Azure Policy リファレンス](./policy-reference.md)に関するページを参照してください。

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>インド南部での GA を宣言された Automation と State Configuration のサポート

**種類:** 新機能

インド南部でプロセスの自動化および State Configuration 機能を使用できます。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/)を参照してください。

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>英国西部での GA を宣言された Automation と State Configuration のサポート

**種類:** 新機能

英国西部でプロセスの自動化および State Configuration 機能を使用できます。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/)を参照してください。

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>アラブ首長国連邦中部での GA を宣言された Automation と State Configuration のサポート

**種類:** 新機能

アラブ首長国連邦中部でプロセスの自動化および State Configuration 機能を使用できます。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/)を参照してください。

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2-norway-west-and-france-south"></a>オーストラリア中部 2、ノルウェー西部、フランス南部で利用可能な Automation と State Configuration のサポート

**種類:** 新機能

各リージョンの地域を選択して、[データ所在地に関するページ](https://azure.microsoft.com/global-infrastructure/data-residency/)で詳細を確認してください。

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Windows および Linux にハイブリッドワーカーをインストールするための新しいスクリプトを追加

**種類:** 新機能

Windows または Linux マシンで Hybrid Runbook Worker を設定する Azure Automation の主要なシナリオの 1 つに対応する 2 つの新しいスクリプトが Azure Automation [GitHub リポジトリ](https://github.com/azureautomation)に追加されました。 このスクリプトでは、新しい VM を作成するか既存の VM を使用し、必要に応じて Log Analytics ワークスペースを作成します。さらに、Windows 用の Log Analytics エージェントまたは Linux 用の Log Analytics エージェントをインストールし、マシンを Log Analytics ワークスペースに登録します。 Windows 用スクリプトの名前は **Create Automation Windows HybridWorker** です。Linux 用スクリプトの名前は **Create Automation Linux HybridWorker** です。

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Azure Resource Manager テンプレート Webhook を介した Runbook の呼び出し

**種類:** 新機能

詳細については、「[ARM テンプレートから Webhook を使用する](./automation-webhooks.md#create-runbook-and-webhook-with-arm-template)」を参照してください。

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Update Management で、Centos 8.x、Red Hat Enterprise Linux Server 8.x、および SUSE Linux Enterprise Server 15 がサポートされるようになりました

**種類:** 新機能

詳細については、サポートされている Linux オペレーティングシステムの[完全な一覧](./update-management/operating-system-requirements.md)を参照してください。

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>ブラジル南部および東南アジアでのリージョン内データ所在地のサポート

**種類:** 新機能

ブラジル南部と東南アジアを除くすべてのリージョンにおいて、Azure Automation データは、ビジネス継続性とディザスター リカバリー (BCDR) を提供するために別のリージョン (Azure のペアになっているリージョン) に格納されます。 ブラジルおよび東南アジア リージョンの場合のみ、Azure Automation データは、これらのリージョンのデータ所在地の要件に対応するために同じリージョンに格納されるようになりました。 詳細については、「[Azure Automation での geo レプリケーション](./automation-managing-data.md#geo-replication-in-azure-automation)」を参照してください。

## <a name="february-2021"></a>2021 年 2 月

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>西日本での GA を宣言された Automation と State Configuration のサポート

**種類:** 新機能

Automation アカウントと State Configuration が西日本リージョンで利用できるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/)を参照してください。

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>ハイブリッド worker で Runbook の実行を強制するためのカスタム Azure Policy コンプライアンスの導入

**種類:** 新機能

新しい Azure Policy コンプライアンス規則を使用すると、ハイブリッド worker グループでのみ実行されるジョブ、Webhook、ジョブ スケジュールを作成することができます。

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>米国東部、フランス中部、北ヨーロッパのリージョンで使用可能になった Update Management

**種類:** 新機能

Automation Update Management 機能を、米国東部、フランス中部、北ヨーロッパのリージョンで使用できるようになりました。 この変更を反映するドキュメントの最新版については、[サポートされるリージョン マッピング](how-to/region-mappings.md)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Automation のドキュメント作成に協力していただける方は、[Docs 共同作成者ガイド](/contribute/)に関するページを参照してください。
