---
title: Azure Security Center でのカスタム セキュリティ ポリシーの作成 | Microsoft Docs
description: Azure Security Center で監視される Azure カスタム ポリシー定義。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5432c794c26e350b23fe47aa1574422143ca4b2
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521481"
---
# <a name="using-custom-security-policies-preview"></a>カスタム セキュリティ ポリシーの使用 (プレビュー)

システムと環境のセキュリティ保護を支援するために、Azure Security Center ではセキュリティに関する推奨事項が生成されます。 これらの推奨事項は業界のベスト プラクティスに基づいており、すべてのお客様に提供される既定の汎用セキュリティ ポリシーに組み込まれています。 また、業界標準と規制基準に関する Security Center のナレッジから提供される場合もあります。

このプレビュー機能を使用して、独自の "*カスタム*" イニシアティブを追加できます。 作成したポリシーにマシンが従っていない場合は、推奨事項が提供されます。

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>イニシアティブをサブスクリプションに追加するには 

1. Security Center のサイドバーから、 **[セキュリティ ポリシー]** ページを開きます。

1. カスタム イニシアティブを追加するサブスクリプションまたは管理グループを選択します。

    [![カスタム ポリシーを作成するサブスクリプションの選択](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > カスタム標準を追加すると、そのスコープに "*イニシアティブ*" が割り当てられます。 そのため、その割り当てに必要な最も広いスコープを選択することをお勧めします。 

1. [セキュリティ ポリシー] ページの [カスタム イニシアティブ (プレビュー)] で、 **[カスタム イニシアティブの追加]** をクリックします。

    [![**[カスタム イニシアティブの追加]** をクリック](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    次のページが表示されます。

    ![ポリシーの作成または追加](media/custom-security-policies/create-or-add-custom-policy.png)

1. [カスタム イニシアティブの追加] ページで、組織で既に作成されているカスタム ポリシーの一覧を確認します。 サブスクリプションに割り当てるイニシアティブが表示されている場合は、 **[追加]** をクリックします。 ニーズに合ったイニシアティブが一覧にない場合は、この手順をスキップしてください。

1. 新しいカスタム イニシアティブを作成するには、次の手順に従います。

    1. **[新規作成]** をクリックします。
    1. 定義の場所と名前を入力します。
    1. 含めるポリシーを選択し、 **[追加]** をクリックします。
    1. 必要なパラメーターを入力します。
    1. **[Save]** をクリックします。
    1. [カスタム イニシアティブの追加] ページで、[最新の情報に更新] をクリックすると、新しいイニシアティブが表示され、使用できるようになります。
    1. **[追加]** をクリックして、サブスクリプションに割り当てます。

    > [!NOTE]
    > 新しいイニシアティブを作成するには、サブスクリプション所有者の資格情報が必要です。 Azure ロールの詳細については、「[Azure Security Center におけるアクセス許可](security-center-permissions.md)」をご覧ください。

1. ポリシーの生成された推奨事項を表示するには、サイドバーの **[推奨事項]** をクリックして推奨事項ページを開きます。 推奨事項は [カスタム] ラベル付きで表示され、最大 30 分間利用できます。

    [![カスタム推奨事項](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>次の手順

この記事では、カスタム セキュリティ ポリシーの作成方法について説明しました。 

その他の関連資料については、次の記事をご覧ください。 

- [セキュリティ ポリシーの概要](tutorial-security-policy.md)
- [組み込みのセキュリティ ポリシーの一覧](security-center-policy-definitions.md)