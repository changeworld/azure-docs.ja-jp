---
title: Custom Speech のモデルとエンドポイントのライフサイクル - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Speech では、適応用として基本モデルが提供され、自分のデータからカスタム モデルを作成できます。 この記事では、モデルと、それらのモデルに使用されるエンドポイントのタイムラインについて説明します。
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385027"
---
# <a name="model-and-endpoint-lifecycle"></a>モデルとエンドポイントのライフサイクル

標準 (カスタマイズされていない) 音声は、基本モデルと呼ばれる AI モデルに基づいて構築されています。 ほとんどの場合、サポートされている音声言語ごとに異なる基本モデルをトレーニングしています。  音声サービスは、精度と品質を向上させるために、数か月ごとに新しい基本モデルを使用して更新されています。  
Custom Speech の場合、カスタム モデルは選択した基本モデルを特定の顧客シナリオからのデータに合わせて適応させることで作成されます。 カスタム モデルを作成すると、そのモデルが適応された対応する基本モデルが標準の音声サービスで更新された場合でも、そのモデルは更新または変更されることはありません。  
このポリシーにより、ユーザーはニーズに合ったカスタム モデルを作成できたら、その特定のカスタム モデルを長期間使用し続けることができます。  ただし、カスタム モデルを定期的に再作成して、最新の基本モデルを適応させ、精度と品質の向上を活用できるようにすることをお勧めします。

モデルのライフサイクルに関しては、他に次のような用語が重要です。

* **適応**: 基本モデルを選択し、テキスト データやオーディオ データを使用して、ドメインやシナリオに合わせてカスタマイズすること
* **デコード**: モデルを使用して音声認識を実行すること (音声をテキストにデコードする)
* **エンドポイント**: 特定のユーザー "*のみ*" がアクセスできる、基本モデルまたはカスタム モデルのユーザー固有のデプロイ。

### <a name="expiration-timeline"></a>有効期限のタイムライン

新しいモデルや新機能が利用可能になると、古くて正確さが低下したモデルは廃止されます。モデルとエンドポイントの有効期限については、次のタイムラインを参照してください。

**基本モデル** 

* 適応: 1 年間使用できます。 モデルがインポートされたら、カスタム モデルを作成するために 1 年間使用できます。 1 年後には、より新しいバージョンの基本モデルから新しいカスタム モデルを作成する必要があります。  
* デコード: インポートの後で 2 年間使用できます。 したがって、このモデルで 2 年間はエンドポイントを作成し、バッチ トランスクリプトを使用することができます。 
* エンドポイント:デコードと同じタイムラインで使用できます。

**カスタム モデル**

* デコード: モデルが作成されてから 2 年間使用できます。 したがって、カスタム モデルを作成してから 2 年間 (バッチ、リアルタイム、テスト) 使用することができます。 2 年後には、通常、基本モデルが適合に非推奨になっているため、"*モデルを再トレーニングする必要があります*"。  
* エンドポイント:デコードと同じタイムラインで使用できます。

基本モデルまたはカスタム モデルいずれかの有効期限が切れると、常に *最新バージョンの基本モデル* にフォールバックします。 このため、実装が中断されることはありませんが、カスタム モデルの有効期限が切れると、"*特定のデータ*" に対して精度が低下する可能性があります。 モデルの有効期限は、Speech Studio の Custom Speech エリアの次の場所で確認できます。

* モデルのトレーニングの概要
* モデルのトレーニングの詳細
* デプロイの概要
* デプロイの詳細

モデルのトレーニングの概要からの例を示します。

![モデルのトレーニングの概要](media/custom-speech/custom-speech-model-training-with-expiry.png) モデルのトレーニングの詳細ページからの例も示します。

![モデルのトレーニングの詳細](media/custom-speech/custom-speech-model-details-with-expiry.png)

また、[`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) または [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) Custom Speech API を使用し、JSON 応答の `deprecationDates` プロパティで、有効期限を確認することもできます。

GetModel API 呼び出しの有効期限データの例を次に示します。 **DEPRECATIONDATES** は、モデルの有効期限が切れたときに表示されます。 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Speech Studio のデプロイ セクションで、または Custom Speech API を使用して、エンドポイントで使用されているモデルを変更することにより、ダウンタイムなしで Custom Speech エンドポイントのモデルをアップグレードできます。

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>モデルの有効期限が切れたときの動作と更新方法
モデルの有効期限が切れた場合の動作とモデルの更新方法は、その使用方法によって異なります。
### <a name="batch-transcription"></a>バッチ文字起こし
[バッチ文字起こし](batch-transcription.md)で使用されるモデルの有効期限が切れた場合、要求は 4xx エラーで失敗します。 これを防ぐには、**文字起こしの作成** 要求の本文で送信される JSON の `model` パラメーターを更新して、より新しい基本モデルまたはより新しいカスタム モデルを指すようにします。 JSON から `model` エントリを削除すると、常に最新の基本モデルを使用することもできます。
### <a name="custom-speech-endpoint"></a>カスタム音声エンドポイント
[カスタム音声エンドポイント](how-to-custom-speech-train-model.md)で使用されるモデルの有効期限が切れると、サービスは使用している言語の最新の基本モデルを使用するように自動的にフォールバックします。 ページ上部にある **[Custom Speech]** メニューの **[配置]** を選択してエンドポイント名をクリックすると、その詳細を表示できます。 詳細ページの上部には、このエンドポイントで使用するモデルをダウンタイムなしでシームレスに更新できる **[モデルの更新]** ボタンが表示されます。 この変更は、[**Update Model**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) Rest API を使用してプログラムで行うこともできます。

## <a name="next-steps"></a>次のステップ

* [モデルのトレーニングとデプロイ](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>その他のリソース

* [データを準備してテストする](./how-to-custom-speech-test-and-train.md)
* [データを検査する](how-to-custom-speech-inspect-data.md)