---
title: Azure Automation の新着情報
description: 毎月更新される Azure Automation に対する重要な更新。
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions
ms.openlocfilehash: ea18171bdd957781e22743c3e59690fc8d4703f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432436"
---
# <a name="whats-new-in-azure-automation"></a>Azure Automation の新着情報

Azure Automation には継続的に改善が施されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[Azure Automation の新着情報のアーカイブ](whats-new-archive.md)」を参照してください。

## <a name="october-2021"></a>2021 年 10 月

### <a name="preview-support-for-hybrid-runbook-worker-extension-for-azure-vms-and-arc-enabled-servers"></a>Hybrid Runbook Worker の Azure VM および Arc 対応サーバー用拡張機能のプレビュー サポート

**種類:** 新機能

Azure Automation では、Azure VM と、Arc 対応サーバーを介した非 Azure マシンに対するユーザー Hybrid Runbook Worker ネイティブ統合がリリースされました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support)を参照してください。

### <a name="preview-support-for-azure-active-directory-authentication"></a>Azure Active Directory 認証のプレビュー サポート

**種類:** 新機能

Azure Automation では、Automation サービスのすべてのパブリック エンドポイントに対して Azure AD 認証サポートを備えた重要なセキュリティ機能が追加されました。 この機能は、Azure VM と Arc 対応サーバー用の Hybrid Runbook Worker 拡張機能のサポートによって実装されました。

これにより、証明書への依存関係がなくなり、ローカル認証方法を使用せずに、厳格な監査とコンプライアンス要件を満たすことができます。 詳細については、[お知らせ](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support)を参照してください。

### <a name="source-control-enabled-to-use-managed-identities"></a>マネージド ID の使用が有効なソース管理

**種類:** 新機能

Azure Automation でのソース管理の統合で、実行アカウントではなく、[マネージド ID](automation-security-overview.md#managed-identities) を使用できるようになりました。 詳細については、[ソース管理の統合の前提条件](source-control-integration.md#prerequisites)に関する記事を参照してください。

## <a name="september"></a>September

### <a name="support-for-az-modules-by-default"></a>Az モジュールを既定でサポート

**種類:** 新機能

Azure Automation では、Az モジュールが既定でサポートされるようになりました。 新しく作成された Automation アカウントには、Az モジュールの最新バージョン (6.4.0) が既定で含まれます。 また、Automation には、既存の Automation アカウントでの Az モジュールの更新を可能にする、Azure portal のオプション、 **[Update Az Modules]\(Az モジュールを更新する\)** も含まれます。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-az-module-support)を参照してください。

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

Azure Automation 資産の暗号化を、お客様が各自のマネージド キーを使用して管理および保護することができます。 カスタマー マネージド キーの導入により、Azure Key Vault で作成して管理するキーを使用して、既定の暗号化を追加の暗号化レイヤーで補完できます。 この追加の暗号化レベルは、組織の規制またはコンプライアンスのニーズを満たすのに役立ちます。

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

Azure Automation で、Azure グローバルおよび Azure Government リージョンのクラウドとハイブリッド ジョブに対して、[システム割り当てマネージド ID](./automation-security-overview.md#managed-identities) がサポートされるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Automation のドキュメント作成に協力していただける方は、[Docs 共同作成者ガイド](/contribute/)に関するページを参照してください。
