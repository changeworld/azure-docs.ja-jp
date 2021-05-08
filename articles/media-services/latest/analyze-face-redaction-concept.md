---
title: Azure Media Services v3 API で顔を検索して編集する | Microsoft Docs
description: Azure Media Services v3 で提供されている顔検出および編集 (ぼかし) プリセットを使用すると、ビデオ ファイルの送信、顔の検出、必要に応じた編集 (ぼかし) の適用を、1 つの結合されたパスで、または編集できるように 2 ステージの操作で、行うことができます。 この記事では、v3 API の Face Detector プリセットで、顔を検索して編集する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 4907a81fc8cb55499fa97f2b02a3e19e7117bbbc
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286387"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Face Detector プリセットで顔を検索して編集する (ぼかす)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3 API に含まれる Face Detector プリセットにより、クラウドでのスケーラブルな顔検出と編集 (ぼかし) が提供されます。 顔編集では、ビデオを編集して選択した個人の顔をぼかすことができます。 顔編集サービスは、公共の安全やニュース媒体などに使用していただけます。 複数人の顔を含んでいる映像の場合、顔編集を手作業で行うと数分の映像でも数時間かかりますが、このプリセットを使えば数ステップの簡単な手順で完了します。

この記事では、**Face Detector プリセット** の詳細を説明し、Azure Media Services SDK for .NET でのその使用方法を示します。

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

## <a name="compliance-privacy-and-security"></a>コンプライアンス、プライバシー、セキュリティ
 
重要な注意事項として、Azure Media Services での分析を使用するときは、適用されるすべての法令に従う必要があります。 他の人の権利を侵害するような方法で Azure Media Services またはその他の Azure サービスを使用することはできません。 生体認証データなどのビデオを Azure Media Services サービスにアップロードして処理と保管を行う前に、ビデオに写っている個人から適切なすべての同意を得ることを含め、適切な権限をすべて持っている必要があります。 Azure Media Services のコンプライアンス、プライバシー、セキュリティについては、Azure の [Cognitive Services の条項](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)に関するページを参照してください。 Microsoft のプライバシー義務とデータの取り扱いについては、Microsoft の[プライバシー ステートメント](https://privacy.microsoft.com/PrivacyStatement)、[オンライン サービス条件](https://www.microsoft.com/licensing/product-licensing/products) (OST)、および[データ処理の補遺](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA") に関するページをご確認ください。 データの保有、削除、破棄などのその他のプライバシー情報は、OST および[こちら](../video-indexer/faq.md)で確認できます。 Azure Media Services を使用すると、Cognitive Services の条項、OST、DPA、およびプライバシー ステートメントに従うことに同意したものと見なされます

## <a name="face-redaction-modes"></a>顔編集モード

顔編集は、ビデオのフレームごとに顔を検出し、その顔オブジェクトを時間軸の前後にわたって追跡することで、同一の人間を他の角度からも処理します。 自動化された編集プロセスは複雑であり、100% 確実にすべての顔が常にぼかされるとは限りません。 このため、プリセットを 2 パス モードで使用して、最終的なぼかしパスにファイルを送る前に、編集ステージを経由することでぼかしの品質と精度を向上させることができます。 

完全自動の **結合** モードに加えて、2 パスのワークフローが用意されており、顔 ID の一覧を使用してぼかしを行う (または、行わない) 顔を選択できます。 フレームごとに任意の調整を行うため、プリセットでは 2 番目のパスへの入力として JSON 形式のメタデータ ファイルが使用されます。 このワークフローは、**分析** モードと **編集** モードに分かれています。

また、単一のパスに 2 つのモードを簡単に結合するだけで、両方のタスクを 1 つのジョブで実行することもできます。このモードは **結合** と呼ばれます。  この記事のサンプル コードでは、サンプルのソース ファイルで簡略化された単一パスの **結合** モードを使用する方法を示します。

### <a name="combined-mode"></a>結合モード

これにより、JSON ファイルを手動で編集する必要なしに、1 回のパスで編集済みの MP4 ビデオ ファイルが生成されます。 ジョブの資産フォルダーに出力される単一の .mp4 ファイルには、選択したぼかし効果を使用してぼかされた顔が含まれます。 編集で最適な結果を実現するため、**SourceResolution** に設定された resolution プロパティを使用します。

| 段階 | ファイル名 | Notes |
| --- | --- | --- |
| 入力資産 |"ignite-sample.mp4" |WMV、MPV、MP4 形式のビデオ |
| プリセットの構成 |Face Detector の構成 | **mode**: FaceRedactorMode.Combined、**blurType**: BlurType.Med、**resolution**: AnalysisResolution.SourceResolution |
| 出力資産 |"ignite-redacted.mp4" |顔にぼかし効果が適用されたビデオ |

### <a name="analyze-mode"></a>分析モード

2 パス ワークフローの **分析** パスでは、ビデオ入力が取得され、顔の位置のリストを含む JSON ファイル、顔 ID、および検出された各顔の jpg 画像が生成されます。 

| 段階 | ファイル名 | Notes |
| --- | --- | --- |
| 入力資産 |"ignite-sample.mp4" |WMV、MPV、MP4 形式のビデオ |
| プリセットの構成 |Face Detector の構成 |**mode**: FaceRedactorMode.Analyze、**resolution**: AnalysisResolution.SourceResolution|
| 出力資産 |ignite-sample_annotations.json |JSON 形式での、顔の位置の注釈データです。 ユーザー編集によりぼかし枠を変更することができます。 以下のサンプルを参照してください。 |
| 出力資産 |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |検出された顔それぞれをトリミングした jpg (数字は顔の labelId を示す) |

#### <a name="output-example"></a>出力例

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>編集 (ぼかし) モード

ワークフローの 2 番目のパスでは、単一の資産に結合する必要のある大量の入力を受け取ります。

これには、ぼかす対象となる Id の一覧、元のビデオ、JSON の注釈が含まれます。 このモードでは、注釈を使用して入力ビデオにぼかし効果を適用します。

Analyze パスからの出力は、元のビデオを含みません。 ビデオは、編集モードのタスクの入力資産にアップロードし、プライマリ ファイルとして選択する必要があります。

| 段階 | ファイル名 | Notes |
| --- | --- | --- |
| 入力資産 |"ignite-sample.mp4" |WMV、MPV、MP4 形式のビデオ 手順 1 と同じビデオです。 |
| 入力資産 |"ignite-sample_annotations.json" |フェーズ 1 からの注釈メタデータ ファイル。ぼかされた顔を変更する必要がある場合、必要に応じて修正が含まれます。 これは、外部のアプリケーション、コード、またはテキスト エディターで編集する必要があります。 |
| 入力資産 | "ignite-sample_IDList.txt" (オプション) | 行で区切られた、編集する顔 ID の新しい一覧です (オプション)。 空白のままにすると、ソース内のすべての顔にぼかしが適用されます。 一覧を使用して、ぼかさない特定の顔を選択できます。 |
| Face Detector プリセット  |プリセットの構成  | **mode**: FaceRedactorMode.Redact、**blurType**: BlurType.Med |
| 出力資産 |"ignite-sample-redacted.mp4" |注釈に基づいてぼかし効果を適用したビデオ |

#### <a name="example-output"></a>出力例

これは 1 つの ID を選択した場合の IDList からの出力です。

foo_IDList.txt の例

```
1
2
3
```

## <a name="blur-types"></a>ぼかしの種類

**Combined** モードまたは **Redact** モードには、**Low**、**Med**、**High**、**Box**、**Black** の 5 種類のぼかしモードがあり、JSON 入力構成で選択することができます。 既定では **Med** が使用されます。

ぼかしの種類のサンプルを以下に示します。


#### <a name="low"></a>低

![低解像度のぼかし設定の例。](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Med

![中解像度のぼかし設定の例。](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>高

![高解像度のぼかし設定の例。](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>ボックス

![出力のデバッグに使用するボックス モード。](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Black

![ブラック ボックス モードを使用すると、すべての顔が黒いボックスで覆われます。](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>出力 JSON ファイルの要素

Redaction MP は、高精度の顔位置検出と追跡を行い、ビデオ フレーム内で最大 64 個の人の顔を検出できます。 顔が正面を向いているときに最善の結果が得られ、横顔や小さい顔 (24 x 24 ピクセル以下) のときは精度が低下することがあります。

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET サンプル コード

次のプログラムは、**結合** 単一パス編集モードの使用方法を示したものです。

- 資産を作成し、その資産にメディア ファイルをアップロードします。
- mode と blurType の設定を使用する Face Detector プリセットを構成します。
- Face Detector プリセットを使用して新しい Transform を作成します
- 出力の編集済みビデオ ファイルをダウンロードします。

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使って、.NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

サンプルは、[FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) フォルダーにあります。 ダウンロードしたプロジェクトに含まれる [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) を開きます。 [API へのアクセス](./access-api-howto.md)に関するページで取得した資格情報の値に置き換えます。

**必要に応じて**、リポジトリのルートにある **[sample.env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** ファイルをコピーし、そこに詳細を入力して、そのファイルの名前を **.env** (先頭のドットに注意してください) に変更することにより、リポジトリ内のすべてのサンプル プロジェクトで使用できるようにします。  これにより、サンプルごとに設定される appsettings.json ファイルが必要がなくなります。また、GitHub の独自のクローンされたリポジトリに設定がチェックインされないようにすることもできます。

#### <a name="examples"></a>例

このコードでは、**結合** モードのぼかしの **FaceDetectorPreset** を設定する方法が示されています。

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

このコード サンプルは、作成の間にプリセットが Transform オブジェクトにどのように渡されるかを示したものです。 Transform を作成した後、ジョブをそれに直接送信できます。

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

