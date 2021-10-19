---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/10/2021
ms.author: memildin
ms.openlocfilehash: 6a98a5dfbdf47ad9e2b74415786c240b32b3b499
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754693"
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
| <bpt id="p1">[</bpt>エンドポイント保護ソリューションを管理するための推奨事項の変更<ept id="p1">](#changes-to-recommendations-for-managing-endpoint-protection-solutions)</ept>             | 2021 年 11 月| 
| [オンプレミスのマシンのインベントリ表示で、リソース名に別のテンプレートが使用されるようになります](#inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name)    | 2021 年 11 月    |
| [ID の推奨事項に対する複数の変更](#multiple-changes-to-identity-recommendations)                                                                                          | 2021 年 11 月    |
| <bpt id="p1">[</bpt>SQL データベースで機密データを分類するための推奨事項の強化<ept id="p1">](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)</ept>                              | Q1 2022    |
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

**変更予定日:** 2021 年 11 月

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



### <a name="inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name"></a>オンプレミスのマシンのインベントリ表示では、リソース名に別のテンプレートが使用されます

**変更予定日:** 2021 年 11 月

[資産インベントリ](asset-inventory.md)内のリソースの表示を改善するために、オンプレミスのマシンに名前を付けるためのテンプレートから "source-computer-IP" 要素を削除します。

- **現在の形式:** ``machine-name_source-computer-id_VMUUID``
- **この更新から:** ``machine-name_VMUUID``


### <a name="multiple-changes-to-identity-recommendations"></a>ID の推奨事項に対する複数の変更

**変更予定日:** 2021 年 11 月

Security Center には、ユーザーとアカウントの管理を向上させるための複数の推奨事項が含まれています。 11 月に、次のような変更を行います。

- **更新間隔の改善** - 現在、ID の推奨事項の更新間隔は 24 時間です。 この更新の間隔が 12 時間に短縮されます。

- **アカウント除外機能** - Security Center には、エクスペリエンスをカスタマイズし、セキュリティ スコアが組織のセキュリティの優先順位を反映していることを確認するための多くの機能があります。 セキュリティの推奨事項に対する除外オプションは、このような機能の 1 つです。 完全な概要と手順については、「[セキュリティ スコアからのリソースと推奨事項の除外](exempt-resource.md)」を参照してください。 この更新により、次の表に示されている 8 つの推奨事項による評価から特定のアカウントを除外できるようになります。

    通常、緊急アクセス用の "break glass" アカウントは、MFA の推奨事項から除外されます。これは、そのようなアカウントは組織の MFA 要件から意図的に除外されることが多いためです。 または、アクセスを許可したいが MFA が有効になっていない外部アカウントがある場合があります。

    > [!TIP]
    > アカウントを除外すると、そのアカウントは異常と表示されなくなり、サブスクリプションが異常と表示されることもなくなります。

    |推奨| 評価キー|
    |-|-|
    |<bpt id="p1">[</bpt>サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed)</ept>|94290b00-4d0c-d7b4-7cea-064a9554e681|
    |<bpt id="p1">[</bpt>サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64)</ept>|151e82c5-5341-a74b-1eb0-bc38d2c84bb5|
    |[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/57e98606-6b1e-6193-0e3d-fe621387c16b)|57e98606-6b1e-6193-0e3d-fe621387c16b|
    |<bpt id="p1">[</bpt>所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9)</ept>|c3b6ae71-f1f0-31b4-e6c1-d5951285d03d|
    |<bpt id="p1">[</bpt>読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60)</ept>|a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b|
    |<bpt id="p1">[</bpt>書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4)</ept>|04e7147b-0deb-9796-2e5c-0336343ceb3d|
    |<bpt id="p1">[</bpt>所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad)</ept>|e52064aa-6853-e252-a11e-dffc675689c2|
    |<bpt id="p1">[</bpt>非推奨のアカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474)</ept>|00c6d40b-e990-6acf-d4f3-471e747a27c4|
    |||
 
- **推奨事項の名前の変更** - この更新から、2 つの推奨事項の名前を変更しています。 また、これらの説明も改訂しています。 評価キーは変更されません。 


    |プロパティ  |現在の値  | この更新から|
    |---------|---------|---------|
    |評価キー     | e52064aa-6853-e252-a11e-dffc675689c2        | 変更なし|
    |名前     |<bpt id="p1">[</bpt>所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad)</ept>         |Active Directory でブロックされていて、所有者のアクセス許可を持つアカウントをサブスクリプションから削除する必要がある        |
    |説明     |サインインがブロックされているユーザー アカウントは、サブスクリプションから削除する必要があります。<br>これらのアカウントは、気付かれずにデータにアクセスする方法を見つけようとしている攻撃者の標的になるおそれがあります。|Active Directory へのサインインがブロックされているユーザー アカウントは、サブスクリプションから削除する必要があります。 これらのアカウントは、気付かれずにデータにアクセスする方法を見つけようとしている攻撃者の標的になるおそれがあります。<br>ID 境界を保護する方法の詳細については、「[Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス](/azure/security/fundamentals/identity-management-best-practices.md)」を参照してください。|
    |関連ポリシー     |<bpt id="p1">[</bpt>所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad)</ept>         |Active Directory でブロックされていて、所有者のアクセス許可を持つアカウントをサブスクリプションから削除する必要がある |
    |||

    |プロパティ  |現在の値  | この更新から|
    |---------|---------|---------|
    |評価キー     | 00c6d40b-e990-6acf-d4f3-471e747a27c4        | 変更なし|
    |名前     |<bpt id="p1">[</bpt>非推奨のアカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474)</ept>|Active Directory でブロックされていて、読み取りおよび書き込みアクセス許可を持つアカウントをサブスクリプションから削除する必要がある|
    |説明     |サインインがブロックされているユーザー アカウントは、サブスクリプションから削除する必要があります。<br>これらのアカウントは、気付かれずにデータにアクセスする方法を見つけようとしている攻撃者の標的になるおそれがあります。|Active Directory へのサインインがブロックされているユーザー アカウントは、サブスクリプションから削除する必要があります。 これらのアカウントは、気付かれずにデータにアクセスする方法を見つけようとしている攻撃者の標的になるおそれがあります。<br>ID 境界を保護する方法の詳細については、「[Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス](/azure/security/fundamentals/identity-management-best-practices.md)」を参照してください。|
    |関連ポリシー     |<bpt id="p1">[</bpt>非推奨のアカウントをサブスクリプションから削除する必要がある<ept id="p1">](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474)</ept>|Active Directory でブロックされていて、読み取りおよび書き込みアクセス許可を持つアカウントをサブスクリプションから削除する必要がある|
    |||
 




### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>SQL データベースで機密データを分類するための推奨事項の強化

<bpt id="p1">**</bpt>変更予定日:<ept id="p1">**</ept> 2022 年第 1 四半期

"<bpt id="p2">**</bpt>データ分類の適用<ept id="p2">**</ept>" セキュリティ コントロールにおける推奨事項 "<bpt id="p1">**</bpt>SQL データベースの機密データを分類する必要がある<ept id="p1">**</ept>" は、より Microsoft のデータ分類戦略に沿った新しいバージョンで置き換えられる予定です。 これにより、推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。


## <a name="next-steps"></a>次のステップ

Security Center に対する最近のすべての変更については、「<bpt id="p1">[</bpt>Azure Security Center の最新情報<ept id="p1">](release-notes.md)</ept>」を参照してください。
