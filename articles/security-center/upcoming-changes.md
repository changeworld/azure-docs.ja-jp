---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/05/2021
ms.author: memildin
ms.openlocfilehash: e6d1c8cf55687a8e4d7612ca432a314ae38256bc
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534421"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「<bpt id="p1">[</bpt>Azure Security Center の最新情報<ept id="p1">](release-notes.md)</ept>」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

| 計画されている変更       | 変更予定日 |
|----------------------|---------------------------|
| [プレビュー アラートの非推奨: ARM.MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | 2021 年 10 月|
| <bpt id="p1">[</bpt>ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定<ept id="p1">](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)</ept>| 2021 年 10 月|
| <bpt id="p1">[</bpt>エンドポイント保護ソリューションを管理するための推奨事項の変更<ept id="p1">](#changes-to-recommendations-for-managing-endpoint-protection-solutions)</ept>             | 2021 年 10 月    |
| <bpt id="p1">[</bpt>SQL データベースで機密データを分類するための推奨事項の強化<ept id="p1">](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)</ept>   | Q1 2022    |
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>プレビュー アラートの非推奨: ARM.MCAS_ActivityFromAnonymousIPAddresses

<bpt id="p1">**</bpt>変更予定日:<ept id="p1">**</ept> 2021 年 10 月

次のプレビュー アラートが非推奨になる予定です。

|アラート名| 説明|
|----------------------|---------------------------|
|<bpt id="p1">**</bpt>PREVIEW - Activity from a risky IP address (プレビュー - 危険な IP アドレスからのアクティビティ)<ept id="p1">**</ept><br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|匿名プロキシ IP アドレスとして識別されている IP アドレスからのユーザー アクティビティが検出されました。<br>このようなプロキシは、自分のデバイスの IP アドレスを隠したいユーザーによって使用され、悪意のある目的で使用される恐れがあります。 この検出では、組織内の他のユーザーがよく使用する IP アドレスのタグ付けが間違っているなどの誤検知を低減する機械学習アルゴリズムが活用されます。<br>アクティブな Microsoft Cloud App Security ライセンスが必要です。|
|||

この情報を提供する新しいアラートを作成し、それを追加しました。 また、新しいアラート (ARM_OperationFromSuspiciousIP、ARM_OperationFromSuspiciousProxyIP) には、Microsoft Cloud App Security のライセンスは必要ありません。

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定

<bpt id="p1">**</bpt>変更予定日:<ept id="p1">**</ept> 2021 年 10 月

ISO 27001 のレガシ実装は Security Center の規制コンプライアンス ダッシュボードから削除されます。 Security Center で ISO 27001 へのコンプライアンスを追跡している場合は、関連するすべての管理グループまたはサブスクリプションに対して新しい ISO 27001:2013 標準をオンボードすると、現在のレガシ ISO 27001 が間もなくダッシュボードから削除されます。

<bpt id="p1">:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="</bpt>ISO 27001 のレガシ実装の削除に関するメッセージが表示されている Security Center の規制コンプライアンス ダッシュボード。<ept id=&quot;p1&quot;>" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::</ept>

### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>エンドポイント保護ソリューションを管理するための推奨事項の変更

<bpt id="p1">**</bpt>変更予定日:<ept id="p1">**</ept> 2021 年 10 月

2021 年 8 月に、お使いのマシンにエンドポイント保護ソリューションをデプロイして維持するための 2 つの新しい<bpt id="p1">**</bpt>プレビュー<ept id="p1">**</ept>推奨事項が追加されました。 詳細については、<bpt id="p1">[</bpt>リリース ノート<ept id="p1">](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)</ept>を参照してください。

これらの推奨事項が一般提供されると、次の既存の推奨事項が置き換えられます。

- "<bpt id="p1">**</bpt>エンドポイント保護をお使いのマシンにインストールする必要がある<ept id="p1">**</ept>" は、次を置き換えます。
    - [仮想マシンにエンドポイント保護ソリューションをインストールする (キー: 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [エンドポイント保護ソリューションをマシンにインストールする (キー: 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

- "<bpt id="p1">**</bpt>Endpoint Protection の正常性の問題を、お使いのコンピューターで解決する必要があります<ept id="p1">**</ept>" は、同じ名前を持つ既存の推奨事項を置き換えます。 2 つの推奨事項には、異なる評価キーがあります。
    - <bpt id="p1">**</bpt>プレビュー<ept id="p1">**</ept>推奨事項の評価キー: 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - <bpt id="p1">**</bpt>GA<ept id="p1">**</ept> 推奨事項の評価キー: 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

詳細情報:
- <bpt id="p1">[</bpt>Security Center でサポートされているエンドポイント保護ソリューション<ept id="p1">](security-center-services.md#endpoint-supported)</ept>
- <bpt id="p1">[</bpt>これらの推奨事項で、デプロイされたソリューションの状態を評価する方法<ept id="p1">](security-center-endpoint-protection.md)</ept>

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>SQL データベースで機密データを分類するための推奨事項の強化

<bpt id="p1">**</bpt>変更予定日:<ept id="p1">**</ept> 2022 年第 1 四半期

"<bpt id="p2">**</bpt>データ分類の適用<ept id="p2">**</ept>" セキュリティ コントロールにおける推奨事項 "<bpt id="p1">**</bpt>SQL データベースの機密データを分類する必要がある<ept id="p1">**</ept>" は、より Microsoft のデータ分類戦略に沿った新しいバージョンで置き換えられる予定です。 これにより、推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。


## <a name="next-steps"></a>次のステップ

Security Center に対する最近のすべての変更については、「<bpt id="p1">[</bpt>Azure Security Center の最新情報<ept id="p1">](release-notes.md)</ept>」を参照してください。