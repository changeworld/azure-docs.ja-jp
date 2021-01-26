---
title: Swagger ドキュメント - 音声サービス
titleSuffix: Azure Cognitive Services
description: Swagger ドキュメントを使用して、さまざまなプログラミング言語の SDK を自動生成できます。 Microsoft のサービスの操作はすべて Swagger によってサポートされています
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 6bb50e427fa85a170c5ad23a63d67c01e898a17d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665686"
---
# <a name="swagger-documentation"></a>Swagger ドキュメント

音声サービスでは、データのインポート、モデルの作成、モデルの精度のテスト、カスタム エンドポイントの作成、文字起こしのキューへのバッチ配置、およびサブスクリプションの管理に使用されるいくつかの REST API を操作するための Swagger 仕様を提供しています。 Custom Speech ポータルを介して利用できる操作のほとんどは、これらの API を使用してプログラムによって実行できます。

> [!NOTE]
> 音声変換とテキスト読み上げの両方の操作がサポートされており、REST API として利用可能で、Swagger 仕様に記載されています。

## <a name="generating-code-from-the-swagger-specification"></a>Swagger 仕様からコードを生成する

[Swagger 仕様](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)には、さまざまなパスをすばやくテストできるオプションが用意されています。 ただし、すべてのパスのコードを生成し、今後のソリューションの基盤にできる呼び出しの 1 つのライブラリを作成することが望ましい場合があります。 Python ライブラリを生成するプロセスを見てみましょう。

ご利用の音声サービス サブスクリプションと同じリージョンに Swagger を設定する必要があります。 Azure portal 内のご利用の音声サービス リソースの下でご利用のリージョンを確認できます。 サポートされているリージョンの完全な一覧については、「[リージョン](regions.md)」をご覧ください。

1. ブラウザーで、ご利用のリージョンの Swagger 仕様に進みます。  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. そのページで **[API]** 定義をクリックし、 **[Swagger]** をクリックします。 表示されたページの URL をコピーします。
1. 新しいブラウザーで https://editor.swagger.io に移動します。
1. **[ファイル]** をクリックし、 **[URL のインポート]** をクリックし、URL を貼り付け、 **[OK]** をクリックします。
1. **[クライアントの生成]** をクリックし、 **[Python]** を選択します。 クライアント ライブラリがお使いのコンピューターに `.zip` ファイル形式でダウンロードされます。
1. ダウンロード ファイルを解凍し、中身をすべて抽出します。 解凍には `tar -xf` を使用することもあります。
1. 解凍したモジュールを Python 環境にインストールします。  
       `pip install path/to/package/python-client`
1. インストールされるパッケージの名前は `swagger_client` です。 正常にインストールされたことを確認します。  
       `python -c "import swagger_client"`

生成した Python ライブラリは、[GitHub 上の音声サービス サンプル](https://aka.ms/csspeech/samples)で使用できます。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [REST (Swagger):一括文字起こしとカスタマイズ](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)

## <a name="next-steps"></a>次のステップ

* [GitHub 上の音声サービス サンプル](https://aka.ms/csspeech/samples)。
* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
