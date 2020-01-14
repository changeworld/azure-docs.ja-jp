---
title: クイック スタート:BLOB ストレージに格納された音声を認識する、Java - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: e110c87835cdf517bdd54adda0ef6d9168a44d52
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469677"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [ソース ファイルを Azure BLOB にアップロードする](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-eclipse"></a>Eclipse でプロジェクトを開く

最初の手順として、ご利用のプロジェクトが Eclipse で開いていることを確認します。

1. Eclipse を起動します
2. プロジェクトを読み込んで `Main.java` を開きます。

## <a name="add-a-reference-to-gson"></a>参照を Gson に追加する
このクイックスタートでは、外部の JSON シリアライザー/デシリアライザーを使用します。 Java の場合、[Gson](https://github.com/google/gson) を選択しました。

pom.xml を開き、次の参照 [!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)] を追加します。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]
(`YourSubscriptionKey`、`YourServiceRegion`、および `YourFileUrl` の値を独自の値に置き換える必要があります。)

## <a name="json-wrappers"></a>JSON ラッパー

REST API は JSON 形式で要求を受け取り、結果も JSON で返すため、それらとは文字列のみを使用してやりとりすることが可能ですが、これについてはお勧めしていません。
要求と応答をより管理しやすくするために、JSON のシリアル化/逆シリアル化に使用するクラスをいくつか宣言します。

先に進み、`Main` の前に宣言を置きます。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Http クライアントを作成して構成する
最初に必要なのは、適切なベース URL と認証セットを含む Http クライアントです。
このコードを `Main` に挿入します[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>文字起こし要求を生成する
次に、文字起こし要求を生成します。 このコードを `Main` に追加します[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>要求を送信し、その状態を確認する
ここで、Speech サービスに要求を投稿し、初期の応答コードを確認します。 この応答コードは、サービスが要求を受信したかどうかを示すだけに過ぎません。 サービスからは応答ヘッダーで URL が返され、文字起こしの状態はこの URL に格納されます。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>文字起こしが完了するのを待つ
サービスでは文字起こしが非同期的に処理されるため、その状態を頻繁にポーリングする必要があります。 チェックは 5 秒ごとに実行します。

状態を確認するには、要求が投稿されたときに取得した URL でコンテンツを取得します。 コンテンツを取得したら、それをヘルパー クラスの 1 つに逆シリアル化することで、やりとりがさらに容易になります。

ここでは、正常な完了を除くすべての状態が示されているポーリング コードを示します。正常な完了については次に取り上げます。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>文字起こしの結果を表示する
サービスによる文字起こしが正常に完了すると、その結果は、状態の応答から取得できる別の URL に格納されます。

この URL の内容をダウンロードし、JSON を逆シリアル化し、結果をループ処理し、表示テキストを出力します。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>コードを確認する
この時点で、コードは次のようになります。(このバージョンにはコメントをいくつか追加しています) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

これで、アプリをビルドし、Speech サービスを使用して音声認識をテストする準備ができました。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
