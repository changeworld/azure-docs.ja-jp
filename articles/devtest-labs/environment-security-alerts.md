---
title: 環境のセキュリティ アラート
description: この記事では、DevTest Labs で環境のセキュリティの警告を表示し、適切なアクションを実行する方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 57f1a0b240035d75746b9d23482446be71d1b1f9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398043"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs での環境のセキュリティの警告
ラボ ユーザーは、ラボに対する Microsoft Defender for Cloud のアラートを確認できます。 ログ データの収集、分析、統合は、Defender for Cloud によって自動的に行われます。 データは、Azure リソースやネットワークから収集されるほか、ファイアウォール、Endpoint Protection など、接続されたソリューションからも収集されます。 Defender for Cloud は実際の脅威を検出し、擬陽性を減らすようになっています。 Defender for Cloud には、次のような働きがあります。

- 優先度付けされたセキュリティ アラートをリストする。
- 問題を迅速に調査するための情報を提供する。
- 攻撃の修復方法について推奨事項を提示する。

詳細については、[Microsoft Defender for Cloud のセキュリティ アラート](../security-center//security-center-alerts-overview.md)に関するページを参照してください。


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
