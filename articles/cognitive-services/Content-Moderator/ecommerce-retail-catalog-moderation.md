---
title: Azure Content Moderator を使用した機械学習と AI による e コマース カタログ モデレーション | Microsoft Docs
description: 機械学習と AI を使用して e コマース カタログを自動的にモデレートする
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 6177758eaa3e611ad67da0778d889df48b052d90
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095753"
---
# <a name="ecommerce-catalog-moderation-with-machine-learning"></a>機械学習による e コマース カタログのモデレーション

このチュートリアルでは、マシン支援型 AI テクノロジと人によるモデレーションを組み合わせて、機械学習ベースの高度な e コマース カタログ モデレーションを実装して、高度なカタログ システムを提供する方法について説明します。

![分類された製品の画像](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>ビジネス シナリオ

マシン支援型テクノロジを使用して、次のカテゴリに製品画像を分類し、調整します。

1. 成人向け (ヌード)
2. わいせつ (際どい)
3. 有名人
4. 米国旗
5. おもちゃ
6. ペン

## <a name="tutorial-steps"></a>チュートリアルの手順

このチュートリアルでは、以下の手順について説明します。

1. Content Moderator にサインアップしてチームを作成します。
2. 有名人と旗の可能性があるコンテンツのためのモデレーション タグ (ラベル) を構成します。
3. Content Moderator の Image API を使用して、成人向けとわいせつ向けの可能性があるコンテンツをスキャンします。
4. 有名人の可能性がある画像をスキャンするには、Computer Vision API を使用します。
5. 旗が存在する可能性がある画像をスキャンするには、Custom Vision サービスを使用します。
6. 人によるレビューと最終的な意思決定のために、微妙なスキャン結果を表示します。

## <a name="create-a-team"></a>チームを作成する

Content Moderator にサインアップしてチームを作成する方法については、[クイック スタート](quick-start.md)のページを参照してください。 **[Credentials]\(資格情報\)** ページの **[Team ID]\(チーム ID\)** をメモします。


## <a name="define-custom-tags"></a>カスタム タグを定義する

カスタム タグを追加するには、[タグ](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags)に関する記事を参照してください。 組み込みの **adult** (成人) タグと **racy** (わいせつ) タグに加えて、新しいタグを使用すると、レビュー ツールでタグのわかりやすい名前を表示できます。

この例では、カスタム タグ **celebrity** (有名人)、**flag** (旗)、**us** (米国)、**toy** (おもちゃ)、**pen** (ペン) を定義しています。

![カスタム タグを構成する](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>API キーとエンドポイントを一覧表示する

1. このチュートリアルでは、3 つの API と、対応するキーと API エンドポイントを使用しています。
2. API エンドポイントは、サブスクリプションのリージョンと Content Moderator Review Team ID によって異なります。

> [!NOTE]
> このチュートリアルは、次のエンドポイントで表示できるリージョンのサブスクリプション キーを使用するように設計されています。 実際の API キーとリージョン キーを一致させてください。そうしないと、次のエンドポイントではお使いのキーが機能しない可能性があります。

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>成人向けコンテンツとわいせつなコンテンツをスキャンする

1. この関数は、画像 URL と、キーと値のペアの配列をパラメーターとして受け取ります。
2. この関数から Content Moderator の Image API が呼び出され、Adult と Racy のスコアが取得されます。
3. スコアが 0.4 を超える場合 (範囲は 0 から 1)、**ReviewTags** 配列の値は **True** に設定されます。
4. **ReviewTags** 配列は、レビュー ツール内の対応するタグを強調表示するために使用されます。

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>有名人をスキャンする

1. [Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) の[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)にサインアップします。
2. **[API キーの取得]** をクリックします。
3. 使用条件に同意します。
4. ログインするには、使用できるインターネット アカウントのリストから選択します。
5. サービス ページに表示される API キーをメモします。
    
   ![Computer Vision API キー](images/tutorial-computer-vision-keys.PNG)
    
6. Computer Vision API を使用して画像をスキャンする関数のプロジェクト ソース コードを参照してください。

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>旗、おもちゃ、ペンに分類する

1. [Custom Vision API プレビュー](https://www.customvision.ai/)に[サインイン](https://azure.microsoft.com/en-us/services/cognitive-services/custom-vision-service/)します。
2. [クイック スタート](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)を使用して、旗、おもちゃ、ペンが存在する可能性があるコンテンツを検出するカスタム分類子を構築します。
   ![Custom Vision のトレーニング画像](images/tutorial-ecommerce-custom-vision.PNG)
3. カスタム分類子の[予測エンドポイント URL を取得します](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api)。
4. 画像をスキャンするためにカスタム分類子の予測エンドポイントを呼び出す関数については、プロジェクトのソース コードを参照してください。

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>human-in-the-loop (人間参加) のレビュー

1. 前述のセクションでは、成人とわいせつ (Content Moderator)、有名人 (Computer Vision)、旗 (Custom Vision) について受信画像をスキャンしました。
2. 各スキャンの一致しきい値に基づいて、微妙な場合にレビュー ツールで人がレビューできるようにします。
        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>画像のバッチを送信する

1. このチュートリアルでは、画像 URL のリストが記載されたテキスト ファイルが "C:Test" ディレクトリにあるとします。
2. 次のコードは、ファイルの存在をチェックし、すべての URL をメモリに読み込みます。
            // Check for a test directory for a text file with the list of Image URLs to scan var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>すべてのスキャンを開始する

1. このトップレベル関数では、前述のテキスト ファイルに含まれるすべての画像 URL がループ処理されます。
2. 画像 URL は各 API でスキャンされ、一致信頼度スコアが基準に該当する場合、ヒューマン モデレーター用のレビューが作成されます。
             // for each image URL in the file... foreach (var Url in Urls) { // Initiatize a new review tags array ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>ライセンス

すべての Microsoft Cognitive Services SDK およびサンプルには、MIT ライセンスがあります。 詳細については、[ライセンス](https://microsoft.mit-license.org/)に関するページを参照してください。

## <a name="developer-code-of-conduct"></a>開発者の倫理規定

このクライアント ライブラリとサンプルを含め、Cognitive Services を使用する開発者は、「Developer Code of Conduct for Microsoft Cognitive Services」(Microsoft Cognitive Services の開発者の倫理規定) (http://go.microsoft.com/fwlink/?LinkId=698895) に従うものとします。

## <a name="next-steps"></a>次の手順

Github の[プロジェクト ソース ファイル](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)を使用して、チュートリアルを構築し、拡張します。
