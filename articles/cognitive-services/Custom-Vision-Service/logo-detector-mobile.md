---
title: チュートリアル:カスタム ロゴ検出器を使用して Azure サービスを認識する - Custom Vision
titlesuffix: Azure Cognitive Services
description: このチュートリアルでは、ロゴ検出シナリオの一部として Azure Custom Vision を使用するサンプル アプリについて見ていきます。 Custom Vision を他のコンポーネントと共に使用してエンド ツー エンドのアプリケーションを提供する方法を学習します。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 520e1981544f6ad0a6f1412a47dc8ea50fdd53a5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771465"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>チュートリアル:写真内にある Azure サービスのロゴを認識する

このチュートリアルでは、より大きなシナリオの一部として Azure Custom Vision を使用するサンプル アプリについて見ていきます。 モバイル プラットフォーム用の Xamarin.Forms アプリである AI Visual Provision アプリは、Azure サービスのロゴの写真を分析した後、実際のサービスをユーザーの Azure アカウントにデプロイします。 ここでは、このアプリで Custom Vision を他のコンポーネントと共に使用して便利なエンド ツー エンドのアプリケーションを実現する方法を学習します。 自分でアプリ全体を実行してもかまいません。また、設定の Custom Vision 部分のみを完成させて、アプリでこの部分がどのように使用されているかを調べることもできます。

ここでは、次の操作方法について説明します。

> [!div class="checklist"]
> - Azure サービスのロゴを認識するカスタム オブジェクト検出器を作成する
> - アプリを Azure Computer Vision と Custom Vision に接続する
> - アプリから Azure サービスをデプロイするための Azure サービス プリンシパル アカウントを作成する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Visual Studio 向けの Xamarin ワークロード ([Xamarin のインストール](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows)に関するページを参照してください)
- Visual Studio 向けの iOS または Android のエミュレーター
- [Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (オプション)

## <a name="get-the-source-code"></a>ソース コードを入手する

提供されている Web アプリを使用する場合は、GitHub 上の [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) リポジトリからアプリのソース コードを複製またはダウンロードします。 Visual Studio で *Source/VisualProvision.sln* ファイルを開きます。 後で、アプリを実行できるようにするために、いくつかのプロジェクト ファイルに必要な編集を加えます。

## <a name="create-an-object-detector"></a>オブジェクト検出器を作成する

[Custom Vision Web サイト](https://customvision.ai/)にサインインし、新しいプロジェクトを作成します。 オブジェクト検出プロジェクトを指定し、Logo ドメインを使用します。これにより、このサービスにおいてロゴ検出用に最適化されたアルゴリズムが使用されます。 

![Chrome ブラウザーに表示された Custom Vision Web サイトの新しいプロジェクト ダイアログ ウィンドウ](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

次に、Azure サービスのロゴの画像をアップロードして、手動でタグ付けすることで、ロゴ検出アルゴリズムをトレーニングする必要があります。 AIVisualProvision リポジトリには、開発者が使用できるトレーニング画像のセットが含まれています。 Web サイトで **[Training Images]\(トレーニング画像\)** タブの **[Add images]\(画像の追加\)** を選択し、リポジトリの **Documents/Images/Training_DataSet** フォルダーに移動します。 各画像内のロゴに手動でタグを付ける必要があるため、このプロジェクトをテストするだけの場合は画像のサブセットのみをアップロードしてもかまいません。 少なくとも、使用する予定の各タグのインスタンスを 15 個アップロードする必要があります。

トレーニング画像をアップロードしたら、ディスプレイ上の最初の 1 つを選択します。 これにより、タグ付けウィンドウが表示されます。 各画像内の各ロゴに対して四角形を描画し、タグを割り当てます。 

![Custom Vision Web サイト上でロゴの画像にタグが適用されている](media/azure-logo-tutorial/tag-logos.png)

このアプリは、特定のタグ文字列で動作するように構成されています。*Source\VisualProvision\Services\Recognition\RecognitionService.cs* ファイル内の定義を参照してください。

[!code-csharp[tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

1 つの画像にタグを付けたら、右に移動して次の画像にタグを付けます。 完了したら、タグ付けウィンドウを閉じます。

## <a name="train-the-object-detector"></a>オブジェクト検出器をトレーニングする

左側のウィンドウで、**[Tags]\(タグ\)** スイッチを **[Tagged]\(タグ付き\)** に設定します。すべての画像が表示されます。 次に、モデルをトレーニングするために、ページ上部の緑色のボタンをクリックします。 これで、新しい画像内の同じタグを認識するようにアルゴリズムに指示します。 さらに、既存の画像のいくつかでモデルがテストされ、精度スコアが生成されます。

![Custom Vision Web サイト上の [Training Images]\(トレーニング画像\) タブ。トレーニング ボタンが枠で囲まれている](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>予測 URL を取得する

モデルのトレーニングが完了したら、それをアプリに統合する準備が整いました。 これを行うには、エンドポイント URL (アプリが照会するモデルのアドレス) と (アプリに予測要求へのアクセスを許可するための) 予測キーを取得する必要があります。 **[Performance]\(パフォーマンス\)** タブで、ページの上部にある **[Prediction URL]\(予測 URL\)** をクリックします。

![URL アドレスと API キーが表示されている Custom Vision Web サイトの予測 API 画面](media/azure-logo-tutorial/cusvis-endpoint.png)

画像ファイルの URL と `Prediction-key` の値を *Source\VisualProvision\AppSettings.cs* ファイル内の適切なフィールドにコピーします。

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Custom Vision の使用方法を調べる

*Source/VisualProvision/Services/Recognition/CustomVisionService.cs* ファイルを開き、Custom Vision キーとエンドポイント URL がアプリでどのように使用されているかを確認します。 **PredictImageContentsAsync** メソッドは、画像ファイルのバイト ストリームを (非同期タスク管理用の) キャンセル トークンと共に受け取ると、Custom Vision の予測 API を呼び出して、その予測の結果を返します。 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

この結果は **PredictionResult** インスタンスの形式になっていて、これ自体に **Prediction** インスタンスのリストが含まれます。 **Prediction** には、検出されたタグと、その画像内の境界ボックスの位置が含まれます。

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

アプリがこのデータを処理する方法についてさらに知りたい場合は、*Source/VisualProvision/Services/Recognition/RecognitionService.cs* ファイルに定義されている **GetResourcesAsync** メソッドから始めてください。 

## <a name="add-computer-vision"></a>Computer Vision を追加する

チュートリアルの Custom Vision 部分は完成しました。しかし、このアプリを実行するには、Computer Vision サービスを統合する必要もあります。 このアプリでは、ロゴの検出プロセスを補完するために Computer Vision のテキスト認識機能を使用します。Azure ロゴは、その外観 "_または_" その近くに印刷されたテキストによって認識できます。 Computer Vision は、Custom Vision モデルとは異なり、画像やビデオに対して特定の操作を実行するように事前にトレーニングされています。

Computer Vision サービスにサブスクライブするだけで、キーとエンドポイント URL を取得できます。 この手順に関してサポートが必要な場合は、「[サブスクリプション キーを取得する方法](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe)」を参照してください。

![[クイック スタート] メニューが選択された、Azure portal の Computer Vision サービス。API エンドポイント URL とキーへのリンクが強調表示されている](media/azure-logo-tutorial/comvis-keys.png)

次に、*Source\VisualProvision\AppSettings.cs* ファイルを開き、`ComputerVisionEndpoint` および `ComputerVisionKey` 変数に適切な値を入力します。

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

このアプリでは、Azure サブスクリプションにサービスをデプロイするために、Azure サービス プリンシパル アカウントが必要です。 サービス プリンシパルを使用すると、ロールベースのアクセス制御を使用して特定のアクセス許可をアプリに委任できます。 詳細については、[サービス プリンシパルのガイド](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals)を参照してください。

サービス プリンシパルは、Azure Cloud Shell または (次のように) Azure CLI を使用して作成できます。 最初に、ログインし、使用するサブスクリプションを選択します。

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

次に、サービス プリンシパルを作成します (完了するまでにしばらく時間がかかることがありますのでご注意ください)。

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

正常に完了すると、必要な資格情報を含む次の JSON 出力が表示されます。

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
`clientId` と `tenantId` の値をメモします。 これらを *Source\VisualProvision\AppSettings.cs* ファイルの適切なフィールドに追加します。

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>アプリの実行

これで、以下に対するアクセス権がアプリに付与されました。
* トレーニング済みの Custom Vision モデル
* Computer Vision サービス
* サービス プリンシパル アカウント 

アプリを実行するには、次の手順に従います。

1. Visual Studio のソリューション エクスプローラーで、VisualProvision.Android または VisualProvision.iOS プロジェクトを選択し、メイン ツール バーのドロップダウン メニューから対応するエミュレーターまたは接続されているモバイル デバイスを選択します (iOS エミュレーターを実行するには MacOS デバイスが必要であることにご注意ください)。 その後、アプリを実行します。

1. 読み込まれた最初の画面で、サービス プリンシパル クライアント ID、テナント ID、パスワードを入力します。 **[Login]** をクリックします。

    > [!NOTE]
    > 一部のエミュレーターでは、この手順で **[Login]** ボタンがアクティブにならない場合があります。 その場合はアプリを停止し、_Source/VisualProvision/Pages/LoginPage.xaml_ ファイルを開いて LOGIN BUTTON というラベルの付いた `Button` 要素を見つけて、次の行を削除します。
      ```xaml
      IsEnabled="{Binding IsValid}"
      ```
    
    > その後、アプリをもう一度実行します。

    ![サービス プリンシパルの資格情報のフィールドがあるアプリの画面](media/azure-logo-tutorial/app-credentials.png)

1. 次の画面で、ドロップダウン メニューから対象の Azure サブスクリプションを選択します (これには、サービス プリンシパルでアクセスできるすべてのサブスクリプションが含まれている必要があります)。 **[Continue]** をクリックします。

    ![ターゲットの Azure サブスクリプションに関するドロップダウン フィールドがあるアプリの画面](media/azure-logo-tutorial/app-az-subscription.png)

    この時点で、デバイスによるカメラと写真ストレージへのアクセスを許可するようにアプリから要求される場合があります。 これらのアクセス許可を承諾します。

1. 次に、デバイスのカメラがアクティブになります。 トレーニングしたいずれかの Azure サービスのロゴの写真を撮影します。 デプロイ ダイアログで、(Azure portal からデプロイする場合と同様に) 新しいサービスのリージョンとリソース グループを選択するように求められます。 

    ![Azure ロゴが含まれている 2 枚の紙片が撮影されたスマートフォンのカメラ画面](media/azure-logo-tutorial/app-camera-capture.png)

    ![デプロイ リージョンとリソース グループの入力用フィールドを含むアプリの画面](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

このシナリオのすべての手順を実行し、アプリを使用して Azure サービスを自分のアカウントにデプロイした場合は、必ず作業の完了後に [Azure portal](https://ms.portal.azure.com/) に移動し、今後使用しないサービスをキャンセルしてください。

また、今後 Custom Vision を使用して独自のオブジェクト検出プロジェクトを作成する予定がある場合は、このチュートリアルで作成したロゴ検出プロジェクトを削除することをお勧めします。 Custom Vision の無料試用版では、2 つのプロジェクトを利用できます。 [Custom Vision Web サイト](https://customvision.ai)で、**[Projects]\(プロジェクト\)** に移動し、**[My New Project]\(自分用の新しいプロジェクト\)** のごみ箱アイコンを選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Custom Vision サービスを使用してモバイル カメラの画像内のロゴを検出する、フル機能の Xamarin.Forms アプリを設定し、詳しく調べました。 次に、自分のアプリ用に作成する Custom Vision モデルを強力かつ正確なモデルにできるように、Custom Vision モデルを構築するためのベスト プラクティスを学習してください。

> [!div class="nextstepaction"]
> [分類子を改善する方法](getting-started-improving-your-classifier.md)