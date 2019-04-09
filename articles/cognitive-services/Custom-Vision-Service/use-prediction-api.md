---
title: 例:予測エンドポイントを使用してプログラムで分類器を指定してテストする - Custom Vision
titlesuffix: Azure Cognitive Services
description: API を使用して、Custom Vision Service の分類器によるイメージのテストをプログラムから行う方法を説明します。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472729"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Prediction API でモデルを使用する

モデルをトレーニングした後、イメージを Prediction API に送信することで、プログラムからイメージをテストできます。

> [!NOTE]
> このドキュメントでは、C# を使用して Prediction API にイメージを送信する方法を示します。 この API の詳細と使用例については、[Prediction API のリファレンス](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)を参照してください。

## <a name="publish-your-trained-iteration"></a>トレーニング済みイテレーションを発行する

[Custom Vision の Web ページ](https://customvision.ai)から自分のプロジェクトを選択し、__[Performance]__ \(パフォーマンス) タブを選択します。

Prediction API にイメージを送信するには、まず予測のイテレーションを発行する必要があり、これは __[Publish]__ \(発行\) を選択して、発行されたイテレーションの名前を指定して実行できます。 これにより、モデルが、Custom Vision Azure リソースの Prediction API にアクセスできるようになります。 

![[Publish]\(発行\) ボタンが赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/unpublished-iteration.png)

モデルが正常に発行されると、左側のサイドバーのイテレーションの横に、[Published]\(発行済み\) ラベルが表示され、さらにイテレーションの説明に発行済みイテレーションの名前が表示されます。

![発行済みラベルと発行済みイテレーションの名前が赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>URL と予測キーを取得する

モデルが発行されたら、__Prediction URL__ を選択して、Prediction API の使用に関する情報を取得できます。 これにより、__Prediction URL__ と __Prediction-Key__ などの Prediction API を使用するための情報を含む下に示すようなダイアログが開きます。

![[Prediction URL] ボタンが赤色の四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/published-iteration-prediction-url.png)

![イメージ ファイルと Prediction-Key 値を使用するための Prediction URL が赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> __Prediction-Key__ は、[Azure portal](https://portal.azure.com) ページのプロジェクトに関連付けられている Custom Vision Azure リソースの __[キー]__ でも見つけることができます。 

ダイアログから、アプリケーションで使用する次の情報をコピーします。

* __イメージ ファイル__を使用するための __Prediction URL__。
* __Prediction-Key__ 値。

## <a name="create-the-application"></a>アプリケーションを作成する

1. Visual Studio から、新しい C# コンソール アプリケーションを作成します。

1. __Program.cs__ ファイルの本文として次のコードを使用します。

    > [!IMPORTANT]
    > 次の情報を変更します。
    >
    > * __namespace__ はプロジェクトの名前に設定します。
    > * 前に取得した __Prediction-Key__ 値を、`client.DefaultRequestHeaders.Add("Prediction-Key",` で始まる行の中に設定します。
    > * 前に取得した __Prediction URL__ 値を、`string url =` で始まる行の中に設定します。

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

## <a name="use-the-application"></a>アプリケーションを使用する

アプリケーションを実行するときには、コンソールにイメージ ファイルへのパスを入力します。 そのイメージが Prediction API に送信され、予測結果が JSON ドキュメントとして返されます。 次の JSON ドキュメントは応答の例です。

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>次の手順

[モバイル利用のためにモデルをエクスポートする](export-your-model.md)

[.NET SDK の概要](csharp-tutorial.md)

[Python SDK の概要](python-tutorial.md)

[Java SDK の概要](java-tutorial.md)

[Node SDK の概要](node-tutorial.md)

[Go SDK の概要](go-tutorial.md)
