---
title: 環境のセキュリティ アラート
description: この記事では、DevTest Labs で環境のセキュリティの警告を表示し、適切なアクションを実行する方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0dccf45f32d6eaf272a464198edd0c5043dbc364
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325165"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs での環境のセキュリティの警告
ラボ ユーザーは、ラボ環境に対する Microsoft Defender for Cloud のアラートを表示できるようになりました。 Defender for Cloud は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。 Defender for Cloud には、優先順位の付いたセキュリティ アラートの一覧が、迅速に問題を調査するために必要な情報と、攻撃の修復方法に関する推奨事項と共に表示されます。 詳細については、[Microsoft Defender for Cloud のセキュリティ アラート](../security-center//security-center-alerts-overview.md)に関するページを参照してください。  


## <a name="prerequisites"></a>前提条件
現在、ご利用のラボにデプロイされている、サービスとしてのプラットフォーム (PaaS) 環境についてのみ、セキュリティの警告を表示することができます。 この機能をテストまたは使用するには、[ご利用のラボに環境をデプロイします](devtest-lab-create-environment-from-arm.md)。 

## <a name="view-security-alerts-for-an-environment"></a>環境のセキュリティの警告を表示する

1. ご利用のラボのホームページで、左側のメニューにある **[セキュリティの警告]** を選択します。 セキュリティの警告 (高、中、低) の数が表示されるはずです。 [警告が分類される方法](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)について詳細を確認してください。

    ![セキュリティの警告 - 概要](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 最後の列の 3 つのドット (...) を右クリックし、 **[セキュリティの警告の表示]** を選択します。 

    ![[セキュリティの警告] ページのスクリーンショット。[セキュリティの警告の表示] が選択されています。](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 警告と Advisor の推奨事項の詳細が表示されます。 詳細については、「[Microsoft Defender for Cloudでセキュリティ アラートの管理と対応を行う](../security-center/security-center-managing-and-responding-alerts.md)」を参照してください。

    ![セキュリティの警告の表示](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>次のステップ
環境の詳細については、次の記事を参照してください。

- [Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)
- [パブリックな環境の構成と使用](devtest-lab-configure-use-public-environments.md)
