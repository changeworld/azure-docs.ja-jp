---
title: IoT コネクタのメトリック ログを表示する - Azure Healthcare API
description: この記事では、IoT コネクタのメトリックを表示する方法について説明します
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 88e5310a53bfd5fed79ae0a9a6efc06d91fdb0cf
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2021
ms.locfileid: "132933953"
---
# <a name="how-to-display-iot-connector-metrics"></a>IoT コネクタのメトリックを表示する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、IoT コネクタのメトリックを Azure portal に表示する方法について学習します。 

## <a name="display-metrics"></a>メトリックの表示

1. Azure Healthcare API ワークスペース内で **、[IoT コネクタ] を選択します**。 

     :::image type="content" source="media\iot-metrics\iot-workspace-displayed-with-connectors-button.png" alt-text="[IoT コネクタ] ボタンを選択します。" lightbox="media\iot-metrics\iot-connectors-button.png"::: 

2. メトリックを表示する IoT コネクタを選択します。

    :::image type="content" source="media\iot-metrics\iot-connector-select.png" alt-text="メトリックを表示する IoT コネクタを選択します。" lightbox="media\iot-metrics\iot-connector-select.png":::
    
3. [IoT **コネクタ]** ページで [メトリック] を選択します。

   :::image type="content" source="media\iot-metrics\iot-select-metrics.png" alt-text="[メトリック] ボタンを選択します。" lightbox="media\iot-metrics\iot-metrics-button.png"::: 

4. [メトリック] ページから、IoT コネクタに表示するメトリックを作成できます。 この例では、次の選択を選択します。

    * **Scope** = IoT コネクタ名 (**既定**)
    * **メトリック名前空間** = 標準メトリック (**既定**) 
    * **メトリック** = 表示する IoT コネクタのメトリック。 この例では、受信メッセージの **数 を選択します**。
    * **集計** = メトリックを表示する方法。 この例では、 [カウント] を **選択します**。 

    :::image type="content" source="media\iot-metrics\iot-select-metrics-to-display.png" alt-text="表示するメトリックを選択します。" lightbox="media\iot-metrics\iot-metrics-selection-close-up.png"::: 

5. これで、IoT コネクタの [受信メッセージの数] のメトリックが、Azure portal に表示されます。

    > [!TIP]
    > [メトリックの追加] ボタンを選択して選択することで、メトリックを追加できます。

    :::image type="content" source="media\iot-metrics\iot-metrics-add-button.png" alt-text="[メトリックの追加] ボタンを選択して、メトリックを追加します。" lightbox="media\iot-metrics\iot-add-metric-button.png":::

    > [!IMPORTANT]
    > [メトリック] ページを離れた場合、メトリックの設定は失われ、再作成する必要があります。 将来表示するために IoT コネクタのメトリックを保存する場合は、タイルとして Azure ダッシュボードにピン留めできます。

## <a name="pinning-metrics-tile-on-azure-portal-dashboard"></a>ダッシュボードの [メトリック] タイルAzure portalピン留めする

1. メトリック タイルをダッシュボードにピン留めするにはAzure portalダッシュボードにピン留 **めする] ボタンを選択** します。

    :::image type="content" source="media\iot-metrics\iot-metrics-select-add-pin-to-dashboard.png" alt-text="[ダッシュボードにピン留めする] ボタンを選択します。" lightbox="media\iot-metrics\iot-pin-to-dashboard-button.png":::

2. IoT コネクタのメトリックを表示するダッシュボードを選択します。 この例では、 という名前のプライベート ダッシュボードを使用します `IoT connector Metrics` 。 [ **ピン留め** ] を選択して、ダッシュボードに [メトリック] タイルを追加します。

    :::image type="content" source="media\iot-metrics\iot-select-pin-to-dashboard.png" alt-text="ダッシュボードとピン留めボタンを選択して、ダッシュボードのピン留めプロセスを完了します。" lightbox="media\iot-metrics\iot-select-pin-to-dashboard.png":::

3. [メトリック] タイルがダッシュボードに正常に追加されたことを確認するメッセージが表示されます。

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-pinned-successful.png" alt-text="メトリック タイルがダッシュボードに正常にピン留めされました。" lightbox="media\iot-metrics\iot-select-dashboard-pinned-successful.png":::

4. 確認が正常に完了したら、[ダッシュボード] を **選択します**。

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-with-metrics-tile.png" alt-text="[ダッシュボード] ボタンを選択します。" lightbox="media\iot-metrics\iot-dashboard-button.png":::

5. [メトリック] タイルをピン留めしたダッシュボードを選択します。 この例では、ダッシュボードは です `IoT connector Metrics` 。 ダッシュボードには、前の手順で作成した IoT コネクタの [メトリック] タイルが表示されます。

    :::image type="content" source="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png" alt-text="ピン留めされた IoT コネクタの [メトリック] タイルを含むダッシュボード。" lightbox="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png":::

> [!TIP]
> 一般的なエラーと [問題の修正については、IoT](./iot-troubleshoot-guide.md) コネクタのトラブルシューティング ガイドを参照してください。

## <a name="conclusion"></a>まとめ 

メトリックへのアクセス権を持つことは、監視とトラブルシューティングに不可欠です。  IoT コネクタを使用すると、メトリックを使用してこれらのアクションを実行できます。 

## <a name="next-steps"></a>次のステップ

IoT コネクタに関してよく寄せられる質問を確認してください。

>[!div class="nextstepaction"]
>[IoT コネクタに関する FAQ](iot-connector-faqs.md)

(FHIR&#174;) は HL7 の商標であり、HL7 の許可を得て使用されます。
