---
title: Azure DevTest Labs での環境のセキュリティの警告
description: この記事では、DevTest Labs で環境のセキュリティの警告を表示し、適切なアクションを実行する方法について説明します。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992022"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs での環境のセキュリティの警告
この記事では、DevTest Labs で環境のセキュリティの警告を表示する方法について説明します。 

## <a name="prerequisites"></a>前提条件
現在、ご利用のラボにデプロイされている環境についてのみ、セキュリティの警告を表示することができます。 この機能をテストまたは使用するには、ご利用のラボに環境をデプロイします。 

## <a name="view-security-alerts-for-an-environment"></a>環境のセキュリティの警告を表示する

1. ご利用のラボのホームページで、左側のメニューにある **[セキュリティの警告]** を選択します。 セキュリティの警告 (高、中、低) の数が表示されるはずです。

    ![セキュリティの警告 - 概要](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 最後の列の 3 つのドット (...) を右クリックし、 **[セキュリティの警告の表示]** を選択します。 

    ![セキュリティの警告の表示](./media/environment-security-alerts/view-security-alerts-menu.png)
3. 警告と Advisor の推奨事項の詳細が表示されます。 

    ![セキュリティの警告の表示](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>次のステップ
環境の詳細については、次の記事を参照してください。

- [Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)
- [パブリックな環境の構成と使用](devtest-lab-configure-use-public-environments.md)
