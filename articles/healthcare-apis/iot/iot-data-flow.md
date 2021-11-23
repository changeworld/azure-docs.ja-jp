---
title: IoT コネクタのデータ フロー - Azure Healthcare API
description: IoT コネクタのデータ フローについて理解する。 IoT コネクタは、IoMT データの取り込み、正規化、グループ化、変換、および FHIR サービスへの永続化を行います。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: e442cad97c01c4cd15c931dea45a849cc723cb76
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2021
ms.locfileid: "132954271"
---
# <a name="iot-connector-data-flow"></a>IoT コネクタのデータ フロー

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、IoT コネクタのデータ フローの概要について説明します。 デバイス データを 高速ヘルスケア相互運用性リソース (FHIR&#174;) ベースの Observation リソースに変換する IoT コネクタ内のさまざまなデータ処理ステージについて [学習](https://www.hl7.org/fhir/observation.html) します。

正常性関連デバイスまたは医療デバイスからのデータは、IoT コネクタがデータを FHIR に変換し、データが FHIR サービスに格納され、FHIR サービスからアクセスされるパスを通過します。 正常性データ パスは、取り込み、正規化、グループ化、変換、永続化の順に次の手順に従います。 このデータ フローでは、インジェストの最初の手順でデバイスから正常性データが取得されます。 データを受信すると、ユーザーが選択したスキーマ テンプレートまたはユーザーが作成したスキーマ テンプレートごとに処理または正規化されます。そのため、正常性データの処理が簡単で、グループ化できます。 正常性データは、3 つの操作パラメーターにグループ化されます。 正常性データは、正規化およびグループ化された後、FHIR 変換先マッピングを使用して処理または変換し、FHIR サービスに保存または永続化できます。

この記事では、データ フローの各ステップについて詳しい説明を行います。 次の手順では、デバイス マッピング (正規化手順) と FHIR 変換先マッピング (変換手順) を使用して [IoT](deploy-iot-connector-in-azure.md) コネクタをデプロイする方法を示します。

次のセクションでは、IoMT (Internet of Medical Things) データがイベント ハブから IoT コネクタに受信された後に通過するステージについて説明します。

:::image type="content" source="media/iot-data-flow/iot-data-flow.png" alt-text="IoT デバイスからイベント ハブへの IoMT データ フロー。IoT コネクタによって取り込まれた IoMT データは、正規化され、グループ化され、変換され、FHIR サービスに保持されます。" lightbox="media/iot-data-flow/iot-data-flow.png":::

## <a name="ingest"></a>取り込み
取り込みは、デバイス データを IoT コネクタに受信する最初のステージです。 デバイス データのインジェスト エンドポイントは、次の[Azure Event Hubs。](../../event-hubs/index.yml) Azure Event Hubsプラットフォームでは、1 秒あたり数百万件のメッセージを受信して処理する機能を備えた、高いスケールとスループットがサポートされています。 また、IoT コネクタでメッセージを非同期的に使用することもできます。これにより、デバイス データが処理されるのをデバイスが待機する必要がなされます。

> [!NOTE]
> 現時点でデバイス データの形式としてサポートされているは JSON のみです。

## <a name="normalize"></a>Normalize (正規化)
Normalize は、デバイス データが上記のイベント ハブから取得され、デバイス マッピングを使用して処理される次のステージです。 このマッピング プロセスにより、デバイス データが、正規化されたスキーマに変換されます。 

正規化プロセスは、後の段階でデータ処理を簡略化するだけでなく、1 つの入力メッセージを複数の正規化されたメッセージに投影する機能も提供します。 たとえば、デバイスから 1 つのメッセージで、体温、脈拍数、血圧、および呼吸速度に関する複数の生命兆候を送信できます。 この入力メッセージから、4 つの別個の FHIR リソースが作成されます。 各リソースは異なる生命兆候を表し、入力メッセージが 4 つの異なる正規化されたメッセージに投影されます。

## <a name="group"></a>グループ
グループは、前のステージから使用できる正規化されたメッセージが 3 つの異なるパラメーターを使用してグループ化される次のステージです。

* デバイス ID
* 測定の種類 
* 期間

デバイス ID と測定タイプのグループ化により、[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 測定タイプを使用できるようになります。 このタイプは、FHIR 内のデバイスから収集した時間ベースの一連の測定値を表す簡潔な方法を提供します。 また、期間は、IoT コネクタによって生成された Observation リソースが FHIR サービスに書き込まれる待機時間を制御します。

> [!NOTE]
> 期間の既定値は 15 分に設定されており、プレビュー用に構成することはできません。

## <a name="transform"></a>変換
変換ステージでは、グループ化された正規化されたメッセージは、FHIR 変換先マッピング テンプレートを介して処理されます。 テンプレートの種類に一致するメッセージは、マッピングで指定されているとおりに FHIR ベースの Observation リソースに変換されます。

この時点で、 [デバイス](https://www.hl7.org/fhir/device.html) リソースとそれに関連付けられている [Patient](https://www.hl7.org/fhir/patient.html) リソースも、メッセージに存在するデバイス識別子を使用して FHIR サービスから取得されます。 これらのリソースは、作成される Observation リソースへの参照として追加されます。

> [!NOTE]
> FHIR サービスの負荷を減らして解決すると、すべての ID 検索がキャッシュされます。 複数の患者に対してデバイスの再利用を予定している場合は、その患者に固有の仮想デバイス リソースを作成し、メッセージ ペイロードで仮想デバイス識別子を送信することをお勧めします。 仮想デバイスは、実際のデバイス リソースに親としてリンクさせることができます。

特定のデバイス識別子のデバイス リソースが FHIR サービスに存在しない場合、結果は作成時の set の値 `Resolution Type` によって異なります。 `Lookup` に設定すると、特定のメッセージは無視され、パイプラインは他の受信メッセージの処理を続行します。 に設定すると、IoT コネクタによって、FHIR サービスにベアボーン Device リソースと `Create` Patient リソースが作成されます。  

## <a name="persist"></a>保持
Observation FHIR リソースが変換ステージで生成されると、そのリソースは FHIR サービスに保存されます。 Observation FHIR リソースが新しい場合は、FHIR サービスに作成されます。 Observation FHIR リソースが既に存在する場合は、更新されます。

## <a name="next-steps"></a>次の手順

Device と FHIR の変換先マッピングを作成する方法について学習します。

> [!div class="nextstepaction"]
> [デバイス マッピング](how-to-use-device-mappings.md)

> [!div class="nextstepaction"]
> [FHIR 宛先マッピング](how-to-use-fhir-mappings.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
