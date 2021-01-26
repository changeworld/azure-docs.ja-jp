---
title: Azure Security Center の規制コンプライアンス ダッシュボードの使用
description: Security Center の規制コンプライアンス ダッシュボードで規制基準を追加および削除する方法について説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2020
ms.author: memildin
ms.openlocfilehash: e7e1567a487dc6cadc94a42f02c597ff0e02665b
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372763"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ

Azure Security Center では、リソースの構成が業界標準、規制、ベンチマークの要件と継続的に比較されます。 **規制コンプライアンス ダッシュボード** では、特定のコンプライアンスのコントロールと要件をどのように満たしているかに基づいて、コンプライアンス体制に関する分析情報が提供されます。


## <a name="overview-of-compliance-packages"></a>コンプライアンス パッケージの概要

業界標準、規制基準、およびベンチマークは、Security Center で "*コンプライアンス パッケージ*" として表されます。  各パッケージは、Azure Policy で定義されたイニシアチブです。 評価としてマップされたコンプライアンス データをダッシュボードに表示するには、**Security Policy** ページ内から管理グループまたはサブスクリプションにコンプライアンス パッケージを追加します。 (Azure Policy とイニシアチブの詳細については、「[セキュリティ ポリシーの操作](tutorial-security-policy.md)」をご覧ください)。

選択したスコープに標準またはベンチマークをオンボードすると、イニシアチブがそのスコープに割り当てられ、評価としてマップされた、関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 オンボードされている標準の概要レポートをダウンロードすることもできます。

また、Microsoft では規制基準自体を追跡し、一部のパッケージの適用範囲を経時的かつ自動的に改善しています。 Microsoft がイニシアティブの新しいコンテンツ (標準のより多くのコントロールにマップされる新しいポリシー) をリリースすると、その追加のコンテンツがダッシュボードに自動的に表示されます。

> [!TIP]
> Microsoft が新しいコンテンツをリリースしたときに経時的に改善される標準の 1 つが、**Azure CIS 1.1.0 (new)** (正式名称は、[CIS Microsoft Azure Foundations Benchmark version 1.1.0](https://www.cisecurity.org/benchmark/azure/)) です。 これを Azure CIS 1.1.0 (すべての Security Center 環境で既定で構成されている Azure CIS の表現) と共にダッシュボードに追加する必要があります。 そのパッケージでは静的なルール セットが利用されます。 新しいパッケージには、より多くのポリシーが含まれており、経時的かつ自動的に更新されます。 以下の説明に従って、新しい動的パッケージに更新します。


## <a name="available-packages"></a>使用可能なパッケージ

NIST SP 800-53 R4、SWIFT CSP CSCF-v2020、UK Official および UK NHS、Canada Federal PBMM、Azure CIS 1.1.0 (new) (Azure CIS 1.1.0 のより完全な表現) などの標準を追加できます。 

さらに、**Azure セキュリティ ベンチマーク** を追加することもできます。これは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 (Azure セキュリティ ベンチマークの詳細については、[こちら](../security/benchmarks/introduction.md)をご覧ください)。

追加の標準が使用可能になると、ダッシュボードでサポートされます。 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>規制基準をダッシュボードへ追加する

次の手順は、サポートされている規制基準のいずれかへの準拠を監視するためのパッケージを追加する方法を示しています。

> [!NOTE]
> 所有者またはポリシーの共同作成者であるユーザーのみが、コンプライアンス標準を追加するために必要なアクセス許可を持っています。 

1. Security Center のサイドバーで、 **[規制コンプライアンス]** を選択して、規制コンプライアンス ダッシュボードを開きます。 ここで、現在選択されているサブスクリプションに割り当てられているコンプライアンス標準を確認できます。   

1. ページの上部から、 **[コンプライアンス ポリシーの管理]** を選択します。 [ポリシー管理] ページが表示されます。

1. 規制コンプライアンス体制を管理するサブスクリプションまたは管理グループを選択します。 

    > [!TIP]
    > 入れ子になったすべてのリソースのコンプライアンス データが集計され、追跡されるように、標準の適用対象として最上位のスコープを選択することをお勧めします。 

1. 組織に関連する標準を追加するには、 **[標準をさらに追加]** をクリックします。 

1. **[規制コンプライアンス標準の追加]** ページで、使用できる標準のパッケージを検索できます。 使用できる標準の一部を次に示します。

    - **Azure セキュリティ ベンチマーク**
    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF-v2020**
    - **UKO および UK NHS**
    - **Canada PBMM**
    
    ![Azure Security Center の規制コンプライアンス ダッシュボードへの規制パッケージの追加](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **[追加]** を選択し、スコープ、パラメーター、修復など、特定のイニシアチブに関して必要な詳細をすべて入力します。

1. Security Center のサイドバーで、再度 **[規制コンプライアンス]** を選択して、規制コンプライアンス ダッシュボードに戻ります。
    * 業界標準および規制基準の一覧に新しい標準が表示されます。 
    * **Azure CIS 1.1.0 (New)** を追加した場合、Azure CIS 1.1.0 コンプライアンスの元の "*静的*" ビューもそのまま残されます。 これは、今後自動的に削除される可能性があります。

    > [!NOTE]
    > 新しく追加された標準がコンプライアンス ダッシュボードに表示されるまでに数時間かかることがあります。

    [![古い Azure CIS と新しい Azure CIS が表示された規制コンプライアンス ダッシュボード](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>ダッシュボードから標準を削除する

提供されている規制基準に組織に関連していないものが含まれている場合、簡単なプロセスで UI から削除することができます。 これにより、規制へのコンプライアンス ダッシュボードをさらにカスタマイズし、ユーザーに該当する標準にのみ焦点を当てることができます。

標準を削除するには:

1. Security Center のメニューから、 **[セキュリティ ポリシー]** を選択します。

1. 標準を削除する、該当するサブスクリプションを選択します。

    > [!NOTE]
    > サブスクリプションから標準を削除することはできますが、管理グループからは削除できません。 

    [セキュリティ ポリシー] ページが開きます。 選択したサブスクリプションの、既定のポリシー、業界標準と規制基準、および作成したカスタム イニシアチブが表示されます。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Azure Security Center の規制へのコンプライアンス ダッシュボードから規制基準を削除する":::

1. 削除する標準に対応する **[無効]** を選択します。 確認ウィンドウが表示されます。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="選択した規制基準を本当に削除するかどうかを確認します":::

1. **[はい]** を選択します。 標準が削除されます。 


## <a name="next-steps"></a>次のステップ

この記事では、追加の標準への準拠を監視するための **コンプライアンス パッケージを追加** する方法について説明しました。 

その他の関連資料については、次の記事を参照してください。 

- [Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)
- [Security Center の規制コンプライアンス ダッシュ ボード](security-center-compliance-dashboard.md)
- [セキュリティ ポリシーの操作](tutorial-security-policy.md)