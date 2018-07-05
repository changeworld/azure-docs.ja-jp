---
title: Windows ML で Custom Vision ONNX モデルを使用する - Cognitive Services | Microsoft Docs
description: Cognitive Services からエクスポートされた ONNX モデルを使用する Windows UWP アプリの作成方法について説明します。
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939431"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>チュートリアル: Custom Vision からエクスポートされた ONNX モデルを Windows ML (プレビュー) で使用する

Custom Vision Service からエクスポートされた ONNX モデルを Windows ML (プレビュー) で使用する方法について説明します。

このドキュメントでは、Custom Vision Service からエクスポートされた ONNX ファイルを Windows ML で 使用する方法について例を挙げて説明します。 サンプルとして Windows UWP アプリケーションが用意されています。 このサンプルには、犬と猫を認識できるトレーニング済みモデルが含まれています。 また、自分で用意したモデルをこのサンプルで使用するための手順も記載されています。

> [!div class="checklist"]
> * サンプル アプリについて
> * サンプル コードを入手する
> * 例を実行する
> * 自分で用意したモデルを使用する

## <a name="prerequisites"></a>前提条件

* 次の要件を満たした Windows 10 デバイス:

    * カメラが搭載されている。

    * Visual Studio 2017 バージョン 15.7 以降がインストールされ、__ユニバーサル Windows プラットフォーム開発__ワークロードが有効にされている。

    * 開発者モードが有効にされている。 詳細については、ドキュメント「[デバイスを開発用に有効にする](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)」を参照してください。

* (省略可能) Custom Vision Service からエクスポートされた ONNX ファイル。 詳細については、ドキュメント「[モバイル デバイスで使用するためにモデルをエクスポートする](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)」を参照してください。

    > [!NOTE]
    > 自分で用意したモデルを使用するには、セクション「[自分で用意したモデルを使用する](#use-your-own-model)」に記載された手順に従ってください。

## <a name="about-the-example-app"></a>サンプル アプリについて

このアプリケーションは汎用 Windows UWP アプリケーションです。 このアプリケーションでは、Windows 10 デバイスに搭載されたカメラを使用して、画像をモデルに提供します。 モデルからタグとスコアが返されると、ビデオ プレビューの下に表示されます。

* カメラからデータが取り込まれると、[MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) を使用して個々のフレームが抽出されます。 フレームはモデルに送信されて、スコアが付けられます。

* モデルからは、トレーニングで学習したタグと、画像にそのアイテムが含まれる信頼度を示す浮動小数値が返されます。

### <a name="the-ui"></a>UI

サンプル アプリケーションの UI は、__CaptureElement__ コントロールと __TextBlock__ コントロールを使用して作成されています。 CaptureElement にはカメラでキャプチャされたビデオのプレビューが表示され、TextBlock にはモデルから返された結果が表示されます。 

### <a name="the-model"></a>モデル

サンプルに付属しているモデル (`cat-or-dog.onnx`) は、Cognitive Services の Custom Vision Service を使用して作成され、トレーニングされたものです。 トレーニング済みモデルは、ONNX モデルとしてエクスポートされています。 このサービスの使用方法の詳細については、[分類子の構築方法](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)に関するドキュメントと、ドキュメント「[モバイル デバイスで使用するためにモデルをエクスポートする](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)」を参照してください。

> [!IMPORTANT]
> サンプルに付属しているモデルのトレーニングには、犬と猫の画像の小規模なセットが使用されました。 そのため、犬と猫の認識が世界で最も上手というわけではありません。

### <a name="the-model-class-file"></a>モデル クラス ファイル

ONNX ファイルを Windows UWP アプリケーションに追加すると、.cs ファイルが作成されます。 このファイルは、`.onnx` ファイル (このサンプルでは `cat-or-dog`) と名前は同じで、C# からモデルを操作するために使用されるクラスが含まれています。 ただし、生成されたクラス内のエンティティは、`_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput` のような名前になる場合があります。 これらのエントリは、(右クリックして、[名前の変更] を選択し) わかりやすい名前に変更してもかまいません。

> [!NOTE]
> 生成されたクラスとメソッドの名前は、サンプル コードによって次のようにリファクターされます。
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>カメラへのアクセス

`Package.appxmanifest` ファイルの __[機能]__ タブは、Web カメラとマイクへのアクセスを許可するように構成されています。

> [!NOTE]
> このサンプルでは音声は使用しませんが、筆者は自分のデバイスのカメラにアクセスするためにマイクを有効にする必要がありました。

このアプリケーションは、デバイスの背面にカメラがある場合は、そのカメラへのアクセスを試みます。 [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) クラスを使用して、カメラからのビデオのキャプチャを開始します。 また、[MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) を使用して、ビデオ フレームをキャプチャし、モデルに送信します。

## <a name="get-the-example-code"></a>サンプル コードを入手する

サンプル アプリケーションは [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP) で入手できます。

## <a name="run-the-example"></a>例を実行する

1. Visual Studio で `F5` キーを押すと、アプリケーションが起動します。 開発者モードを有効にするよう求めるメッセージが表示される場合があります。 詳細については、ドキュメント「[デバイスを開発用に有効にする](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)」を参照してください。

2. アプリケーションにデバイスのカメラとマイクへのアクセスを許可するよう求めるメッセージが表示されたら、許可します。

3. カメラを犬または猫に向けます。 画像に犬と猫のどちらが含まれているかを示すスコアが、アプリケーションのプレビューの下に表示されます。

    > [!TIP]
    > 犬や猫がそばにいない場合は、犬や猫の写真を使用できます。

## <a name="use-your-own-model"></a>自分で用意したモデルを使用する

自分で用意したモデルを使用するには、次の手順を実行します。

> [!IMPORTANT]
> このセクションの手順では、現在のモデル (cat-or-dog.cs) の名前を変更し、新しいモデルのクラスとメソッドの名前をリファクターします。 これは、サンプル モデルとの名前の競合を避けるためです。

1. Custom Vision Service を使用してモデルをトレーニングします。 モデルをトレーニングする方法については、[分類子の構築方法](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)に関するページを参照してください。

2. トレーニング済みモデルを ONNX モデルとしてエクスポートします。 モデルのエクスポート方法の詳細については、ドキュメント「[モバイル デバイスで使用するためにモデルをエクスポートする](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)」を参照してください。

3. ソリューション エクスプローラーで、__cat-or-dog.cs__ を右クリックし、名前を __cat-or-dog.txt__ に変更します。 名前を変更することで、新しいモデルとの名前の競合を防ぎます。

    > [!TIP]
    > 新しいモデルのクラス名に別の名前を使用することもできますが、既存の名前を再利用するほうが簡単です。

4. ソリューション エクスプローラーで、__VisionApp__ エントリを右クリックし、__[追加]__ > __[既存の項目...]__ の順に選択します。

5. モデルのクラスを生成するには、インポートする ONNX ファイルを選択し、__[追加]__ ボタンをクリックします。 ONNX ファイルと同じ名前 (拡張子は `.cs`) で新しいクラスがソリューション エクスプローラーに追加されます。

6. 生成された .cs ファイルを開き、次のアイテムの名前を確認します。

    > [!IMPORTANT]
    > クラスや機能を見分ける際に、サンプルの `cat-or-dog.txt` ファイルがガイドとして役立ちます。

    * モデルの入力を定義するクラス。 生成された名前は `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput` のようになっています。 このクラスの名前を __ModelInput__ に変更します。
    * モデルの出力を定義するクラス。 生成された名前は `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput` のようになっています。 このクラスの名前を __ModelOutput__ に変更します。
    * モデルを定義するクラス。 生成された名前は `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model` のようになっています。 このクラスの名前を __Model__ に変更します。
    * モデルを作成するメソッド。 生成された名前は `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model` のようになっています。 このメソッドの名前を __CreateModel__ に変更します。

7. ソリューション エクスプローラーで、`.onnx` ファイルを __Assets__ フォルダーに移動します。 

8. ONNX ファイルをアプリケーション パッケージに含めるには、`.onnx` ファイルを選択し、プロパティの __[ビルド アクション]__ を __[コンテンツ]__ に設定します。

9. __MainPage.xaml.cs__ ファイルを開きます。 次の行を見つけて、ファイル名を新しい `.onnx` ファイルの名前に変更します。

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    この変更によって、実行時に新しいモデルが読み込まれます。

10. アプリケーションをビルドし、実行します。 これで、画像のスコア付けに新しいモデルが使用されるようになりました。

## <a name="next-steps"></a>次の手順

Custom Vision モデルをエクスポートして使用する別の方法については、次のドキュメントを参照してください。

* [モデルをエクスポートする](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [エクスポートされた Tensorflow モデルを Android アプリケーションで使用する](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [エクスポートされた CoreML モデルを Swift iOS アプリケーションで使用する](https://go.microsoft.com/fwlink/?linkid=857726)
* [エクスポートされた CoreML モデルを Xamarin を使って iOS アプリケーションで使用する](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML で ONNX モデルを使用する方法については、ドキュメント「[Windows ML でモデルをアプリに統合する](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model)」を参照してください。
