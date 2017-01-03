---
title: "Azure Advisor のセキュリティに関する推奨事項 | Microsoft Docs"
description: "Azure Advisor を使用して、Azure のデプロイのセキュリティを向上させます。"
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 4345e544d2e2bcc90ee2e3caf5bf6dfc19f01ca3

---
# <a name="advisor-security-recommendations"></a>Advisor のセキュリティに関する推奨事項

Advisor は、すべての Azure リソースに関する推奨事項を、一貫性がある統合された形で提示します。 Azure Security Center と統合して、セキュリティに関する推奨事項を生成します。 セキュリティに関する推奨事項は、Advisor ダッシュボードの **[セキュリティ]** タブで取得できます。

![Advisor の [セキュリティ] タブ](./media/advisor-security-recommendations/advisor-security-tab.png)

セキュリティ センターは、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。 それは、Azure リソースのセキュリティの状態を定期的に分析します。 セキュリティ センターでは、潜在的なセキュリティの脆弱性が特定されると、推奨事項が作成されます。 推奨事項では、必要なコントロールを構成する手順を説明します。 

![Advisor の [セキュリティ] タブ](./media/advisor-security-recommendations/advisor-security-recommendations.png)

推奨事項の適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)」を参照してください。

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Azure Advisor のセキュリティに関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション ウィンドウで **[その他のサービス]** をクリックし、サービス メニュー ウィンドウで **[監視と管理]** までスクロールし、**[Azure Advisor]** をクリックします。 これにより、Advisor ダッシュボードが起動します。 
3. Advisor ダッシュボードで、**[セキュリティ]** タブをクリックし、推奨事項を受け入れるサブスクリプションを選択します。

> [!NOTE]
> Advisor は、**所有者、共同作成者、または閲覧者**の役割が割り当てられているサブスクリプション向けの推奨事項を生成します。  

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、次のリソースをご覧ください。
-  [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
-  [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
-  [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)
 



<!--HONumber=Nov16_HO3-->


