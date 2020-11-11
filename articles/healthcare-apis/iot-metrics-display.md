---
title: Azure IoT Connector for FHIR (プレビュー) のメトリックを表示および構成する
description: この記事では、Azure IoT Connector for FHIR (プレビュー) のメトリックの表示と構成について説明します。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133414"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Azure IoT Connector for FHIR (プレビュー) のメトリックを表示および構成する 

この記事では、Azure IoT Connector for FHIR* のメトリックを表示および構成する方法について説明します。 

> [!TIP]
> メトリック データのエクスポートを設定する方法については、「[診断設定を使用して Azure IoT Connector for FHIR (プレビュー) のメトリックをエクスポートする](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export)」のガイダンスを参照してください。

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) のメトリックを表示する
1. IoT コネクタのメトリックを表示するには、Azure portal で Azure API for FHIR サービスを選択します。 

2. **[メトリック]** に移動します 

3. **[IoT Connector]\(IoT コネクタ\)** タブを選択します。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. [IoT Connector]\(IoT コネクタ\) を選択してそのメトリックを表示します (例: この Azure API for FHIR サービスに関連付けられている IoT コネクタ (4) があります)。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> **[カスタム]** タブを使用すると、IoT コネクタのメトリックを表示する特定の日時の組み合わせを作成できます。

5. 表示する IoT コネクタのメトリックの期間を選択します (例: 1 時間、24 時間、7 日間、またはカスタム)。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="IoT Connector3" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) のメトリックの種類 
表示される IoT コネクタのメトリックは次のとおりです。

|Metrics Type (メトリックの種類)|Metrics Purpose (メトリックの目的)| 
|-----------|--------------|
|Number of Incoming Messages (受信メッセージの数)|受信した未加工の受信メッセージの数 (例: デバイス イベント)。|
|Number of Normalized Messages (正規化されたメッセージの数)|正規化されたメッセージの数。|
|Number of Message Groups (メッセージ グループの数)|指定された時間枠内にメッセージが集計されたグループの数。|
|Average Normalized Stage Latency (正規化されたステージの平均待機時間)|正規化ステージの平均待機時間。 正規化ステージは、未加工の受信メッセージに対して正規化を実行することです。|
|Average Group Stage Latency (グループ ステージの平均待機時間)|グループ ステージの平均待機時間。 グループ ステージは、正規化されたメッセージに対してバッファーリング、集計、およびグループ化を実行することです。| 
|Total Error Count|エラーの総数。| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Azure IoT Connector for FHIR (プレビュー) のメトリックの絞り込みと構成
この例では、 **[Number of Incoming Messages]\(受信メッセージの数\)** メトリックに焦点を当てます。

1. 焦点を当てる時点を選択します。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="IoT Connector4" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. この画面から、 **[メトリックの追加]** 、 **[フィルターの追加]** 、 **[Apply splitting]\(分割の適用\)** を行ってさらにカスタマイズすることができます。 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT Connector5" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>まとめ 
データ プレーンのメトリックにアクセス可能であることは、監視とトラブルシューティングに欠かせません。  Azure IoT Connector for FHIR は、メトリックを使用してこのようなアクションを実行する際に役立ちます。 

## <a name="next-steps"></a>次の手順

Azure IoT Connector for FHIR についてよく寄せられる質問を確認します。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR についてよく寄せられる質問](fhir-faq.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。

FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。