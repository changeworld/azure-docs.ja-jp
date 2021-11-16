---
title: Microsoft Defender for Cloud の法令遵守ダッシュボード
description: Defender for Cloud の法令遵守ダッシュボードで規制基準を追加および削除する方法について説明します
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/05/2021
ms.author: memildin
ms.openlocfilehash: f4b79d1f575d2cccfd2ea6f50af13e439a3ca968
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053502"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>規制コンプライアンス ダッシュボードで標準セットをカスタイマイズする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud では、リソースの構成と業界標準、規制、ベンチマークの要件が継続的に比較されます。 **規制コンプライアンス ダッシュボード** では、特定のコンプライアンス要件をどのように満たしているかに基づいて、コンプライアンス体制に関する分析情報が提供されます。

> [!TIP]
> [よく寄せられる質問](regulatory-compliance-dashboard.md#faq---regulatory-compliance-dashboard)については、「Defender for Cloud の法令順守ダッシュボード」を参照してください。

## <a name="how-are-regulatory-compliance-standards-represented-in-defender-for-cloud"></a>Defender では、規制遵守の基準はどのようにして Defender で表されますか?

業界標準、規制標準、およびベンチマークは、クラウドの規制遵守ダッシュボードの Defender で表現されています。 各標準は、Azure Policy で定義されたイニシアチブです。

評価としてマップされたコンプライアンス データをダッシュボードに表示するには、**Security Policy** ページ内から管理グループまたはサブスクリプションにコンプライアンス標準を追加します。 Azure Policy とイニシアチブの詳細については、「[セキュリティ ポリシーの操作](tutorial-security-policy.md)」を参照してください。

標準またはベンチマークを選択したスコープに割り当てた場合、評価としてマップされた関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 割り当てられている標準の概要レポートをダウンロードすることもできます。

Microsoft では規制基準自体を追跡し、一部のパッケージの適用範囲を経時的かつ自動的に改善しています。 Microsoft がイニシアティブの新しいコンテンツをリリースすると、そのコンテンツは、標準のコントロールにマップされた新しいポリシーとしてダッシュボードに自動的に表示されます。


## <a name="what-regulatory-compliance-standards-are-available-in-defender-for-cloud"></a>Defender for Cloud では、どのような規制遵守基準を利用できますか?

既定で、すべてのサブスクリプションに **Azure セキュリティ ベンチマーク** が割り当てられています。 これは、Microsoft が作成した、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 [Azure セキュリティ ベンチマークの詳細を確認してください](/security/benchmark/azure/introduction)。

次のような標準も追加できます。

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK OFFICIAL および UK NHS
- カナダ連邦の PBMM
- Azure CIS 1.3.0
- CMMC レベル 3
- New Zealand ISM Restricted

標準は、使用可能になるとダッシュボードに追加されます。


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>規制基準をダッシュボードへ追加する

次の手順は、サポートされている規制基準のいずれかへの準拠を監視するためのパッケージを追加する方法を示しています。

### <a name="prerequisites"></a>前提条件
ダッシュボードに標準を追加するには:

- サブスクリプションでは、クラウドの強化されたセキュリティ機能について Defender が有効になっている必要があります
- ユーザーには、所有者またはポリシーの共同作成者のアクセス許可が必要です。

### <a name="add-a-standard"></a>標準の追加

1. Defender for Cloud メニューで、 **規制遵守** を選択して法令遵守ダッシュボードを開きます。 ここで、現在選択されているサブスクリプションに割り当てられているコンプライアンス標準を確認できます。   

1. ページの上部から、 **[コンプライアンス ポリシーの管理]** を選択します。 [ポリシー管理] ページが表示されます。

1. 規制コンプライアンス体制を管理するサブスクリプションまたは管理グループを選択します。 

    > [!TIP]
    > 入れ子になったすべてのリソースのコンプライアンス データが集計され、追跡されるように、標準の適用対象として最上位のスコープを選択することをお勧めします。 

1. 組織に関連する標準を追加するには、 **[業界および規制の基準]** セクションを展開し、 **[標準をさらに追加]** を選択します。

1. **[規制コンプライアンス標準の追加]** ページで、以下を含む使用可能な標準を検索できます。

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO および UK NHS**
    - **カナダ連邦の PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC レベル 3**
    - **New Zealand ISM Restricted**
    
    ![Microsoft Defender for Cloud の法令順守ダッシュボードに規制標準を追加します。](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **[追加]** を選択し、スコープ、パラメーター、修復など、特定のイニシアチブに関して必要な詳細をすべて入力します。

1. Defender for Cloud のメニューで、 **[規制遵守]** をもう一度選択して、法令遵守ダッシュボードに戻ります。

    業界標準および規制基準の一覧に新しい標準が表示されます。 

    > [!NOTE]
    > 新しく追加された標準がコンプライアンス ダッシュボードに表示されるまでに数時間かかることがあります。

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-dashboard.png" alt-text="規制コンプライアンス ダッシュボード。" lightbox="./media/regulatory-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>ダッシュボードから標準を削除する

提供されている規制基準のうち、お客様の組織に関連しないものをすべて削除することで、規制コンプライアンス ダッシュボードをカスタマイズし、お客様に該当する標準のみに焦点を当て続けることができます。

標準を削除するには:

1. Defender for Cloud のメニューから、[セキュリティ ポリシー] **を選択します**。

1. 標準を削除する、該当するサブスクリプションを選択します。

    > [!NOTE]
    > サブスクリプションから標準を削除することはできますが、管理グループからは削除できません。 

    [セキュリティ ポリシー] ページが開きます。 選択したサブスクリプションの、既定のポリシー、業界標準と規制基準、および作成したカスタム イニシアチブが表示されます。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Microsoft Defender for Cloud の法令遵守ダッシュボードから規制標準を削除します。":::

1. 削除する標準に対応する **[無効]** を選択します。 確認ウィンドウが表示されます。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="選択した規制基準を本当に削除することを確認します。":::

1. **[はい]** を選択します。 標準が削除されます。 


## <a name="next-steps"></a>次のステップ

この記事では、規制基準と業界標準への準拠を監視するための **コンプライアンス標準を追加** する方法について説明しました。

関連資料については、次のページを参照してください。

- [Azure セキュリティ ベンチマーク](/security/benchmark/azure/introduction)
- [クラウドの法令遵守ダッシュボードの defender](regulatory-compliance-dashboard.md) -クラウドおよび外部ツールの defender を使用して規制遵守データを追跡およびエクスポートする方法について説明します
- [セキュリティ ポリシーの操作](tutorial-security-policy.md)