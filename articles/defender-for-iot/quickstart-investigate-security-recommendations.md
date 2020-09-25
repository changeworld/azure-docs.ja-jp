---
title: クイック スタート:セキュリティに関する推奨事項の調査
description: Defender for IoT セキュリティ サービスを使用してセキュリティに関する推奨事項を調査します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 859f1c4a1ed1b3d9139307c52f44a14e3089e31f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945511"
---
# <a name="quickstart-investigate-security-recommendations"></a>クイック スタート:セキュリティに関する推奨事項の調査


セキュリティの状態を改善し、IoT ソリューション全体の攻撃面を小さくする最善の方法は、Defender for IoT によって提示される推奨事項と軽減策を適時に分析することです。

このクイックスタートでは、各 IoT セキュリティ推奨事項から得られる情報について確認すると共に、各推奨事項および関連デバイスの詳細情報にドリルダウンし、それを使用してリスクを軽減する方法について説明します。

では、始めましょう。

## <a name="investigate-new-recommendations"></a>新しい推奨事項を調査する

IoT Hub の推奨情報には、IoT Hub について集約されたセキュリティに関する推奨事項がすべて一覧表示されます。

1.  Azure portal で、新規の推奨事項を調べる  **IoT ハブ** を開きます。

1.   **[セキュリティ]**   メニューから  **[Recommendations]\(推奨事項\)** を選択します。 IoT ハブのすべてのセキュリティ推奨事項が表示されます。 **[新規]**   フラグが付いた推奨事項は、過去 24 時間に発行された推奨事項であることを示しています。 

    [ ![ASC for IoT でセキュリティに関する推奨事項を調査する](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  一覧から推奨事項を選択して推奨事項の詳細を開き、推奨事項の各細目にドリルダウンします。

## <a name="security-recommendation-details"></a>セキュリティに関する推奨事項の詳細

集約された各推奨事項を開くと、その詳しい説明、修復手順、推奨事項をトリガーした各デバイスの ID が表示されます。 また、推奨事項の重要度、および Log Analytics を使用した調査用の直接アクセス リンクも表示されます。

1.   **[IoT Hub]**  \> **[セキュリティ]**  \> **[Recommendations]\(推奨事項\)**   の一覧から、セキュリティに関する推奨事項をどれか選択して開きます。

1.  集計期間内にこの推奨事項を発行したすべてのデバイスについて、推奨事項の**説明**、 **重要度**、 **デバイスの詳細** を確認します。 

1.  推奨事項の細目を確認した後、 **[Manual remediation step]\(手動による修復手順\)**   に示されている手順を使用して、推奨事項の原因となった問題を修復したり解決したりすることができます。 

    [ ![ASC for IoT でセキュリティに関する推奨事項を修復する](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  ドリルダウン ページで目的のデバイスを選択し、個別のデバイスについて推奨事項の詳細を調査します。

    [ ![ASC for IoT でデバイスのセキュリティに関する推奨事項を個別に調査する](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  さらに調査が必要な場合は、リンクを使用して  **Log Analytics で推奨事項を調査します** 。 


## <a name="next-steps"></a>次の手順

次の記事に進んで、カスタム アラートの作成方法を学習してください。

> [!div class="nextstepaction"]
> [カスタム アラートの作成](quickstart-create-custom-alerts.md)
