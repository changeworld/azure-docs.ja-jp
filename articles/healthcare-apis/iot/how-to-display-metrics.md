---
title: IoT コネクタのメトリックログの表示-Azure の医療 Api
description: この記事では、IoT コネクタメトリックを表示する方法について説明します
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: 6718270b7b996813f4503e58cf2609e7eb3add00
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733471"
---
# <a name="how-to-display-iot-connector-metrics"></a>IoT コネクタのメトリックを表示する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure portal で IoT コネクタのメトリックを表示する方法について説明します。 

## <a name="display-metrics"></a>メトリックの表示

1. Azure の医療 Api ワークスペースで、[ **IoT コネクタ**] を選択します。 

     :::image type="content" source="media\iot-metrics\iot-workspace-displayed-with-connectors-button.png" alt-text="[IoT コネクタ] ボタンを選択します。" lightbox="media\iot-metrics\iot-connectors-button.png"::: 

2. メトリックを表示する IoT コネクタを選択します。

    :::image type="content" source="media\iot-metrics\iot-connector-select.png" alt-text="メトリックを表示する IoT コネクタを選択します。" lightbox="media\iot-metrics\iot-connector-select.png":::
    
3. [IoT コネクタ] ページ内の **メトリック** を選択します。

   :::image type="content" source="media\iot-metrics\iot-select-metrics.png" alt-text="[メトリック] ボタンを選択します。" lightbox="media\iot-metrics\iot-metrics-button.png"::: 

4. [メトリック] ページで、IoT コネクタに対して表示するメトリックを作成できます。 この例では、次の選択を選択します。

    * **スコープ** = IoT コネクタ名 (**既定**)
    * **メトリック名前空間** = 標準メトリック (**既定**) 
    * **メトリック** = 表示する IoT コネクタのメトリック。 この例では、 **受信メッセージの数** を選択します。
    * **集計** = メトリックをどのように表示するかを示します。 この例では、[ **カウント**] を選択します。 

    :::image type="content" source="media\iot-metrics\iot-select-metrics-to-display.png" alt-text="表示するメトリックを選択します。" lightbox="media\iot-metrics\iot-metrics-selection-close-up.png"::: 

5. Azure portal に表示される **受信メッセージの数** について、IoT コネクタのメトリックが表示されるようになりました。

    > [!TIP]
    > [ **メトリックの追加** ] ボタンを選択し、選択を行うことで、メトリックを追加できます。

    :::image type="content" source="media\iot-metrics\iot-metrics-add-button.png" alt-text="メトリックをさらに追加するには、[メトリックの追加] ボタンを選択します。" lightbox="media\iot-metrics\iot-add-metric-button.png":::

    > [!IMPORTANT]
    > [メトリック] ページから移動すると、メトリックの設定が失われ、再作成する必要があります。 今後の表示のために IoT コネクタメトリックを保存する場合は、タイルとして Azure ダッシュボードにピン留めすることができます。

## <a name="pinning-metrics-tile-on-azure-portal-dashboard"></a>Azure portal ダッシュボードのメトリックタイルのピン留め

1. メトリックタイルを Azure portal ダッシュボードにピン留めするには、[ **ダッシュボードにピン留め** する] ボタンを選択します。

    :::image type="content" source="media\iot-metrics\iot-metrics-select-add-pin-to-dashboard.png" alt-text="[ダッシュボードにピン留めする] ボタンを選択します。" lightbox="media\iot-metrics\iot-pin-to-dashboard-button.png":::

2. IoT コネクタのメトリックを表示するダッシュボードを選択します。 この例では、という名前のプライベートダッシュボードを使用し `IoT connector Metrics` ます。 [ **ピン留め** する] を選択して、メトリックタイルをダッシュボードに追加します。

    :::image type="content" source="media\iot-metrics\iot-select-pin-to-dashboard.png" alt-text="ダッシュボードとピン留めのボタンを選択して、ダッシュボードのピン留めプロセスを完了します。" lightbox="media\iot-metrics\iot-select-pin-to-dashboard.png":::

3. メトリックタイルがダッシュボードに正常に追加されたことを示す確認メッセージが表示されます。

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-pinned-successful.png" alt-text="メトリックタイルがダッシュボードに正常にピン留めされました。" lightbox="media\iot-metrics\iot-select-dashboard-pinned-successful.png":::

4. 確認が完了したら、[ **ダッシュボード**] を選択します。

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-with-metrics-tile.png" alt-text="[ダッシュボード] ボタンを選択します。" lightbox="media\iot-metrics\iot-dashboard-button.png":::

5. メトリックタイルをピン留めしたダッシュボードを選択します。 この例では、ダッシュボードは `IoT connector Metrics` です。 ダッシュボードに、前の手順で作成した IoT コネクタのメトリックタイルが表示されます。

    :::image type="content" source="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png" alt-text="ピン留めされた IoT コネクタのメトリックタイルを含むダッシュボード。" lightbox="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png":::

## <a name="conclusion"></a>まとめ 

監視とトラブルシューティングには、メトリックへのアクセスが不可欠です。  IoT コネクタは、メトリックを使用してこれらのアクションを実行するのに役立ちます。 

## <a name="next-steps"></a>次の手順

IoT コネクタについてよく寄せられる質問を確認してください。

>[!div class="nextstepaction"]
>[IoT コネクタに関する Faq](iot-connector-faqs.md)

(FHIR&#174;) HL7 の登録商標であり、HL7 のアクセス許可と共に使用されます。
