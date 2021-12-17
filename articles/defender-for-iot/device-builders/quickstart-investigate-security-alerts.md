---
title: クイック スタート:セキュリティ アラートを調べる
description: お使いの IoT デバイスに関する Defender for IoT セキュリティ アラートの理解を深め、ドリルダウンと調査を行います。
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: c55392650edf85c753bcde32a03e8ad313ebd148
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331511"
---
# <a name="quickstart-investigate-security-alerts"></a>クイック スタート:セキュリティ アラートを調べる

お使いの IoT ソリューション全体にわたってコンプライアンスと保護を確保するための最善の方法は、Defender for IoT から発行されたアラートを定期的に調べ、修復を行うことです。

## <a name="investigate-new-security-alerts"></a>新規のセキュリティ アラートを調べる

IoT ハブのセキュリティ アラートの一覧には、IoT ハブについての集計済みセキュリティ アラートがすべて表示されます。 

1. Azure portal で、新規のアラートを調べる **IoT ハブ** を開きます。

1. **[セキュリティ]** メニューの **[アラート]** を選択します。 IoT ハブのすべてのセキュリティ アラートが表示されます。 **[新規]** フラグが付いたアラートは、過去 24 時間に発行されたアラートであることを示しています。

    :::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="新規フラグを使用して、新規の IoT セキュリティ アラートを調べます":::

1. 一覧からアラートを選択してアラートの詳細を開き、アラートの各細目を理解します。 

## <a name="security-alert-details"></a>Security alert details

各集計済みアラートを開くと、アラートについての詳しい説明、修復手順、アラートをトリガーした各デバイスのデバイス ID が表示されます。 アラートの重要度と直接の調査は Log Analytics を使用して表示できます。 

1. **[IoT Hub]** 、 **[セキュリティ]** 、 **[アラート]** の順に移動します。 

1. 一覧からセキュリティ アラートを選択して開きます。 

1. 集計期間内にこのアラートを発行したすべてのデバイスについて、アラートの **説明**、**重要度**、**検出元**、**デバイスの詳細** を確認します。

    :::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="集計済みアラートで、各デバイスの詳細を調査して確認します"::: 

1. アラートの細目を確認した後、 **[Manual remediation step]\(手動による修復手順\)** に示されている手順を使用し、アラートの原因となった問題を修復したり解決したりできます。

    :::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="手動による修復手順に従って、デバイスのセキュリティ アラートを解決または修復します":::

1. さらに調査が必要な場合は、リンクを使用して **Log Analytics でアラートを調査します**。
 
    :::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="アラートをさらに調べるには、画面に示されている、Log Analytics を使用した調査リンクを使用します":::

## <a name="next-steps"></a>次のステップ

セキュリティ アラートの種類と利用可能なカスタマイズの詳細については、次の記事に進んでください。

> [!div class="nextstepaction"]
> [IoT セキュリティ アラートについて](concept-security-alerts.md)
