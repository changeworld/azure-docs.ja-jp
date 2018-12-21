---
title: 'クイック スタート: 音声を認識する、Java - Speech Service'
titleSuffix: Azure Cognitive Services
description: Java (Windows または Linux) での音声認識方法の詳細
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: fmegen
ms.openlocfilehash: 7d1f26a43866025c3b542fc10a3f316ad0d1dc37
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103121"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-service-sdk"></a>クイック スタート: Windows または Linux で Java と Speech Service SDK を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、[Speech Service SDK](speech-sdk.md) を使用して、Java コンソール アプリケーションを作成します。 PC のマイクからリアルタイムで音声をテキストに変換します。 アプリケーションは、Speech SDK Maven パッケージと、64 ビット Windows または Ubuntu Linux 16.04 上の Eclipse Java IDE (v4.8) で構築されます。 これは、64 ビットの Java 8 のランタイム環境 (JRE) で実行されます。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](speech-devices-sdk.md) を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech サービス サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、「[Speech Service を無料で試す](get-started.md)」を参照してください。


## <a name="create-and-configure-project"></a>プロジェクトの作成と構成

Eclipse を開始する前に Ubuntu 16.04 を使用している場合は、次のコマンドを実行して、必要なパッケージがインストールされていることを確認します。

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Eclipse を起動します。

1. [Eclipse Launcher] で、新しいディレクトリの名前を **[Workspace]** フィールドに入力します。 次に **[Launch]** を選択します。

   ![[Eclipse Launcher] のスクリーンショット](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. しばらくすると、Eclipse IDE のメイン ウィンドウが表示されます。 ウェルカム画面が表示される場合は画面を閉じます。

1. Eclipse メニュー バーから、 **[File]** > **[New]** > **[Project]** を選択して新しいプロジェクトを作成します。

1. **[新しいプロジェクト]** ダイアログ ボックスが表示されます。 **[Java Project]** を選択し、**[Next]** を選択します。

   ![Java プロジェクトが強調表示されている [新しいプロジェクト] ダイアログ ボックスのスクリーンショット](media/sdk/qs-java-jre-02-select-wizard.png)

1. [New Java Project] ウィザードが開始されます。 **[Project name]** フィールドに「**quickstaart**」と入力し、実行環境として **[JavaSE-1.8]** を選択します。 **[完了]** を選択します。

   ![[新規 Java プロジェクト] ウィザードのスクリーンショット](media/sdk/qs-java-jre-03-create-java-project.png)

1. **[Open Associated Perspective?]\(パースペクティブを開きますか?)** というウィンドウが表示される場合は、**[Open Perspective]\(パースペクティブを開く)** を選択します。

1. **Package explorer** で、**quickstart** プロジェクトを右クリックします。 コンテキスト メニューから、**[Configure]** > **[Convert to Maven Project]** を選択します。

   ![パッケージ エクスプローラーのスクリーンショット](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. **[Create new POM]** ウィンドウが表示されます。 **[Group Id]** フィールドに、 **com.microsoft.cognitiveservices.speech.samples** と入力し、**[Artifact Id]** フィールドに、**quickstart** と入力します。 続けて、**[完了]** を選択します。

   ![[Create new POM] ウィンドウのスクリーンショット](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. **pom.xml** ファイルを開いて編集します。

   * ファイルの最後にある、終了タグ `</project>` の前に、次に示すように Speech SDK 用の Maven リポジトリへの参照を含む `repositories` 要素を作成します。

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * その後、依存関係として Speech SDK バージョン 1.1.0 を指定した `dependencies` 要素も追加します。

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * 変更を保存します。

## <a name="add-sample-code"></a>サンプル コードを追加する

1. 新しい空のクラスを Java プロジェクトに追加するために、**[File]**  >  **[New]**  >  **[Class]** の順に選択します。

1. **[New Java Class]** ウィンドウで、**[Package]** フィールドに **speechsdk.quickstart** と入力し、**[Name]** フィールドに **Main** と入力します。

   ![[New Java Class] ウィンドウのスクリーンショット](media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java` のすべてのコードを次のスニペットに置き換えます。

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

F11 キーを押すか、**[Run]**  >  **[Debug]** の順に選択します。
その後 15 秒間、マイクからの音声入力が認識され、コンソール ウィンドウにログが記録されます。

![認識が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/java-jre` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech SDK for Java を使用して音声から意図を認識する](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>関連項目

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
