---
title: IoT コネクタのデータフロー-Azure の医療 Api
description: IoT コネクタのデータフローを理解します。 IoT コネクタは、取り込み、正規化、グループ化、変換を行い、IoMT データを FHIR サービスに永続化します。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: 80de5c094175a4c2372befeaedddbe1ac56bba8a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308332"
---
# <a name="iot-connector-data-flow"></a>IoT コネクタのデータ フロー

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、IoT コネクタのデータフローの概要について説明します。 デバイスデータを高速医療相互運用性リソース (FHIR&#174;) ベースの [監視](https://www.hl7.org/fhir/observation.html) リソースに変換する IoT コネクタ内のさまざまなデータ処理ステージについて説明します。

次に、IoT コネクタによって受信されたデータのさまざまな段階を示します。

## <a name="ingest"></a>取り込み
取り込みは、デバイスデータを IoT コネクタに受信する最初の段階です。 デバイス データのインジェスト エンドポイントは、[Azure Event Hubs](../../event-hubs/index.yml) でホストされます。 Azure Event Hubs プラットフォームは、1 秒あたり数百万件のメッセージを受信して処理する機能により、高いスケールおよびスループットを実現します。 また、IoT コネクタはメッセージを非同期に使用できるため、デバイスデータの処理中にデバイスの待機が不要になります。

> [!NOTE]
> 現時点でデバイス データの形式としてサポートされているは JSON のみです。

## <a name="normalize"></a>Normalize (正規化)
ノーマライズは、デバイスデータが上記の Azure Event Hub から取得され、デバイスマッピングを使用して処理される次の段階です。 このマッピング プロセスにより、デバイス データが、正規化されたスキーマに変換されます。 

正規化プロセスでは、後の段階でのデータ処理が簡単になるだけでなく、1つの入力メッセージを複数の正規化されたメッセージに射影する機能も用意されています。 たとえば、デバイスから 1 つのメッセージで、体温、脈拍数、血圧、および呼吸速度に関する複数の生命兆候を送信できます。 この入力メッセージから、4 つの別個の FHIR リソースが作成されます。 各リソースは異なる生命兆候を表し、入力メッセージが 4 つの異なる正規化されたメッセージに投影されます。

## <a name="group"></a>グループ
グループは次の段階で、前の段階から使用できる正規化されたメッセージは、3つの異なるパラメーターを使用してグループ化されます。

* デバイス ID
* 測定の種類 
* 期間

デバイス ID と測定タイプのグループ化により、[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 測定タイプを使用できるようになります。 このタイプは、FHIR 内のデバイスから収集した時間ベースの一連の測定値を表す簡潔な方法を提供します。 および期間は、IoT コネクタによって生成される監視リソースが FHIR サービスに書き込まれる待ち時間を制御します。

> [!NOTE]
> 期間の既定値は 15 分に設定されており、プレビュー用に構成することはできません。

## <a name="transform"></a>変換
変換段階では、グループ化された正規化メッセージは、FHIR の送信先マッピングテンプレートを通じて処理されます。 テンプレートの種類に一致するメッセージは、マッピングで指定されているとおりに FHIR ベースの Observation リソースに変換されます。

この時点で、 [デバイス](https://www.hl7.org/fhir/device.html) リソースとそれに関連付けられている [患者](https://www.hl7.org/fhir/patient.html) リソースも、メッセージに示されているデバイス識別子を使用して fhir サービスから取得されます。 これらのリソースは、作成される Observation リソースへの参照として追加されます。

> [!NOTE]
> FHIR サービスの負荷を軽減するために、すべての id の参照がキャッシュされています。 複数の患者に対してデバイスの再利用を予定している場合は、その患者に固有の仮想デバイス リソースを作成し、メッセージ ペイロードで仮想デバイス識別子を送信することをお勧めします。 仮想デバイスは、実際のデバイス リソースに親としてリンクさせることができます。

特定のデバイス id のデバイスリソースが FHIR サービスに存在しない場合、結果は作成時に設定されたの値によって決まり `Resolution Type` ます。 `Lookup` に設定すると、特定のメッセージは無視され、パイプラインは他の受信メッセージの処理を続行します。 に設定されている場合 `Create` 、IoT コネクタは、ベアボーンデバイスと患者リソースを FHIR サービスに作成します。  

## <a name="persist"></a>保持
変換ステージで監視 FHIR リソースが生成されると、リソースは FHIR サービスに保存されます。 FHIR リソースが新しい場合は、FHIR サービスに作成されます。 FHIR リソースが既に存在している場合は、更新されます。

## <a name="next-steps"></a>次のステップ

デバイスおよび FHIR の変換先マッピングを作成する方法について説明します。

> [!div class="nextstepaction"]
> [デバイス マッピング](how-to-use-device-mapping-iot.md)

> [!div class="nextstepaction"]
> [FHIR 宛先マッピング](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
