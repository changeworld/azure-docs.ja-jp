---
title: リソース、サブスクリプション、管理グループ、セキュリティ スコアから Microsoft Defender for Cloud の推奨事項を除外する
description: サブスクリプションまたは管理グループからのセキュリティの推奨事項を除外する規則を作成し、セキュリティ スコアに対する影響を防ぐ方法について説明します。
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: how-to
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: fdcf7108f5e8aa047d36937f1407763d232a7835
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525585"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>セキュリティ スコアからのリソースと推奨事項の除外 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

すべてのセキュリティ チームの最優先事項として重要なのは、組織にとって重要なタスクとインシデントにアナリストが集中できるようにすることです。 Defender for Cloud には、エクスペリエンスをカスタマイズし、セキュリティ スコアが組織のセキュリティの優先順位を反映するようにするための多くの機能があります。 **除外** オプションは、このような機能の 1 つです。

Microsoft Defender for Cloud でセキュリティに関する推奨事項を調査する場合、最初に確認する情報の 1 つは、影響を受けるリソースの一覧です。

この一覧には、含まれるべきではないと思われるリソースが含まれていることがあります。 または、推奨事項が属していないと思われるスコープに表示されます。 リソースは、Defender for Cloud によって追跡されないプロセスによって修復された可能性があります。 この推奨事項は、特定のサブスクリプションに適していない可能性があります。 または、組織が特定のリソースまたは推奨事項に関連するリスクに同意することを決定した場合もあります。

このような場合は、次のような推奨事項の除外を作成できます。

- **リソースを除外** して、将来、問題のあるリソースと共に一覧に表示されないようにします。セキュリティ スコアには影響しません。 リソースは適用外として表示され、その理由は "除外" として、選択した特定の正当な理由と共に示されます。

- **サブスクリプションまたは管理グループを除外** して、推奨事項がセキュリティ スコアに影響を与えないようにします。サブスクリプションまたは管理グループには今後表示されません。 これは、既存のリソースと、今後作成するすべてのリソースに関係してきます。 推奨事項は、選択したスコープに対して選択した特定の理由でマークされます。

## <a name="availability"></a>可用性

| 側面                          | 詳細                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:                  | プレビュー<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| 価格:                        | これは、Microsoft Defender for Cloud Azure Policy強化されたセキュリティ機能が有効になっているお客様に追加コストで提供される Premium Azure Policy 機能です。 他のユーザーについては、将来、料金が適用されることがあります。                                                                                                                                                                 |
| 必要なロールとアクセス許可: | **所有者** または **リソース ポリシーの共同作成者** (除外対象を作成するため)<br>規則を作成するには、Azure Policy でポリシーを編集するためのアクセス許可が必要です。<br>詳細については、「[Azure Policy における RBAC アクセス許可](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)」を参照してください。                                            |
| 制限事項:                    | 除外は、Defender for Cloud の既定のイニシアチブ 、[Azure セキュリティベンチマーク](/security/benchmark/azure/introduction)、または提供されている規制標準イニシアチブに含まれる推奨事項に対してのみ作成できます。 カスタム イニシアチブから生成された推奨事項を除外することはできません。 [ポリシー、イニシアチブ、推奨事項](security-policy-concept.md)の間の関係に関する詳細を参照してください。 |
| クラウド:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>除外対象の定義

Defender for Cloud がサブスクリプション、管理グループ、またはリソースに対して行うセキュリティに関する推奨事項を微調整するには、次の除外規則を作成できます：

- 特定の **推奨事項** をマークするか、"軽減済み" または "リスク承認済み" としてマークします。 サブスクリプション、複数のサブスクリプション、または管理グループ全体に対して推奨設定の除外を作成することができます。
- 特定の推奨事項について、**1 つまたは複数のリソース** を "軽減済み" または "リスク承認済み" としてマークします。

> [!NOTE]
> 除外は、Defender for Cloud の既定のイニシアチブ、Azure セキュリティ ベンチマーク、または提供されている規制標準イニシアチブに含まれる推奨事項に対してのみ作成できます。 サブスクリプションに割り当てられているカスタム イニシアチブから生成された推奨事項を除外することはできません。 [ポリシー、イニシアチブ、推奨事項](security-policy-concept.md)の間の関係に関する詳細を参照してください。

> [!TIP]
> API を使用して除外を作成することもできます。 JSON の例と関連する構造の説明については、「[Azure Policy 適用除外の構造](../governance/policy/concepts/exemption-structure.md)」を参照してください。

除外規則を作成するには、次の手順を実行します。

1. 特定の推奨事項について、推奨事項の詳細ページを開きます。

1. ページの上部にあるツールバーで、 **[除外]** を選択します。

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="サブスクリプションまたは管理グループから除外する推奨事項の除外規則を作成します。":::

1. **[除外]** ウィンドウで次のようにします。
    1. この除外規則のスコープを選択します:
        - 管理グループを選択すると、そのグループ内のすべてのサブスクリプションから推奨事項が除外されます。
        - この規則を作成して推奨設定から 1 つまたは複数のリソースを除外する場合は、[Selected resources]\(選択したリソース\) を選択し、一覧から関連するリソースを選択します。

    1. この除外規則の名前を入力します。
    1. 必要に応じて、有効期限を設定します。
    1. 除外対象のカテゴリを選択します。
        - **サードパーティによって解決済み (軽減済み)** – Security Center が特定していないサードパーティのサービスを使用している場合。 

            > [!NOTE]
            > 推奨事項を軽減済みとして除外すると、セキュリティ スコアに対するポイントは付与されません。 ただし、問題のあるリソースのポイントは *削除* されないため、スコアは増えることになります。

        - **リスク承認済み (免除)** - この推奨事項を軽減しないというリスクに同意する場合
    1. 必要に応じて、説明を入力します。
    1. **［作成］** を選択します

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="サブスクリプションまたは管理グループから推奨事項を除外するための除外規則を作成する手順。":::

    除外対象が有効になるタイミング (最大で 30 分かかる場合がある):
    - 推奨事項またはリソースは、セキュリティ スコアには影響しません。
    - 特定のリソースを除外した場合は、推奨事項の詳細ページの **[適用外]** タブに表示されます。
    - 推奨事項を除外した場合は、Defender for Cloud の [推奨事項] ページで既定で非表示になります。 これは、そのページの **推奨事項のステータス** フィルターの既定のオプションが、 **[適用外]** 推奨事項を除外するためのオプションではないからです。 セキュリティ コントロールのすべての推奨事項を除外する場合も同様です。

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Microsoft Defender for Cloud の [推奨事項] ページの既定のフィルターでは、該当しない推奨事項とセキュリティコントロールが非表示になります":::

    - 推奨事項の詳細ページの上部にある情報ストリップには、除外されたリソースの数が更新されます。
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="除外されたリソースの数。":::

1. 除外されたリソースを確認するには、 **[適用外]** タブを開きます。

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="除外対象の変更。":::

    各除外の理由が表 (1) に示されています。

    除外対象を変更または削除するには、(2) に示すように、省略記号メニュー ("...") を選択します。

1. サブスクリプションのすべての除外ルールを確認するには、情報ストリップから **[View exemptions]\(除外の表示\)** を選択します。

    > [!IMPORTANT]
    > 1 つの推奨事項に関連する特定の除外を確認するには、該当するスコープと推奨名に従って一覧をフィルター処理します。

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy の除外対象ページ":::

    > [!TIP]
    > または、[Azure Resource Graph を使用して、除外対象に関する推奨事項を検索](#find-recommendations-with-exemptions-using-azure-resource-graph)します。

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>サブスクリプションで作成された除外を監視する

このページで既に説明したように、除外規則は、サブスクリプションと管理グループ内のリソースに影響する推奨事項をきめ細かく制御できる強力なツールです。 

ユーザーがこの機能をどのように使用しているかを追跡するために、Logic App Playbook をデプロイする Azure Resource Manager (ARM) テンプレートと、除外の作成時に通知するために必要なすべての API 接続を作成しました。

- プレイブックの詳細については、[Microsoft Defender for Cloud でリソースの除外を追跡する方法](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)に関するテクニカル コミュニティのブログ記事 を参照しくださいか。
- ARM テンプレートは[Microsoft Defender for Cloud GitHub にあります](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- 必要なすべてのコンポーネントをデプロイするには、[こちらの自動化されたプロセス](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)を使用してください

## <a name="use-the-inventory-to-find-resources-that-have-exemptions-applied"></a>インベントリを使用して除外が適用されているリソースを検索する

Microsoft Defender for Cloud の資産インベントリ ページには、Defender for Cloud に接続したリソースのセキュリティ態勢を表示する 1 つのページが表示されます。 詳細については、「[資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)」を参照してください。

インベントリ ページにある多くのフィルターを使用して、リソースの一覧を、特定のシナリオに最も関係が深いものに絞り込むことができます。 そのようなフィルターの 1 つに **[適用除外を含む]** があります。 1 つまたは複数の推奨から除外されたすべてのリソースを検索するには、このフィルターを使用します。

:::image type="content" source="media/exempt-resource/inventory-filter-exemptions.png" alt-text="Defender for Cloud の資産インベントリ ページと、除外対象のリソースを検索するフィルター":::


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Azure Resource Graph を使用して除外対象の推奨事項を検索する

Azure Resource Graph (ARG) を使用すると、堅牢なフィルター処理、グループ化、および並べ替え機能を使用して、クラウド環境全体のリソース情報にすばやくアクセスできます。 これは、Azure サブスクリプション全体の情報を、プログラムから、または Azure portal 内ですばやく効率的に照会する方法です。

除外規則があるすべての推奨事項を表示するには、次の手順を実行します。

1. **Azure Resource Graph エクスプローラー** を開きます。

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph エクスプローラーの起動** 推奨ページ" :::

1. 次のクエリを入力し、 **[クエリの実行]** を選択します。

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


詳細については、次のページを参照してください。
- [Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/index.yml)。
- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)
- [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>FAQ - 除外規則

- [複数のポリシー イニシアチブに 1 つの推奨事項がある場合はどうなりますか。](#what-happens-when-one-recommendation-is-in-multiple-policy-initiatives)
- [除外をサポートしていない推奨事項はありますか。](#are-there-any-recommendations-that-dont-support-exemption)

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>複数のポリシー イニシアチブに 1 つの推奨事項がある場合はどうなりますか。

場合によっては、複数のポリシー イニシアチブにセキュリティの推奨事項が表示されることがあります。 同じ推奨事項の複数のインスタンスが同じサブスクリプションに割り当てられていて、その推奨設定の除外対象を作成した場合は、編集権限があるすべてのイニシアチブに影響します。 

たとえば、推奨事項 **** は、Microsoft Defender for Cloud によってすべての Azure サブスクリプションに割り当てられる既定のポリシー イニシアチブの一部です。 XXXXX にも含があります。

この推奨設定の除外を作成しようとすると、次の 2 つのメッセージのいずれかが表示されます。

- 両方の **イニシアティブ** を編集するために必要なアクセス許可がある場合は、次の内容が表示される：

    *この推奨事項は、「コンマで区切られたイニシアチブ名」といういくつかのポリシー イニシアチブに含まれています。除外はすべてのユーザーに対して作成されます。*  

- 両方の **イニシアチブに対して** 十分なアクセス許可がない場合は、代わりに次のメッセージが表示される：

    *すべてのポリシー イニシアチブに適用の除外を適用するにはアクセス許可が制限されているために、除外は十分なアクセス許可を持つイニシアチブに対してのみ作成されます。*

### <a name="are-there-any-recommendations-that-dont-support-exemption"></a>除外をサポートしていない推奨事項はありますか。

以下の推薦事項では、例外がサポートされていません。

- コンテナーの CPU とメモリの制限を強制する必要がある
- 特権コンテナーの使用を避ける
- コンテナー イメージは信頼されたレジストリからのみデプロイする必要がある
- コンテナーは許可されたポートでのみリッスンする必要がある
- サービスは許可されたポートでのみリッスンする必要がある
- コンテナーで最小限の特権を持つ Linux 機能を適用する必要がある
- コンテナーで不変 (読み取り専用) のルート ファイル システムを適用する必要がある
- 特権エスカレーションを含むコンテナーは避ける必要がある
- コンテナーをルート ユーザーとして実行しない
- ホスト ネットワークとポートの使用を制限する必要がある
- 機密性の高いホストの名前空間を共有するコンテナーは避ける必要がある
- ポッドの HostPath ボリューム マウントの使用を既知のリストに制限して、侵害されたコンテナーからのノード アクセスを制限する必要がある
- コンテナーの AppArmor プロファイルのオーバーライドまたは無効化を制限する必要がある


## <a name="next-steps"></a>次の手順

この記事では、セキュリティ スコアに影響しないように、リソースを推奨事項から除外する方法を学習しました。 セキュリティ スコアの詳細については、以下を参照してください。

- [Microsoft Defender for Cloud のセキュリティで保護されたスコア](secure-score-security-controls.md)
