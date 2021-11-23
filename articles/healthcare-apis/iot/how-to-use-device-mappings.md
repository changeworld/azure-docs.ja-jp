---
title: IoT Connector でのデバイスマッピング-Azure の医療 api
description: この記事では、Azure の医療 Api IoT Connector でデバイスマッピングテンプレートを構成して使用する方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 4b86742fb3bb5cc5c642b4d97b96c6aebc7c887c
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936582"
---
# <a name="how-to-use-device-mappings"></a>デバイスマッピングの使用方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタデバイスと FHIR の宛先マッピングの編集、テスト、トラブルシューティングについては、 [IoMT コネクタデータマッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) ツールをご覧ください。 Azure portal で IoT コネクタにアップロードするか、 [オープンソースバージョン](https://github.com/microsoft/iomt-fhir) の iot コネクタで使用するためのマッピングをエクスポートします。

この記事では、デバイスマッピングを使用して IoT コネクタを構成する方法について説明します。

IoT コネクタには、2種類の JSON ベースのマッピングが必要です。 1 種類目の **デバイス マッピング** は、`devicedata` Azure Event Hub エンドポイントに送信されるデバイス ペイロードのマッピングを担います。 型、デバイス識別子、測定日時、測定値を抽出します。 

2番目のタイプである **Fast 医療相互運用性リソース (fhir&#174;) の宛先マッピング** は、fhir リソースのマッピングを制御します。 これにより、観察期間の長さ、値の格納に使用される FHIR データ型、用語コードを構成できます。 

2種類のマッピングは、その型に基づいて JSON ドキュメントに構成されます。 これらの JSON ドキュメントは、Azure portal を通じて IoT コネクタに追加されます。 デバイスマッピングドキュメントは、[ **デバイスマッピング** ] ページと、 **宛先** ページを介して fhir 送信先マッピングドキュメントによって追加されます。

> [!NOTE]
> マッピングは、基になる blob ストレージに格納され、コンピューティング実行ごとに blob から読み込まれます。 更新された場合は、すぐに有効になります。 

## <a name="device-mappings-overview"></a>デバイスマッピングの概要

デバイスマッピングは、デバイスメッセージの内容を、詳細な評価のために共通の形式に抽出する機能を提供します。 受信した各デバイスメッセージは、すべてのデバイスマッピングテンプレートに対して評価されます。 

1つの受信デバイスメッセージを、後で FHIR サービスの異なる観測にマップされる複数の送信メッセージに分割することができます。 

結果は、テンプレートによって解析された 1 つまたは複数の値を表す、正規化されたデータ オブジェクトです。 

正規化されたデータ モデルには、検出して抽出する必要がある、いくつかの必須プロパティがあります。

|プロパティ|説明|
|--------|-----------|
|**Type**|測定を分類するための名前または種類。 この値は、必要な FHIR 変換先マッピングにバインドするために使用されます。 複数のマッピングを同じ型に出力して、複数のデバイス間で異なる表現を1つの共通出力にマップすることができます。|
|**OccurenceTimeUtc**|測定が発生した時刻。|
|**DeviceId**|デバイスの識別子。 この値は、送信先の FHIR サービスに存在するデバイスリソースの識別子と一致している必要があります。|
|**Properties**|作成された Observation リソースに値を保存できるように、少なくとも 1 つのプロパティを抽出します。 プロパティは、正規化中に抽出されたキーと値のペアのコレクションです。|

> [!IMPORTANT]
> 完全に正規化されたモデルは、 [Imeasurement](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) インターフェイスによって定義されます。

次に示すのは、IoT コネクタ内で正規化と変換プロセスを実行する際の動作の概念例です。

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT データの正規化フロー example1" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="IoT データの正規化フロー example2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

コンテンツ ペイロード自体は、次の 3 つの部分で構成される Azure Event Hub メッセージです: Body、Properties、SystemProperties。 `Body` は、UTF-8 でエンコードされた文字列を表すバイト配列です。 テンプレートの評価中に、バイト配列は自動的に文字列値に変換されます。 `Properties` は、メッセージの作成者が使用するキー値のコレクションです。 `SystemProperties` も、Azure Event Hub フレームワークによって予約され、エントリが自動的に入力されるキー値コレクションです。

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
## <a name="mapping-with-jsonpath"></a>JSONPath によるマッピング

現在サポートされている5つのデバイスコンテンツマッピングの種類は、必要なマッピングと抽出された値の両方に対して JSONPath に依存しています。 JSONPath の詳細については、 [こちら](https://goessner.net/articles/JsonPath/)を参照してください。 5種類のテンプレートはすべて、JSONPath 式を解決するために [JSON .net 実装](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) を使用します。

デバイスマッピングテンプレート内で1つまたは複数のテンプレートを定義できます。 受信した各イベントハブデバイスメッセージは、すべてのデバイスマッピングテンプレートに対して評価されます。 

1つの受信デバイスメッセージを、後で FHIR サービスの異なる観測にマップされる複数の送信メッセージに分割することができます。 

さまざまな種類のテンプレートが存在し、デバイスマッピングファイルを作成するときに使用できます。

|名前                                                                     | [説明]                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](./how-to-use-jsonpath-content-mappings.md)     |JsonPath を使用した式の作成をサポートするテンプレート                  
|[CollectionContentTemplate](./how-to-use-collection-content-mappings.md) |正規化中に使用されるテンプレートの一覧を表すために使用されるテンプレート。                                                            |                                                           
|[CalculatedContentTemplate](./how-to-use-calculated-functions-mappings.md)|複数の式言語のいずれかを使用した式の作成をサポートするテンプレート。 では、Jのパス関数を使用したデータ変換がサポートされています。|
|[IotJsonPathContentTemplate](./how-to-use-iot-jsonpath-content-mappings.md)|Azure iot Hub から送信されたメッセージ、または Azure Iot Central の従来のデータエクスポート機能をサポートするテンプレート。|
|[IotCentralJsonPathContentTemplate](./how-to-use-iot-central-json-content-mappings.md)|Azure Iot Central のデータのエクスポート機能を使用して送信されたメッセージをサポートするテンプレート。| 

> [!TIP]
> 一般的なエラーと問題の解決については、「IoT コネクタの [トラブルシューティングガイド](./iot-troubleshoot-guide.md) 」を参照してください。 

## <a name="next-steps"></a>次のステップ

この記事では、デバイスマッピングの使用方法について説明しました。 FHIR 変換先マッピングの使用方法については、「」を参照してください。

>[!div class="nextstepaction"]
>[FHIR 変換先マッピングの使用方法](how-to-use-fhir-mappings.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
