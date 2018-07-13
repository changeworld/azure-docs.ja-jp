---
title: Emotion API C# チュートリアル | Microsoft Docs
description: Cognitive Services Emotion API を使用して、イメージ内の顔で表現された感情を認識する基本的な Windows アプリについて説明します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/23/2017
ms.author: anroth
ms.openlocfilehash: f015e5720f65d0951a77de76ce8882a6dcdc1c3b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374176"
---
# <a name="emotion-api-in-c35-tutorial"></a>Emotion API C&#35; チュートリアル

> [!IMPORTANT]
> Video API のプレビューは 2017 年 10 月 30 日をもって終了します。 新しい [Video Indexer API のプレビュー](https://azure.microsoft.com/services/cognitive-services/video-indexer/)をお試しください。ビデオから分析情報を手軽に抽出でき、ビデオ内で話される言葉や表情、性格、感情を検知することで、検索結果などのコンテンツの検索エクスペリエンスを強化できます。 [詳細情報](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

Emotion API を使用して、イメージ内の顔で表現された感情を認識する基本的な Windows アプリケーションについて説明します。 下の例では、イメージ URL またはローカルに保存されたファイルを送信できます。 Emotion API と .NET Framework の一部である WPF (Windows Presentation Foundation) を使用して Windows 用の独自アプリをビルドするためのテンプレートとして、このオープン ソース例を使用できます。

## <a name="Prerequisites">前提条件</a>
#### <a name="platform-requirements"></a>プラットフォームの要件  
下の例は、[Visual Studio 2015、Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) を使用して、.NET Framework 向けに開発されています。  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Emotion API にサブスクライブしてサブスクリプション キーを取得する  
例を作成する前に、Microsoft Cognitive Services の一部である Emotion API にサブスクライブする必要があります。 [サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページをご覧ください。 プライマリ キーとセカンダリ キーの両方をこのチュートリアルで利用できます。 必ず、お使いの API を秘密かつ安全にしておくためのベスト プラクティスに従ってください。  

#### <a name="get-the-client-library-and-example"></a>クライアント ライブラリと例を入手する  
[SDK](https://www.github.com/microsoft/cognitive-emotion-windows) 経由で Emotion API のクライアント ライブラリをダウンロードできます。 ダウンロードした ZIP ファイルは、任意のフォルダーに抽出する必要があります、多くのユーザーは Visual Studio 2015 フォルダーを選択します。
## <a name="Step1">手順 1: 例を開く</a>
1.  Microsoft Visual Studio 2015 を起動し、**[ファイル]** をクリックし、**[開く]**、**[プロジェクト/ソリューション]** の順に選択します。
2.  ダウンロードした Emotion API ファイルを保存したフォルダーを参照します。 **[Emotion]**、**[Windows]**、**[サンプル WPF]** フォルダーの順にクリックします。
3.  **EmotionAPI WPF-Samples.sln** という名前の Visual Studio 2015 ソリューション (.sln) ファイルをダブルクリックして開きます。 これにより Visual Studio でソリューションが開きます。

## <a name="Step2">手順 2: 例をビルドする</a>
1. **ソリューション エクスプローラー**で、**[参照設定]** を右クリックし、**[NuGet パッケージの管理]** を選択します。

  ![NuGet パッケージ マネージャーを開く](../Images/EmotionNuget.png)

2.  **[NuGet パッケージ マネージャー]** ウィンドウが開きます。 最初に左上隅にある **[参照]** を選択し、続いて検索ボックスに "Newtonsoft.Json" と入力し、**Newtonsoft.Json** パッケージを選択して **[インストール]** をクリックします。  

  ![NuGet パッケージを参照する](../Images/EmotionNugetBrowse.png)  

3.  Ctrl キーと Shift キーを押しながら B キーを押すか、リボン メニューで **[ビルド]** をクリックしてから **[Build Solution]**(ソリューションのビルド) を選択します。

## <a name="Step3">手順 3: 例を実行する</a>
1.  ビルドが完了したら、**F5** キーを押すか、リボン メニューの **[Start]**(開始) をクリックして例を実行します。
2.  「**Paste your subscription key here to start**」(開始するにはここにサブスクリプション キーを貼り付けてください) と記されている**テキスト ボックス**を含む Emotion API ウィンドウを探します。 次のスクリーンショットに示すように、テキスト ボックスにサブスクリプション キーを貼り付けます。 [Save Key](キーの保存) ボタンをクリックすると、自分の PC またはラップトップにサブスクリプション キーを残すことを選択できます。 サブスクリプション キーをシステムから削除する場合は、[キーの削除] をクリックして、PC またはラップトップからキーを削除します。
  
  ![Emotion 機能インターフェイス](../Images/EmotionKey.png)

3.  「**シナリオの選択**」の下で、「**Detect emotion using a stream**」(ストリームを使用して感情を検出する) または「**Detect emotion using a URL**」(URL を使用して感情を検出する) の 2 つのシナリオのいずれかを選択し、画面の指示に従います。 Microsoft は、アップロードしたイメージを受け取り、Emotion API および関連サービスを向上させるために使用できます。 イメージを送信することにより、[開発者の倫理規定](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)に従っていることを確認します。
4.  このアプリケーション例で使用するイメージ例があります。 これらのイメージは、**データ** フォルダーの下の [Face API Github リポジトリ](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)にあります。 これらのイメージの使用は公正使用契約に基づいて許諾されています。つまり、これらのイメージはこの例をテストする目的で使用できますが、再発行に使用することはできません。

## <a name="Review">確認し学習する</a>
実行中のアプリケーションがあるので、このアプリ例が Microsoft Cognitive Services とどのように統合するか見てみましょう。 これにより、このアプリのビルドの継続や、Microsoft Emotion API を使用した独自のアプリの開発が簡単になります。 

このアプリ例では、Emotion API のクライアント ライブラリ、Microsoft Emotion API 用のシン C# クライアント ラッパーを利用します。 前述したようにアプリの例をビルドしたときに、NuGet パッケージからクライアント ライブラリを取得しました。 **Emotion**、**Windows**、**クライアント ライブラリ** の下にある「[Client Library](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)」という名前のフォルダーでクライアント ライブラリのソース コードを確認することができます。これは上記の「[前提条件](#Prerequisites)」でのダウンロードしたファイル リポジトリの一部です。
 
**ソリューション エクスプローラー**で、クライアント ライブラリ コードを使用する方法も見つかります。**EmotionAPI WPF_Samples** の下で、**DetectEmotionUsingStreamPage.xaml** を展開して、ローカルに保存されたファイルを参照するために使用される **DetectEmotionUsingStreamPage.xaml.cs** を探すか、**DetectEmotionUsingURLPage.xaml** を展開して、イメージ URL のアップロード時に使用される **DetectEmotionUsingURLPage.xaml.cs** を見つけます。 .xaml.cs ファイルをダブルクリックして Visual Studio の新しいウィンドウで開きます。 

アプリ例で Emotion クライアント ライブラリがどのように使用されるかを確認し、**DetectEmotionUsingStreamPage.xaml.cs** と **DetectEmotionUsingURLPage.xaml.cs** からの 2 つのコード スニペットを見てみましょう。 各ファイルには、"KEY SAMPLE CODE STARTS HERE" と "KEY SAMPLE CODE ENDS HERE" を示すコード コメントが含まれており、下で再現されているコード スニペットを探す際に役立ちます。

Emotion API は、入力として、イメージ URL とバイナリ イメージ データ (オクテット ストリームの形式) のどちらでも扱えます。 2 つのオプションは、下に表示されています。 どちらの場合も、最初に using ディレクティブを見つけます。これがあれば、Emotion クライアント ライブラリを使用できます。 
```csharp

            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE STARTS HERE 
            // Use the following namespace for EmotionServiceClient 
            // ----------------------------------------------------------------------- 
            using Microsoft.ProjectOxford.Emotion; 
            using Microsoft.ProjectOxford.Emotion.Contract; 
            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------- 
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs 

このコード スニペットは、クライアント ライブラリを使用して、サブスクリプション キーおよびフォト URL を Emotion API サービスに送信する方法を示しています。 

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs 

下には、サブスクリプション キーおよびローカルで保存されたイメージを Emotion API に送信する方法が示されています。 


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
