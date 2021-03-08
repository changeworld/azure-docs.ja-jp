---
title: 従量制課金に対する異常検出 | Azure Marketplace
description: 従量制課金に対する自動の異常検出によって、ご自身の顧客がコマーシャル マーケットプレースのオファーの従量制課金使用量に応じて正しく請求されるようにする方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989220"
---
# <a name="anomaly-detection-for-metered-billing"></a>従量制課金に対する異常検出

この記事では、従量制課金使用量に応じて顧客に正しく請求できるようにするための、マーケットプレースの測定サービスと、関連する自動化された異常検出機能について詳しく説明します。 現在、従量制課金オプションは、[サービスとしてのソフトウェア](plan-saas-offer.md) (SaaS) オファーと、マネージド アプリケーション プランを提供する [Azure アプリケーション](plan-azure-application-offer.md#types-of-plans)に対して使用できます。 このオプションを使用すると、パートナーはコマーシャル マーケットプレース プログラムで標準以外の単位に応じて課金されるオファーを作成できます。

SaaS およびマネージド アプリケーション用にカスタム メーターを配置しているパートナーの場合、パートナー センターで特定の "_カスタム メーター_" についての "_超過分イベント_" の異常として、予想される利用行動からの逸脱が表示されることがあります。 このリスクを軽減するために、パートナー センターには機械学習アルゴリズムを適用した異常検出サービスが使用されており、正常な従量制課金の動作を識別し、従量制課金使用量を分析することで、最小限のユーザー操作で異常が検出されます。 パートナー センターには、報告された使用量が予想される使用量を超えている場合は発行元に通知することを目的に、従量制課金使用量のデータセットに対して "_異常検出モデル_" が使用されています。

## <a name="usability-experience"></a>ユーザビリティ エクスペリエンス

Microsoft は、Microsoft からお客様への請求の前に SaaS または Azure マネージド アプリケーション オファーの顧客の超過分使用量を報告することをパートナーに委任しています。 間違った使用量が報告された場合、顧客が間違った請求書を受け取る可能性があり、Microsoft とパートナーの両方の信頼性が損なわれることになります。

これを軽減するために、SaaS アプリと Azure アプリケーションのマネージド アプリケーション プランの両方に対して自動の異常検出機能が用意されています。 この機能は、従量制課金に備えて使用量を事前に監視し、予想される範囲内での使用量の予想値を予測する、機械学習モデルです。 使用量が想定される範囲外の場合は異常として扱われ、パートナー センターのコマーシャル マーケットプレース プログラムの [オファーの概要] ページにアラート通知が表示されます。

超過分の使用量は機械学習モデルで毎日分析されます。 発行元は、各オファーのカスタム測定ディメンションについて、顧客の超過分の使用量に対して報告されたすべての異常を確認できます。

### <a name="view-and-manage-metered-usage-anomalies"></a>従量制課金使用量の異常の表示と管理

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[分析]** の順に選択します。
1. **[Metered usage anomalies]\(従量制課金使用量の異常\)** タブを選択します。

    [![[利用状況] ページの [Metered usage anomalies]\(従量制課金使用量の異常\) タブを示しています。](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    "***図 1:[Metered usage anomalies]\(従量制課金使用量の異常\) タブ***

1. 従量制課金に対して検出された使用量の異常については、発行元として、その異常が本当なのかどうかを調査して確認するように求められます。 診断を確認するには、 **[Mark as anomaly]\(異常としてマーク\)** を選択します。

     [![[Mark as anomaly]\(異常としてマーク\) ダイアログ ボックスを示しています。](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    "***図 2:[Mark as anomaly]\(異常としてマーク\) ダイアログ ボックス***

1. 検出された超過分使用量の異常が正規のものではないと思われる場合は、そのフィードバックを提供できます。そのためには、パートナー センターで異常のフラグが設定された特定の超過分使用量について、 **[Not an anomaly]\(異常ではない\)** を選択します。

    [![[Why is it not an anomaly]\(異常ではない理由\) ダイアログ ボックスを示しています。](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    "***図 3:[Why is it not an anomaly?]\(異常ではない理由\) ダイアログ ボックス***

1. ページを下にスクロールすると、未確認の異常の一覧が表示されます。 一覧には、未確認のまま残っている異常が表示されます。 パートナー センターでフラグが設定された任意の異常を正規のものまたは誤りとしてマークすることができます。

   [![パートナー センターの [利用状況] ページでの未確認の異常の一覧を示しています。](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    "***図 4:パートナー センターでの未確認の異常の一覧***

1. また、超過分の使用量に対して実行したアクションを示す異常のアクション ログも表示されます。 アクション ログでは、どの超過分使用量イベントが正規のものまたは誤りとしてマークされたかを確認できます。

   [![[利用状況] ページの異常のアクション ログを示しています。](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox)
   ***図 5:異常のアクション ログ***

1. パートナー センターの分析で、エクスポート レポートにおける超過分使用量イベントの修正再表示はサポートされていません。 パートナー センターで、異常に対して修正された超過分の使用量を入力できます。詳細は調査のために Microsoft チームに渡されます。 調査に基づいて、超過料金が発生した顧客に Microsoft から必要に応じてクレジット返金が発行されます。 フラグが設定された異常のどれかを選択したときに **[Mark as anomaly]\(異常としてマーク\)** を選択すると、使用量の超過分の異常を正規のものとしてマークできます。

   [![[Mark as anomaly]\(異常としてマーク\) ダイアログ ボックスを示しています。](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox)
   ***図 6: [Mark as anomaly]\(異常としてマーク\) ダイアログ ボックス***

パートナー センターで超過分の使用量に対して初めて不規則のフラグが設定された場合、異常を正規のものまたは誤りとしてマークするために、そのインスタンスから 30 日の期間が与えられます。 30 日の期間が終了すると、発行元として異常に対処することはできなくなります。

> [!IMPORTANT]
> 過少報告された超過分の使用量単位は、修正再表示および財務調整の対象にできません。

選択した計算期間のすべての異常 (確認済みまたはそれ以外) を確認できます。 計算期間は、過去 30 日間、過去 60 日間、過去 90 日間とさまざまです。

> [!IMPORTANT]
> 例外が最初にパートナー センターで報告されてから 30 日以内に、フラグが設定された異常を処理することが必要とされます。

ウィジェットのフィルター モーダルを使用すると、個々のオファーと個々のカスタム メーターを選択できます。

超過分の使用量を自分で異常としてマークするか、異常としてフラグ設定したモデルを正規のものとして確認した後、選択を "異常ではない" に変更することはできません。

> [!IMPORTANT]
> 過剰請求の状況が発生した場合は、超過分の使用量を再送信できます。

## <a name="see-also"></a>関連項目
- [コマーシャル マーケットプレースの測定サービスを使用した SaaS の従量制課金](./partner-center-portal/saas-metered-billing.md)
- [マネージド アプリケーションの従量制課金](./partner-center-portal/azure-app-metered-billing.md)
