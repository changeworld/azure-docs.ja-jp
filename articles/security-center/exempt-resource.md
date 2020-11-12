---
title: Azure Security Center のセキュリティに関する推奨事項とセキュリティ スコアからリソースを除外する
description: セキュリティに関する推奨事項とセキュリティ スコアからリソースを除外する方法について説明します
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 347d5ac9de164f2e96340df71fd3b1b908e607c1
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372746"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>推奨事項とセキュリティ スコアからリソースを除外する

すべてのセキュリティ チームの最優先事項として重要なのは、組織にとって重要なタスクとインシデントにアナリストが集中できるようにすることです。 Security Center には、最も優先度が高い情報をカスタマイズし、組織のセキュリティに関する決定がセキュリティ スコアに確実に反映されるようにするための機能が多数用意されています。 リソースの除外は、このような機能の 1 つです。

Azure Security Center でセキュリティに関する推奨事項を調査するとき、最初にレビューする情報の 1 つが、影響を受けたリソースの一覧です。

この一覧には、含まれるべきではないと思われるリソースが含まれていることがあります。 それは、Security Center によって追跡されていないプロセスによって修復された可能性があります。 あるいは、ご自身の組織が、その特定のリソースのリスクを受け入れることにしたのかもしれません。 

このような場合は、除外規則を作成して、今後そのリソースが異常なリソースとして表示されないように、またご自身のセキュリティ スコアに影響を及ぼさないようにすることができます。 

リソースは適用外として表示され、その理由は "除外" として、選択した正当な理由と共に示されます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|プレビュー<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|価格:|これは、Azure Defender のお客様が追加コストなしで利用できる Azure Policy の Premium 機能です。 他のユーザーについては、将来、料金が適用されることがあります。|
|必要なロールとアクセス許可:|**サブスクリプションの所有者** または **ポリシーの共同作成者** (除外対象を作成する場合)<br>規則を作成するには、Azure Policy でポリシーを編集するためのアクセス許可が必要です。<br>詳細については、「[Azure Policy における RBAC アクセス許可](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)」を参照してください。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||


## <a name="create-an-exemption-rule"></a>除外規則を作成する

1. 異常なリソースの一覧で、除外するリソースの省略記号メニュー ("...") を選択します。

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="コンテキスト メニューからの除外オプションの作成":::

    除外対象の作成ウィンドウが開きます。

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="除外対象の作成":::

1. ご自身の条件を入力し、このリソースを除外する必要がある理由を選択します。
    - **軽減済み** - この問題は、推奨ツールまたはプロセス以外で処理されているため、リソースには関連していません
    - **免除** - このリソースのリスクを受け入れています
1. **[保存]** を選択します。
1. しばらくすると、以下のようになります (最大 24 時間かかる場合があります)。
    - リソースはセキュリティ スコアに影響しません。
    - リソースは、推奨事項の詳細ページの **[Not applicable]\(適用外\)** タブに表示されます
    - 推奨事項の詳細ページの上部にある情報ストリップには、除外されたリソースの数が表示されます。
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="除外されたリソースの数":::

1. 除外されたリソースを確認するには、 **[Not applicable]\(適用外\)** タブを開きます。

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="除外対象の変更":::

    各除外の理由が表 (1) に示されています。

    除外対象を変更または削除するには、(2) に示すように、省略記号メニュー ("...") を選択します。


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>ご自身のサブスクリプションのすべての除外規則を確認する

ポリシー割り当てで除外対象のリソースを作成するとき、除外規則によって使用されるのが Azure Policy です。

Azure Policy を使用すると、 **[Exemption]\(除外対象\)** ページですべての除外対象を追跡できます。

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy の除外対象ページ":::



## <a name="next-steps"></a>次の手順

この記事では、セキュリティ スコアに影響しないように、リソースを推奨事項から除外する方法を学習しました。 セキュリティ スコアの詳細については、以下を参照してください。

- [Azure Security Center 内のセキュリティ スコア](secure-score-security-controls.md)