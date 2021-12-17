---
title: IoT コネクタと Azure Machine Learning サービス-Azure の医療 api
description: この記事では、IoT コネクタと Azure Machine Learning サービスの使用方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 8150734243fda78805b5ef4cbf3ab318222b21cb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894864"
---
# <a name="iot-connector-and-azure-machine-learning-service"></a>IoT コネクタと Azure Machine Learning サービス

この記事では、IoT コネクタと Azure Machine Learning サービスの使用について説明します。

## <a name="iot-connector-and-azure-machine-learning-service-reference-architecture"></a>IoT コネクタと Azure Machine Learning サービス参照アーキテクチャ

Iot コネクタを使用すると、IoT デバイスを高速医療相互運用性リソース (FHIR&#174;) サービスとシームレスに統合できます。 この参照アーキテクチャは、医療関連のインターネット (IoMT) プロジェクトの導入を促進するように設計されています。 このソリューションでは、Machine Learning (ML) コンピューティングに Azure Databricks を使用します。 ただし、Kubernetes またはパートナー ML ソリューションを使用した Azure ML サービスは、Machine Learning スコアリング環境に適合する可能性があります。

4本の線の色は、データ体験のさまざまな部分を示しています。

- **Blue** = IoT data to FHIR service。
- **緑** = IoT データをスコア付けするためのデータパス
- **赤** = データのホットパス。患者のリスクを nuance に伝えます。 ホットパスの目標は、可能な限りリアルタイムに近いものにすることです。
- **オレンジ** = データのウォームパス。 引き続き、患者の治療で nuance をサポートしています。 通常、データ要求は手動で、または更新スケジュールによってトリガーされます。

:::image type="content" source="media/iot-concepts/iot-connector-machine-learning.png" alt-text="IoT コネクタと Machine Learning サービス参照アーキテクチャのスクリーンショット。" lightbox="media/iot-concepts/iot-connector-machine-learning.png":::

**データの取り込み-手順 1 ~ 5**

1. Azure IoT hub/Azure IoT Edge に送信された IoT デバイスまたはデバイスゲートウェイからのデータ。
2. Azure IoT Hub に送信された Azure IoT エッジからのデータ。
3. デバイス管理のためにセキュリティで保護されたストレージ環境に送信される未加工の IoT デバイスデータのコピー。
4. PHI IoMT ペイロードは Azure IoT hub から IoT コネクタに移動します。 複数の Azure サービスは、1つの IoT コネクタアイコンで表されます。
5. 3つの部分 (番号 5): a。 IoT コネクタは、FHIR サービスの患者リソースを要求します。 b. FHIR サービスは、患者のリソースを IoT コネクタに送り返します。 c. IoT 患者の観測は、FHIR サービスのレコードです。

**Machine Learning と AI データルート–手順 6 ~ 11**

6. Azure 関数 (ML 入力) に送信された、グループ化されていないデータストリームを正規化しました。
7. Azure Function (ML 入力) は、IoMT ペイロードとマージするように患者のリソースを要求します。
8. PHI を使用した IoMT ペイロードは、Machine Learning のコンピューティングおよびストレージに配布するために Event Hub に送信されます。
9. PHI IoMT ペイロードは、より長い時間ウィンドウでのスコアリング監視のために Azure Data Lake Storage Gen 2 に送信されます。
10. PHI IoMT ペイロードは、ウィンドウ、データの調整、データスコアリングのために Azure Databricks に送信されます。
11. Azure Databricks は、必要に応じて data lake からより多くの患者データを要求します。 a. また Azure Databricks は、スコア付けされたデータのコピーを data lake に送信します。

**通知とケアの調整-手順 12-18**

**ホットパス**

12. Azure Databricks は、Azure 関数 (ML 出力) にペイロードを送信します。
13. RiskAssessment または Flag リソースが FHIR サービスに送信されました。 a. 各監視ウィンドウについて、RiskAssessment リソースが FHIR サービスに送信されます。 b. リスク評価が許容範囲外の監視ウィンドウでは、フラグリソースも FHIR サービスに送信する必要があります。
14. 適切なケアチームにルーティングするためにデータリポジトリに送信されるスコア付けされたデータ。 Azure SQL Server は、Power BI とのネイティブな相互作用のために、この設計で使用されるデータリポジトリです。
15. Power BIダッシュボードは、15分以内にリスク評価出力と共に更新されます。

**ウォームパス**

16. Power BI は、データ更新スケジュールのダッシュボードを更新します。 通常、更新間隔は15分より長くなります。
17. 現在のデータを使用して、ケアチームアプリを設定します。
18. 医療向け Microsoft Teams の患者アプリを使用して慎重に調整します。

## <a name="next-steps"></a>次のステップ

この記事では、IoT コネクタと Machine Learning サービスの統合について学習しました。 IoT コネクタの概要については、「」を参照してください。

>[!div class="nextstepaction"]
>[IoT コネクタの概要](iot-connector-overview.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
