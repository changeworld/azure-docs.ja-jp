---
title: Azure Content Moderator - .NET を使用してモデレーション ジョブを開始する | Microsoft Docs
description: Azure Content Moderator SDK for .NET を使用してモデレーション ジョブを開始する方法
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373152"
---
# <a name="start-moderation-jobs-using-net"></a>.NET を使用してモデレーション ジョブを開始する

この記事では、Content Moderator SDK for .NET を初めて使用して次の処理を行うときに役立つ情報とコード サンプルを提供します。
 
- ヒューマン モデレーターがレビューをスキャンし、作成するモデレーション ジョブを開始する
- 保留中のレビューの状態を取得する
- レビューの最終状態を追跡して取得する
- 結果をコールバック URL に送信する

この記事では Visual Studio と C# に精通していることを前提としています。

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator サービスにサインアップする

REST API や SDK を通じて Content Moderator サービスを使用するには、サブスクリプション キーが必要です。
キーを入手する方法については、[クイック スタート](quick-start.md)を参照してください。

## <a name="define-a-custom-moderation-workflow"></a>カスタム モデレーション ワークフローを定義する

モデレーション ジョブでは、API を使用してコンテンツがスキャンされ、**ワークフロー**を使用してレビューを作成するかどうかが決定されます。
レビュー ツールには既定のワークフローが含まれていますが、このクイックスタート用に[カスタムのワークフローを定義](Review-Tool-User-Guide/Workflows.md)してみましょう。

コードでは、モデレーション ジョブを開始するワークフローの名前を使用します。

## <a name="create-your-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. ソリューションに新しい**コンソール アプリ (.NET Framework)** プロジェクトを追加します。

   サンプル コードで、プロジェクトに **CreateReviews** と名前を付けます。

1. このプロジェクトをソリューションのシングル スタートアップ プロジェクトとして選択します。

1. [Content Moderator クライアント ヘルパーのクイックスタート](content-moderator-helper-quickstart-dotnet.md)で作成した **ModeratorHelper** プロジェクト アセンブリへの参照を追加します。

### <a name="install-required-packages"></a>必要なパッケージをインストールする

次の NuGet パッケージをインストールします。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>プログラムの using ステートメントを更新する

プログラムの using ステートメントを変更します。

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>アプリケーション固有の設定を初期化する

次の定数と静的フィールドを Program.cs 内の **Program** クラスに追加します。

> [!NOTE]
> TeamName 定数は、Content Moderator サブスクリプションの作成時に使用した名前に設定します。 [Content Moderator Web サイト](https://westus.contentmoderator.cognitive.microsoft.com/)から TeamName を取得します。
> ログインしたら、**[設定]** (歯車) メニューから **[資格情報]** を選択します。
>
> チーム名は、**[API]** セクションの **[ID]** フィールドの値です。


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>自動モデレートするコードを追加し、レビューを作成し、ジョブの詳細を取得する

> [!Note]
> 実際には、コールバック URL **CallbackEndpoint** を (HTTP POST 要求経由で) 手動レビューの結果を受け取る URL に設定します。

まず、**Main** メソッドに次のコードを追加します。

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> Content Moderator サービス キーには、Requests per Second (RPS) の速度制限があります。 この制限を超えた場合、SDK は 429 エラー コードを使用して例外をスローします。 
>
> 階層なしのキーの RPS は 1 に制限されています。

## <a name="run-the-program-and-review-the-output"></a>プログラムを実行して出力を確認する

コンソールに次のサンプル出力が表示されます。

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Content Moderator レビュー ツールにサインインして、保留中の画像レビューを確認します。

**[次へ]** ボタンを使用して送信します。

![ヒューマン モデレーター用の画像レビュー](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>ログ ファイルのサンプル出力を確認する

> [!NOTE]
> 出力ファイルの **Teamname**、**ContentId**、**CallBackEndpoint**、および **WorkflowId** の文字列は、以前使用した値を反映しています。

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>コールバック URL を指定した場合は、この応答を受け取ります。

次の例のような応答が表示されます。

> [!NOTE]
> コールバック応答の文字列 **ContentId** および **WorkflowId** には、以前使用した値が反映されます。

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>次の手順

.NET 用のこのクイック スタートや他の Content Moderator のクイック スタートの [Visual Studio ソリューションをダウンロード](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)し、統合を開始します。
