---
title: Media Services v3 を使用してビデオを分析する
description: Azure Media Services を使用してビデオを分析する方法について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 5155ffed26daaf53b991a84971929500057d0007
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275280"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>チュートリアル:Media Services v3 を使用してビデオを分析する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

このチュートリアルでは、Azure Media Services を使用したビデオの分析について説明します。 記録されたビデオまたはオーディオ コンテンツに関する詳細な分析情報を得る必要のある多くのシナリオがあります。 たとえば、より高い顧客満足度を実現するため、組織は音声テキスト変換処理を実行して、カスタマー サポートの記録をインデックスとダッシュボードを含む検索可能なカタログに変換できます。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * このトピックで説明されているサンプル アプリをダウンロードする。
> * 指定されたビデオを分析するコードを調べる。
> * アプリケーションを実行します。
> * 出力を調べる。
> * リソースをクリーンアップする。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>コンプライアンス、プライバシー、セキュリティ
 
重要な注意事項として、Video Indexer の使用においては、適用されるすべての法令に従う必要があります。 他の人の権利を侵害するような方法で Video Indexer またはその他の Azure サービスを使用することはできません。 生体認証データなどのビデオを Video Indexer サービスにアップロードして処理と保管を行う前に、ビデオに写っている個人から適切なすべての同意を得ることを含め、適切な権限をすべて持っている必要があります。 Video Indexer のコンプライアンス、プライバシー、セキュリティについては、Azure の [Cognitive Services の条項](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)に関するページを参照してください。 Microsoft のプライバシー義務とデータの取り扱いについては、Microsoft の[プライバシー ステートメント](https://privacy.microsoft.com/PrivacyStatement)、[オンライン サービス条件](https://www.microsoft.com/licensing/product-licensing/products) (OST)、および[データ処理の補遺](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA") に関するページをご確認ください。 データの保有、削除、破棄などのその他のプライバシー情報は、OST および[こちら](../video-indexer/faq.md)で確認できます。 Video Indexer を使用すると、Cognitive Services の条項、OST、DPA、およびプライバシー ステートメントに従うことに同意したものと見なされます。

## <a name="prerequisites"></a>前提条件

- Visual Studio がインストールされていない場合は、[Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) を入手してください。
- [Media Services アカウントを作成する](./account-create-how-to.md)<br/>Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。
- 「[Azure CLI で Azure Media Services API にアクセスする](./access-api-howto.md)」の手順に従い、資格情報を保存します。 API にアクセスするために必要となります。

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使って、.NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

サンプルは [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos) フォルダーにあります。

ダウンロードしたプロジェクトに含まれる [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) を開きます。 [API へのアクセス](./access-api-howto.md)に関するページで取得した資格情報の値に置き換えます。

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>指定されたビデオを分析するコードを調べる

このセクションでは、*AnalyzeVideos* プロジェクトの [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) ファイルで定義されている関数を調べます。

サンプルを実行すると、次のアクションが行われます。

1. ビデオを分析する **変換** と **ジョブ** を作成します。
2. 入力 **アセット** を作成し、そこにビデオをアップロードします。 アセットは、ジョブの入力として使用されます。
3. ジョブの出力を格納する出力アセットを作成します。
4. ジョブを送信します。
5. ジョブの状態を確認します。
6. ジョブの実行から生成されるファイルをダウンロードします。

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>入力アセットを作成し、ローカル ファイルをそれにアップロードする 

**CreateInputAsset** 関数は、新しい入力 [アセット](/rest/api/media/assets)を作成し、指定されたローカル ビデオ ファイルをそこにアップロードします。 このアセットは、エンコード ジョブへの入力として使われます。 Media Services v3 では、ジョブへの入力としては、アセットを使うか、または HTTPS URL 経由で Media Services アカウントから使用できるようにされたコンテンツを使うことができます。 HTTPS URL からのエンコード方法については、[こちら](job-input-from-http-how-to.md)の記事を参照してください。  

Media Services v3 では、Azure Storage API を使ってファイルをアップロードします。 次の .NET スニペットはその方法を示したものです。

後で示す関数は、次の処理を実行します。

* アセットを作成する。
* 書き込み可能な [SAS URL](../../storage/common/storage-sas-overview.md) を資産の[ストレージ内のコンテナー](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)に取得する。

    資産の [ListContainerSas](/rest/api/media/assets/listcontainersas) 関数を使用して SAS URL を取得する場合、複数の SAS URL が返されることに注意してください。これは、ストレージ アカウント キーがストレージ アカウントごとに 2 つ存在するためです。 ストレージ アカウントにキーが 2 つあるのは、ストレージ アカウント キーのシームレスなローテーションを可能にするためです (一方のキーを使用しながらもう一方のキーを変更した後、新しいキーの使用を開始し、その後もう一方のキーをローテーションするなど)。 1 つ目の SAS URL はストレージ キー 1 を、2 つ目の SAS URL はストレージ キー 2 を表します。
* SAS URL を使用してストレージ内のコンテナーにファイルをアップロードする。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>ジョブの結果を格納する出力アセットを作成する

出力[アセット](/rest/api/media/assets)には、ジョブの結果が格納されます。 プロジェクトで定義されている **DownloadResults** 関数は、この出力アセットから "output" フォルダーに結果をダウンロードするので、取得したものを確認できます。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>ビデオを分析する変換とジョブを作成する

Media Services でコンテンツをエンコードまたは処理するときは、レシピとしてエンコード設定をセットアップするのが一般的なパターンです。 その後、**ジョブ** を送信してビデオにレシピを適用します。 新しいビデオごとに新しいジョブを送信することで、ライブラリ内のすべてのビデオにレシピを適用します。 Media Services でのレシピは **変換** と呼ばれます。 詳しくは、「[Transforms and jobs](./transform-jobs-concept.md)」(変換とジョブ) をご覧ください。 このチュートリアルで説明されているサンプルでは、指定されたビデオを分析するレシピが定義されています。

#### <a name="transform"></a>変換

新しい [Transform](/rest/api/media/transforms) インスタンスを作成するときは、出力として生成するものを指定する必要があります。 **TransformOutput** は必須パラメーターです。 各 **TransformOutput** には **Preset** が含まれます。 **Preset** では、目的の **TransformOutput** の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 この例では、**VideoAnalyzerPreset** プリセットが使われていて、言語 ("en-US") がコンストラクター (`new VideoAnalyzerPreset("en-US")`) に渡されています。 このプリセットを使用して、ビデオから複数の音声と画像の分析情報を抽出できます。 複数のオーディオ情報をビデオから抽出する必要がある場合は、**AudioAnalyzerPreset** プリセットを使用できます。

**Transform** を作成するときは、次のコードに示すように、最初に **Get** メソッドを使って変換が既に存在するかどうかを確認します。 Media Services v3 では、エンティティが存在しない場合 (大文字と小文字の区別がない名前のチェック)、エンティティに対する **Get** メソッドは **null** を返します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>ジョブ

上で説明したように、[Transform](/rest/api/media/transforms) オブジェクトはレシピであり、[Job](/rest/api/media/jobs) は **Transform** が特定の入力ビデオまたはオーディオ コンテンツに適用する Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。 ビデオの場所は、HTTPS URL、SAS URL、または Media Service アカウント内のアセットを使用して指定できます。

この例では、ジョブの入力はローカル ビデオです。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>ジョブが完了するのを待つ

ジョブが完了するまでに、一定の時間がかかります。 完了したら通知を受け取りたいでしょう。 [ジョブ](/rest/api/media/jobs)完了の通知の取得にはさまざまなオプションがあります。 最も簡単なオプション (次に示すもの) は、ポーリングを使うものです。

待機時間が発生する可能性があるため、ポーリングは運用アプリに推奨されるベスト プラクティスではありません。 アカウントで過剰に使った場合、ポーリングはスロットルされる可能性があります。 開発者は、代わりに Event Grid を使う必要があります。

Event Grid は、高可用性、一貫したパフォーマンス、および動的スケーリングを目的に設計されています。 Event Grid では、アプリはほぼすべての Azure サービスやカスタム ソースのイベントをリッスンし、対応できます。 単純な HTTP ベースのリアクティブ イベント ハンドリングでは、インテリジェントなイベント フィルタリングやイベント ルーティングを使用して、効率的なソリューションを構築できます。 詳細については、[カスタム Web エンドポイントにイベントをルーティングする方法](monitoring/job-state-events-cli-how-to.md)に関するページを参照してください。

**Job** には通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態が返されます。 ジョブがキャンセル処理中の場合は **Canceling** が、完了したときには **Canceled** が返されます。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>ジョブ エラー コード

[エラー コード](/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

### <a name="download-the-result-of-the-job"></a>ジョブの結果をダウンロードする

次の関数は出力[アセット](/rest/api/media/assets)から "output" フォルダーに結果をダウンロードするので、ジョブの結果を調べることができます。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

一般に、再利用を計画しているオブジェクトを除くすべてのものをクリーンアップする必要があります (通常、Transform は再利用し、StreamingLocator は保持します)。 実験後にお使いのアカウントをクリーン アップする場合は、再利用する予定のないリソースを削除します。 たとえば、次のコードでジョブと出力された資産を削除します。

### <a name="delete-resources-with-code"></a>コードでリソースを削除する

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

CLI を使うこともできます。

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

Ctrl + F5 キーを押して、*AnalyzeVideos* アプリを実行します。

プログラムを実行すると、ジョブはビデオで検出された各顔のサムネイルを生成します。 insights.json ファイルも生成します。

## <a name="examine-the-output"></a>出力を調べる

ビデオ分析の出力ファイルは、insights.json という名前です。 このファイルには、ビデオに関する分析情報が含まれています。 json ファイルに含まれる要素の説明については、「[Media intelligence](./analyze-video-audio-files-concept.md)」(メディア インテリジェンス) をご覧ください。

## <a name="multithreading"></a>マルチスレッド

> [!WARNING]
> Azure Media Services v3 SDK は、スレッドセーフではありません。 マルチスレッド アプリで使うときは、スレッドごとに新しい AzureMediaServicesClient オブジェクトを生成する必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル: ファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md)
