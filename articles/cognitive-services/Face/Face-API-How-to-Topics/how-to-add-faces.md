---
title: Face API で顔を追加する | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services の Face API を使用して、画像に顔を追加します。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372992"
---
# <a name="how-to-add-faces"></a>顔を追加する方法

このガイドでは、PersonGroup に膨大な数の人と顔を追加するベスト プラクティスを説明します。
同じ戦略が FaceList と LargePersonGroup にも適用されます。
サンプルは Face API クライアント ライブラリを使用して C# で記述されています。

## <a name="step1"></a> 手順 1: 初期化

いくつかの変数を宣言し、ヘルパー関数を実装して要求をスケジュール設定します。

- `PersonCount` は、人の合計です。
- `CallLimitPerSecond` は、サブスクリプション レベルに基づいた 1 秒あたりの最大呼び出し回数です。
- `_timeStampQueue` は、要求のタイムスタンプを記録するキューです。
- `await WaitCallLimitPerSecondAsync()` は、次の要求の送信が有効になるまで待機します。

```CSharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step2"></a> 手順 2: API 呼び出しを承認する

クライアント ライブラリを使用するとき、サブスクリプション キーが FaceServiceClient クラスのコンストラクターを介して渡されます。 例: 

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

サブスクリプション キーは、Azure Poral の [Marketplace] ページから入手できます。 [サブスクリプション](https://www.microsoft.com/cognitive-services/en-us/sign-up)に関するページを参照してください。

## <a name="step3"></a> 手順 3: PersonGroup を作成する

人を保存するために "MyPersonGroup" という名前の PersonGroup を作成します。
要求時間が `_timeStampQueue` にエンキューされ、全体的な検証が保証されます。

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a> 手順 4: PersonGroup に対して人を作成する

複数の人を同時に作成します。また、`await WaitCallLimitPerSecondAsync()` を適用して、呼び出し制限を超えないようにします。

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a> 手順 5: 顔を人に追加する

さまざまな人への顔の追加は同時に処理されますが、特定の 1 人については順次処理されます。
ここでも、`await WaitCallLimitPerSecondAsync()` を呼び出して、要求の頻度が制限範囲内になるようにします。

```CSharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a> まとめ

このガイドでは、膨大な数の人と顔を含む PersonGroup を作成するプロセスについて学びました。 次のことを覚えておいてください。

- この方法は FaceList と LargePersonGroup にも適用されます。
- LargePersonGroup 内のさまざまな FaceList または Person への顔の追加や削除は同時に処理できます。
- LargePersonGroup 内の特定の 1 つの FaceList または Person に対する同じ操作は、順次実行する必要があります。
- わかりやすくするため、潜在的な例外の処理はこのガイドでは説明していません。 堅牢性をさらに拡張するには、適切な再試行ポリシーを適用する必要があります。

これまでに説明してデモを示した機能の参照先は以下のとおりです。

- PersonGroup の作成に [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API を使用
- Person の作成に [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API を使用
- 人への顔の追加に [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API を使用

## <a name="related"></a> 関連トピック
- [画像内の顔を識別する方法](HowtoIdentifyFacesinImage.md)
- [画像内の顔を検出する方法](HowtoDetectFacesinImage.md)
- [大規模なフィーチャーを使用する方法](how-to-use-large-scale.md)
