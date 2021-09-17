---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/19/2021
ms.author: memildin
ms.openlocfilehash: 0e9988aa7779d85714d6e1562af78c3a5bf0e1ff
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444693"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

| 計画されている変更       | 変更予定日 |
|----------------------|---------------------------|
| [ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| 2021 年 8 月|
| [一部のアラートの種類のプレフィックスを "ARM_" から "VM_" に変更](#changing-prefix-of-some-alert-types-from-arm_-to-vm_)                                          | 2021 年 10 月|
| [エンドポイント保護ソリューションを管理するための推奨事項の変更](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Q4 2021    |
| [SQL データベースで機密データを分類するための推奨事項の強化](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | Q1 2022    |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定

**変更予定日:** 2021 年 8 月

ISO 27001 のレガシ実装は Security Center の規制コンプライアンス ダッシュボードから削除されます。 Security Center で ISO 27001 へのコンプライアンスを追跡している場合は、関連するすべての管理グループまたはサブスクリプションに対して新しい ISO 27001:2013 標準をオンボードすると、現在のレガシ ISO 27001 が間もなくダッシュボードから削除されます。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="ISO 27001 のレガシ実装の削除に関するメッセージが表示されている Security Center の規制コンプライアンス ダッシュボード。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changing-prefix-of-some-alert-types-from-arm_-to-vm_"></a>一部のアラートの種類のプレフィックスを "ARM_" から "VM_" に変更 

**変更予定日:** 2021 年 10 月

2021 年 7 月に、[Azure Resource Manager アラート用の Azure Defender の論理的な再編成](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts)について発表しました。 

Azure Defender プランの論理的な再編成の一環として、21 個のアラートが [Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) から [Azure Defender for servers](defender-for-servers-introduction.md) に移動されました。

この再割り当てに合わせて、これらのアラートのプレフィックスの更新を計画しています。 次の表に示すように、"ARM_" を "VM_" に置き換える予定です。

| 現在の名前                                   | 変更後の名前                             |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |

[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) プランと [Azure Defender for servers](defender-for-servers-introduction.md) プランの詳細を参照してください。


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>エンドポイント保護ソリューションを管理するための推奨事項の変更

**変更予定日:** 2021 年第 4 四半期

2021 年 8 月に、お使いのマシンにエンドポイント保護ソリューションをデプロイして維持するための 2 つの新しい **プレビュー** 推奨事項が追加されました。 詳細については、[リリース ノート](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)を参照してください。

これらの推奨事項が一般提供されると、次の既存の推奨事項が置き換えられます。

- "**エンドポイント保護をお使いのマシンにインストールする必要がある**" は、次を置き換えます。
    - [仮想マシンにエンドポイント保護ソリューションをインストールする](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [マシンにエンドポイント保護ソリューションをインストールする](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee) 

- "**Endpoint Protection の正常性の問題を、お使いのコンピューターで解決する必要があります**" は、同じ名前を持つ既存の推奨事項を置き換えます。 2 つの推奨事項には、異なる評価キーがあります。
    - **プレビュー** 推奨事項の評価キー: 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - **GA** 推奨事項の評価キー: 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

詳細情報:
- [Security Center でサポートされているエンドポイント保護ソリューション](security-center-services.md#endpoint-supported)
- [これらの推奨事項で、デプロイされたソリューションの状態を評価する方法](security-center-endpoint-protection.md)

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>SQL データベースで機密データを分類するための推奨事項の強化

**変更予定日:** 2022 年第 1 四半期

"**データ分類の適用**" セキュリティ コントロールにおける推奨事項 "**SQL データベースの機密データを分類する必要がある**" は、より Microsoft のデータ分類戦略に沿った新しいバージョンで置き換えられる予定です。 これにより、推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。


## <a name="next-steps"></a>次のステップ

Security Center に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。