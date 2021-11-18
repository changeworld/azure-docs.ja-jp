---
title: IoT コネクタとは - Azure Healthcare API
description: この記事では、IoMT データを FHIR サービスに格納する前に IoT コネクタが実行する手順について説明します。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: b74b3c1aefe2c7dd05421385e672385196aab15e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719431"
---
# <a name="what-is-iot-connector"></a>IoT コネクタとは

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

IoT コネクタは、IoMT (Internet of Medical Things) デバイスからデータを取り込む機能を提供する Azure Healthcare API のオプション機能です。

この記事では、IoT コネクタのデータ フローの概要について説明します。 デバイス データを 高速ヘルスケア相互運用性リソース (FHIR&#174;) ベースの Observation リソースに変換する IoT コネクタ サービス内のさまざまなデータ処理ステージについて [学習](https://www.hl7.org/fhir/observation.html) します。

データが IoT コネクタによって受信された後に通過するさまざまなステージを次に示します。

## <a name="ingest"></a>取り込み

取り込みは、デバイス データを IoT コネクタ サービスに受信する最初のステージです。 デバイス データのインジェスト エンドポイントは、Azure Event Hubs でホストされます。 [Azure Event Hub プラットフォームでは](../../event-hubs/index.yml) 、1 秒あたり数百万件のメッセージを受信して処理する機能を備えた、高いスケールとスループットがサポートされています。 また、IoT コネクタ サービスがメッセージを非同期的に使用して、デバイス データが処理される間にデバイスが待機する必要がなされます。

> [!NOTE]
> 現時点でデバイス データの形式としてサポートされているは JSON のみです。

## <a name="normalize"></a>Normalize (正規化)

Normalize は、デバイス データが上記の Azure Event Hub から取得され、デバイス マッピング テンプレートを使用して処理される次のステージです。 このマッピング プロセスにより、デバイス データが、正規化されたスキーマに変換されます。 正規化プロセスでは、後の段階でデータ処理が簡略化されるだけでなく、1 つの入力メッセージを複数の正規化されたメッセージにプロジェクションすることもできます。 たとえば、デバイスから 1 つのメッセージで、体温、脈拍数、血圧、および呼吸速度に関する複数の生命兆候を送信できます。 この入力メッセージから、4 つの別個の FHIR リソースが作成されます。 各リソースは異なる生命兆候を表し、入力メッセージが 4 つの異なる正規化されたメッセージに投影されます。

## <a name="group"></a>グループ

グループは、前のステージから使用できる正規化されたメッセージが 3 つの異なるパラメーターを使用してグループ化される次のステージです。 

* デバイス ID
* 測定の種類 
* 期間

デバイス ID と測定タイプのグループ化により、[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 測定タイプを使用できるようになります。 この種類は、FHIR のデバイスからの時間ベースの一連の測定値を表す簡潔な方法を提供します。一方、期間は、IoT コネクタによって生成された Observation リソースが FHIR サービスに書き込まれる待機時間を制御します。

> [!NOTE]
> 期間の既定値は 15 分に設定されており、プレビュー用に構成することはできません。

## <a name="transform"></a>変換

変換ステージでは、グループ化された正規化されたメッセージは、FHIR 変換先マッピング テンプレートを介して処理されます。 テンプレートの種類に一致するメッセージは、マッピングで指定されているとおりに FHIR ベースの Observation リソースに変換されます。

この時点で、デバイス リソースとそれに関連付けられている Patient リソースも、メッセージに存在するデバイス識別子を使用して FHIR サービスから取得されます。 これらのリソースは、作成される Observation リソースへの参照として追加されます。

> [!NOTE]
>FHIR サービスの負荷を減らして解決すると、すべての ID 検索がキャッシュされます。 複数の患者でデバイスを再利用する予定の場合は、患者に固有の仮想デバイス リソースを作成し、メッセージ ペイロードに仮想デバイス識別子を送信する必要があります。 仮想デバイスは、実際のデバイス リソースに親としてリンクさせることができます。

特定のデバイス識別子のデバイス リソースが FHIR サービスに存在しない場合、結果は作成時に設定された解決の種類の値によって異なります。 [検索] に設定すると、特定のメッセージは無視され、パイプラインは引き続き他の受信メッセージを処理します。 [作成] に設定すると、IoT コネクタ サービスによって、FHIR サービス上にベアボーン Device リソースと Patient リソースが作成されます。

## <a name="persist"></a>保持

Observation FHIR リソースが変換ステージで生成されると、そのリソースは FHIR サービスに保存されます。 FHIR リソースが新しい場合は、サービスに作成されます。 FHIR リソースが既に存在する場合は、更新されます。

## <a name="next-steps"></a>次の手順

IoT コネクタのマッピングの詳細については、次のガイドを参照してください。

>[!div class="nextstepaction"]
>[デバイス マッピングを使用する方法](how-to-use-device-mappings.md)

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングを使用する方法](how-to-use-fhir-mappings.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
