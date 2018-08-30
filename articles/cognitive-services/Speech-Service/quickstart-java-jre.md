---
title: 'クイック スタート: Java (Windows または Linux) での音声認識'
titleSuffix: Microsoft Cognitive Services
description: Java (Windows または Linux) での音声認識方法の詳細
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 923ab3378d5e2d833e11c5111d4dd9964fea6dc4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126615"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>クイック スタート: Java (Windows または Linux) での音声認識

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

このドキュメントでは、Speech SDK を利用する Java ランタイム環境 (JRE) 用の Java ベースのコンソール アプリケーションを作成する方法について説明します。
このアプリケーションは、Microsoft Cognitive Services SDK Maven パッケージが基になっています。
統合開発環境 (IDE) として Eclipse を使用します。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 [Speech サービスを無料で試す](get-started.md)ための記事を参照してください。
* 機能するマイクを備えた、Eclipse を実行できる PC (Windows x64、Ubuntu 16.04 x64)。
* Java 8 用の 64 ビット JRE/JDK。
* バージョン 4.8 の [Eclipse](https://www.eclipse.org) (64 ビット バージョン)。
* Ubuntu 16.04 では、必要なパッケージのインストールに次のコマンドを実行します。

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>プロジェクトの作成と構成

1. Eclipse を起動します。

1. [Eclipse Launcher] で、新しいディレクトリの名前を **[Workspace]** フィールドに入力します。
   次に、**[Launch]** をクリックします。

   ![Eclipse ワークスペースの作成](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. しばらくすると、Eclipse IDE のメイン ウィンドウが表示されます。
   [Welcome] 画面が表示される場合は閉じます。

1. **[File]** \> **[New]** \> **[Project]** の順に選択します。

1. **New Project** ウィザードが表示されたら、**[Java Project]** を選択して、**[Next]** をクリックします。

   ![ウィザードの選択](media/sdk/qs-java-jre-02-select-wizard.png)

1. 次のウィンドウで、プロジェクト名として **quickstart** と入力し、実行環境として **[JavaSE-1.8]** (またはそれ以降) を選択します。
   **[完了]** をクリックします。

   ![ウィザードの選択](media/sdk/qs-java-jre-03-create-java-project.png)

1. **[Open Associated Perspective?]** というタイトルのウィンドウがポップ アップ表示される場合は、**[Open Perspective]** を選択します。

1. **Package explorer** で **quickstart** プロジェクトを右クリックして、**[Configure]** \> **[Convert to Maven Project]** の順に選択します。

   ![Maven プロジェクトへの変換](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. ポップアップ表示されるウィンドウで、**[Group Id]** に **com.microsoft.cognitiveservices.speech.samples** と入力し、**[Artifact Id]** に **quickstart** と入力します。**[完了]** を選択します。

   ![Maven POM の構成](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. **pom.xml** ファイルを編集します。

  * ファイルの最後にある、終了タグ `</project>` の前に、Speech SDK 用の Maven リポジトリへの参照を含むリポジトリ セクションを作成します。

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * その後、依存関係として Speech SDK バージョン 0.6.0 を指定した依存関係セクションも追加します。

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * 変更を保存します。

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. **[File]** \> **[New]** \> **[Class]** の順に選択して、新しい空のクラスを Java プロジェクトに追加します。

1. **[New Java Class]** ウィンドウで、**[Package]** フィールドに **speechsdk.quickstart** と入力し、**[Name]** フィールドに **Main** と入力します。

   ![Main クラスの作成](media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java` のすべてのコードを次のスニペットに置き換えます。

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

F11 キーを押すか、**[Run]** \> **[Debug]** の順に選択します。
その後 15 秒間、マイクからの音声入力が認識され、コンソール ウィンドウにログが記録されます。

![認識が成功した後のコンソール出力](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/java-jre` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

* [サンプルを入手する](speech-sdk.md#get-the-samples)
