---
title: Azure Security Center の規制コンプライアンス ダッシュボードで規制コンプライアンスの動的監視に更新する方法 | Microsoft Docs
description: 規制コンプライアンス パッケージの更新 (プレビュー)
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cfa39799e44cee0a2d36efccd454ccf8ca15157f
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603234"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>規制コンプライアンス ダッシュボードでの動的コンプライアンス パッケージへの更新 (プレビュー)

Azure Security Center では、リソースの構成が業界標準、規制、ベンチマークの要件と継続的に比較されます。 **規制コンプライアンス ダッシュボード**では、特定のコンプライアンスのコントロールと要件をどのように満たしているかに基づいて、コンプライアンス体制に関する分析情報が提供されます。

コンプライアンス体制を追跡できる標準の 1 つが、[Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (正式には、"CIS Microsoft Azure Foundations Benchmark version 1.1.0") です。 

コンプライアンス ダッシュボードに最初に表示される Azure CIS の表現では、Security Center に含まれる静的なルール セットが利用されます。

**動的コンプライアンス パッケージ (プレビュー)** 機能を使用すると、Security Center で業界標準の適用範囲が経時的かつ自動的に改善されます。 コンプライアンス パッケージは、基本的に Azure Policy で定義されたイニシアティブです。 これらは、選択したスコープ (サブスクリプション、管理グループなど) に割り当てることができます。 評価としてマップされたコンプライアンス データをダッシュボードに表示するには、Security Policy 内から管理グループまたはサブスクリプションにコンプライアンス パッケージを追加します。 コンプライアンス パッケージを追加すると、選択したスコープに規制コンプライアンス イニシアティブが効果的に割り当てられます。 これにより、新しく公開された規制イニシアティブをコンプライアンス標準としてダッシュボードで追跡できます。 Microsoft がイニシアティブの新しいコンテンツ (標準のより多くのコントロールにマップされる新しいポリシー) をリリースすると、その追加のコンテンツがダッシュボードに自動的に表示されます。

Azure CIS ベンチマークの動的コンプライアンス パッケージである **Azure CIS 1.1.0 (New)** では、以下を行うことで元の "*静的*" バージョンを改善します。

* より多くのポリシーを含める
* 追加された新しい適用範囲で自動的に更新する 

以下の説明に従って、新しい動的パッケージに更新します。

## <a name="adding-a-dynamic-compliance-package"></a>動的コンプライアンス パッケージの追加

次の手順では、Azure CIS Benchmark v1.1.0 への準拠を監視するための動的パッケージを追加する方法を説明します。   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Azure CIS 1.1.0 (New) 動的コンプライアンス パッケージに更新する 

1. **[セキュリティ ポリシー]** ページを開きます。 このページには、管理グループ、サブスクリプション、およびワークスペースの数と、管理グループの構造が表示されます。

1. 規制コンプライアンス体制を管理するサブスクリプションまたは管理グループを選択します。 入れ子になったすべてのリソースのコンプライアンス データが集計され、追跡されるように、標準の適用対象として最上位のスコープを選択することをお勧めします。 

1. 業界および規制の標準 (プレビュー) セクションでは、Azure CIS 1.1.0 を新しいコンテンツに更新できることがわかります。 **[今すぐ更新]** をクリックします。 

1. 必要に応じて、 **[標準をさらに追加]** をクリックして、 **[規制コンプライアンス標準の追加]** ページを開きます。 そこで、**Azure CIS 1.1.0 (New)** や、他のコンプライアンス標準の動的パッケージ (**NIST SP 800-53 R4**、**SWIFT CSP CSCF-v2020**、**UKO および UK NHS**、**Canada PBMM** など) を手動で検索できます。
    
    ![Azure Security Center の規制コンプライアンス ダッシュボードへの規制パッケージの追加](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Security Center のサイドバーで、 **[規制コンプライアンス]** を選択して、規制コンプライアンス ダッシュボードを開きます。 
    * 業界および規制の標準の一覧に Azure CIS 1.1.0 (New) が表示されます。 
    * Azure CIS 1.1.0 コンプライアンスの元の "*静的*" ビューもそのまま残されます。 これは、今後自動的に削除される可能性があります。

    > [!NOTE]
    > 新しく追加された標準がコンプライアンス ダッシュボードに表示されるまでに数時間かかることがあります。


    [![古い Azure CIS と新しい Azure CIS が表示された規制コンプライアンス ダッシュボード](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

* 規制コンプライアンス ダッシュボードに表示される標準を、新しい "*動的*" パッケージに**アップグレード**する方法
* **コンプライアンス パッケージを追加**して、その他の標準への準拠を監視する方法 

その他の関連資料については、次の記事を参照してください。 

- [Security Center の規制コンプライアンス ダッシュ ボード](security-center-compliance-dashboard.md)
- [セキュリティ ポリシーの操作](tutorial-security-policy.md)
- 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」 - Azure Security Center の推奨事項をお客様の Azure リソースの保護に役立てる方法について説明しています。