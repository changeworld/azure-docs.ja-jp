---
title: Computer Vision API C# チュートリアル | Microsoft Docs
description: Microsoft Cognitive Services で Computer Vision API を使用する基本的な Windows アプリについて説明します。 OCR を実行し、サムネイルを作成して、イメージ内の視覚的な特徴を操作します。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374752"
---
# <a name="computer-vision-api-c35-tutorial"></a>Computer Vision API C&#35; チュートリアル

Computer Vision REST API を使用して、光学文字認識 (OCR) を実行し、スマートにクロップされたサムネイルを作成するほか、イメージ内の視覚的な特徴 (顔など) を検出、カテゴライズ、タグ付け、および記述する、基本的な Windows アプリケーションについて説明します。 下の例では、イメージ URL またはローカルに保存されたファイルを送信できます。 このオープン ソースの例は、Vision API と、.NET Framework の一部である WPF (Windows Presentation Foundation) を使用して、Windows 用の独自アプリをビルドするためのテンプレートとして使用できます。

### <a name="prerequisites"></a>前提条件

#### <a name="platform-requirements"></a>プラットフォームの要件

下の例は、[Visual Studio 2015、Community Edition](https://www.visualstudio.com/downloads/) を使用して、.NET Framework 向けに開発されています。

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Computer Vision API にサブスクライブしてサブスクリプション キーを取得する 

例を作成する前に、Microsoft Cognitive Services (以前の Project Oxford) の一部である Computer Vision API () にサブスクライブする必要があります。 サブスクリプションとキーの管理の詳細については、[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページを参照してください。 プライマリ キーとセカンダリ キーの両方をこのチュートリアルで利用できます。 

> [!NOTE]
> このチュートリアルは、**westcentralus** リージョンでサブスクリプション キーを使用するように設計されています。 Computer Vision の無料試用時に生成されるサブスクリプション キーは **westcentralus** リージョンを使用するので、それらのキーは正常に機能します。 [https://azure.microsoft.com/](https://azure.microsoft.com/) から自分の Azure アカウントを使用してサブスクリプション キーを生成した場合は、**westcentralus** リージョンを指定する必要があります。 **westcentralus** リージョンの外部で生成されたキーは機能しません。

#### <a name="get-the-client-library-and-example"></a>クライアント ライブラリと例を入手する

[SDK](https://www.github.com/microsoft/cognitive-vision-windows) から自分のコンピューターに、Computer Vision API クライアント ライブラリとアプリケーション例を複製できます。 ZIP としてダウンロードしないでください。

### <a name="Step1">手順 1: 例をインストールする</a>

GitHub Desktop で、Sample-WPF\VisionAPI-WPF-Samples.sln を開きます。

### <a name="Step2">手順 2: 例をビルドする</a>

* Ctrl キーと Shift キーを押しながら B キーを押すか、リボン メニューで [ビルド] をクリックしてから [ソリューションのビルド] を選択します。

### <a name="Step3">手順 3: 例を実行する</a>

1. ビルドが完了したら、**F5** キーを押すか、リボン メニューの **[Start]**(開始) をクリックして例を実行します。
2. "Paste your subscription key here to start"(開始するにはここにサブスクリプション キーを貼り付けてください) と書かれたテキスト編集ボックスがある、Computer Vision API ユーザー インターフェイスのウィンドウを見つけます。
[Save Key](キーの保存) ボタンをクリックすると、自分の PC またはラップトップにサブスクリプション キーを残すことを選択できます。 サブスクリプション キーをシステムから削除する場合は、[キーの削除] をクリックして、PC またはラップトップからキーを削除します。

    ![Vision のサブスクリプション キー](../Images/Vision_UI_Subscription.PNG)

3. [Select Scenario](シナリオの選択) の下で、6 つのうち、使用するいずれかのシナリオをクリックし、画面の指示に従います。 Microsoft は、ユーザーがアップロードしたイメージを受け取り、Computer Vision API および関連サービスを改善するためにそれらを使用することがあります。 イメージを送信することにより、[開発者の倫理規定](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)に従っていることを確認します。

    ![イメージ分析のインターフェイス](../Images/Analyze_Image_Example.PNG)

4. このアプリケーション例で使用するイメージ例があります。 これらのイメージは、Face API Windows Github リポジトリの [Data](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) フォルダー内にあります。 これらのイメージの使用は、[イメージのライセンス](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md)についての契約のもとでライセンスされることに注意してください。

### <a name="Review">確認し学習する</a>

動作するアプリケーションを準備したので、アプリの例が Cognitive Services 技術とどのように統合されているかをよく調べていきましょう。 これで、このアプリ上でのビルドや Microsoft Computer Vision API を使用した独自のアプリの開発を続けるのがより容易になります。

このアプリの例では、Microsoft Computer Vision API 用の C# シン クライアント ラッパーである Computer Vision API クライアント ライブラリを利用します。 前述したようにアプリの例をビルドしたときに、NuGet パッケージからクライアント ライブラリを取得しました。 クライアント ライブラリのソース コードは、**Vision**、**Windows**、**Client Library** の下にある "**Client Library**" という名前のフォルダーで確認できます。これは、上記の「前提条件」で触れた、ダウンロードしたファイル リポジトリの一部となっています。

ソリューション エクスプローラーで、クライアント ライブラリのコードを使用する方法を見つけることもできます。**VisionAPI-WPF_Samples** の下で **AnalyzePage.xaml**を展開し、**AnalyzePage.xaml.cs** を見つけます。これは、イメージ分析エンドポイントにイメージを送信するために使用されます。 .xaml.cs ファイルをダブルクリックして Visual Studio の新しいウィンドウで開きます。

アプリの例の中で Vision クライアント ライブラリがどのように使用されているかをよく調べ、**AnalyzePage.xaml.cs** に含まれる 2 つのコード スニペットを見てみましょう。 このファイルには、"KEY SAMPLE CODE STARTS HERE" (主要なサンプル コードはここから開始) と "KEY SAMPLE CODE ENDS HERE" (主要なサンプル コードはここで終了) というコード コメントが含まれていて、下に再掲したコード スニペットを探すのに役立ちます。

分析エンドポイントは、入力として、イメージ URL とバイナリ イメージ データ (オクテット ストリームの形式) のどちらでも扱えます。 最初に、using ディレクティブを見つけます。これがあると Vision クライアント ライブラリを使用できます。

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(…)**: このコード スニペットは、クライアント ライブラリを使用して、サブスクリプション キーとローカルに保存されたイメージを Computer Vision API サービスの分析エンドポイントに送信する方法を示しています。

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(…)**: このコード スニペットは、クライアント ライブラリを使用して、サブスクリプション キーと写真の URL を Computer Vision API サービスの分析エンドポイントに送信する方法を示しています。

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**その他のページやエンドポイント**: Computer Vision API サービスによって公開されているその他のエンドポイントを操作する方法は、サンプル内の他のページを見て調べることでわかります。たとえば、OCR エンドポイントは OCRPage.xaml.cs に含まれるコードの一部として示されています 

### <a name="Related">関連トピック</a>
 * [Face API を使ってみる](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


