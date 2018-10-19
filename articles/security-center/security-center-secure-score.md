---
title: Azure Security Center 上のセキュリティ スコア | Microsoft Docs
description: " Azure Security Center 上のセキュリティ スコアを使用して、セキュリティに関する推奨事項に優先順位を付けます。 "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: fc521db9ad753c4162b65abfd2f9f23c318fa994
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131062"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Azure Security Center 上のセキュリティ スコアの向上


セキュリティ上の利点を提供する非常に多くのサービスでは、通常、ワークロードのセキュリティ保護と強化のために最初に実行する手順を知るのが困難です。 Azure Security Center のセキュリティ スコアでは、セキュリティに関する推奨事項がレビューされ、その優先順位が設定されるので、最初に実行する推奨事項を把握できます。これは、最も重大なセキュリティの脆弱性を探して調査の優先順位を付けるのに役立ちます。 セキュリティ スコアは、セキュリティで保護されたワークロードを実現するためにセキュリティを強化するのに役立つ測定ツールです。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]


![スコア ダッシュボードのセキュリティ保護](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>スコアの計算のセキュリティ保護

Security Center では、セキュリティ アナリストの作業を模倣して、セキュリティに関する推奨事項をレビューし、各推奨事項の重要度を判断する高度なアルゴリズムを適用します。
Azure Security Center ではアクティブな推奨事項が絶えずレビューされ、それらに基づいてセキュリティ スコアが計算されます。推奨事項のスコアは、その重大度と、ワークロードのセキュリティに最も影響するセキュリティのベスト プラクティスから導出されます。

**セキュリティ スコア**は、正常なリソースとリソースの合計間の比率に基づいて計算されます。 正常なリソースの数がリソースの総数と等しい場合は、50 の最大セキュリティ スコアを取得します。 最大スコアに近いセキュリティ スコアを取得するには、推奨事項に従って、正常でないリソースを修正します。

Security Center では、全体的なセキュリティ スコアも提供されます。 

**全体的なセキュリティ スコア**は、すべての推奨事項の累積です。 選択した項目に応じて、サブスクリプションまたは管理グループ全体で、全体的なセキュリティ スコアを表示できます。 スコアは、選択したサブスクリプションと、それらのサブスクリプションに対するアクティブな推奨事項によって異なります。

 

セキュリティ スコアに最も影響する推奨事項を確認するには、影響が上位 3 つの推奨事項を Security Center ダッシュボードに表示するか、**[セキュリティ スコアの影響]** 列を使用して、推奨事項の一覧ブレード内の推奨事項を並べ替えることができます。


全体的なセキュリティ スコアを表示するには:

1. Azure のダッシュボードで **[Security Center]** をクリックしてから、**[推奨事項]** をクリックします。
2. 上部にセキュリティ スコアが表示されます。これは、選択したサブスクリプションごとの各ポリシーのスコアを表します。 
2. 推奨事項を一覧表示する次の表では、各推奨事項について、**[セキュリティ スコアの影響]** を表す列があるのがわかります。 この数字は、推奨事項に従った場合に、全体的なセキュリティ スコアがどの程度向上するかを表します。 たとえば、次の画面では、**コンテナーのセキュリティ構成の脆弱性を修復**した場合、セキュリティ スコアが 35 ポイント増加します。

   ![セキュリティ スコア](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>個々のセキュリティ スコア

さらに、個々のセキュリティ スコアを表示するには、個々の推奨事項ブレード内でこれらを検索します。  

**推奨事項セキュリティ スコア**は、正常なリソースとリソースの合計間の比率に基づいて計算されます。 正常なリソースの数がリソースの総数と等しい場合は、推奨事項の最大セキュリティ スコアを取得します。 最大スコアに近いセキュリティ スコアを取得するには、修復手順に従って、正常でないリソースを修正します。

**[推奨事項の影響]** により、推奨される手順を適用した場合に、セキュリティ スコアがどの程度向上するかを確認できます。 たとえば、セキュリティ スコアが 42 で、**[推奨事項の影響]** が +3 の場合、推奨事項で説明されている手順を実行すると、セキュリティ スコアが 45 に向上します。

推奨事項は、修復手順が実行されていない場合にワークロードがさらされる驚異を示します。

![個々の推奨事項のセキュリティ スコア](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>次の手順
この記事では、Azure Security Center 上で**セキュリティ スコア**を使用して、セキュリティ体制を向上させる方法を示しました。 セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* 「[Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。


