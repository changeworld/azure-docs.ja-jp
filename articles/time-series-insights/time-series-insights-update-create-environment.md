---
title: Azure Time Series Insights (プレビュー) のチュートリアル| Microsoft Docs
description: Azure Time Series Insights (プレビュー) の詳細
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872307"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Azure Time Series Insights (プレビュー) のチュートリアル

このチュートリアルでは、シミュレートされたデバイスのデータが入力される Azure Time Series Insights (TSI) プレビュー環境を作成するプロセスについて説明します。 このチュートリアルでは、以下の内容を学習します。

* TSI (プレビュー) 環境を作成する。
* TSI (プレビュー) 環境をイベント ハブに接続する。
* 風力発電地帯のシミュレーションを実行して、TSI プレビュー環境にデータをストリーミングする。
* データの基本的な分析を実行する。
* 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

## <a name="create-a-time-series-insights-preview-environment"></a>Time Series Insights (プレビュー) 環境を作成する

このセクションでは、[Azure Portal](https://portal.azure.com/) を使用して Azure TSI (プレビュー) 環境を作成する方法について説明します。

1. お使いのサブスクリプション アカウントを使用して、Azure Portal にサインインします。
1. 左上隅の **[+ リソースの作成]** を選択します。
1. **[モノのインターネット (IoT)]** カテゴリを選択し、**[Time Series Insights]** を選択します。

  ![チュートリアル 1][1]

1. [Time Series Insights 環境] ページで、必要なパラメーターを入力し、**[次へ: イベント ソース]** をクリックします。

  ![チュートリアル 2][2]

1.  **[イベント ソース]** ページで、必要なパラメーターを入力し、**[確認および作成]** をクリックします。

  ![チュートリアル 3][3]

1. すべての詳細を確認し、**[作成]** をクリックして、環境のプロビジョニングを開始します。

  ![チュートリアル 4][4]

1. デプロイが正常に完了したら、通知を受け取ります。

  ![チュートリアル 5][5]

## <a name="send-events-to-your-tsi-environment"></a>TSI 環境にイベントを送信する

このセクションでは、風力発電デバイス シミュレーターを使用して、イベント ハブ経由で TSI 環境にイベントを送信します。

  1. Azure Portal で、お使いのイベント ハブ リソースに移動し、それを TSI 環境に接続します。 [リソースを既存のイベント ハブに接続する方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)を確認してください。

  1. [イベント ハブ リソース] ページで **[共有アクセス ポリシー]**、**[RootManageSharedAccessKey]** の順に移動します。 表示された **[接続文字列 -- 主キー]**  をコピーします。

      ![チュートリアル 6][6]

  1. [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html) に移動します。 この Web アプリでは、風力発電デバイスをシミュレートします。
  1. 手順 3 でコピーした接続文字列を **[イベント ハブ接続文字列]** に貼り付けます。

      ![チュートリアル 7][7]

  1. **[Click to Start]\(クリックして開始\)** をクリックして、イベントのイベント ハブへのプッシュを開始します。 この段階で、`instances.json` という名前のファイルがお使いのコンピューターにダウンロードされます。 後で必要になるので、このファイルを保存しておきます。

  1. お使いのイベント ハブに戻ります。 ハブによって受信された新しいイベントが表示されています。

     ![チュートリアル 8][8]

## <a name="analyze-data-in-your-environment"></a>環境内でデータを分析する

このセクションでは、Time Series Insights 更新エクスプローラーを使用して、時系列データの基本的な分析を実行します。

  1. Azure Portal のリソース ページで URL をクリックして、Time Series Insights 更新エクスプローラーに移動します。

      ![チュートリアル 9][9]

  1. エクスプローラーで、**[Unparented Instances]\(親のないインスタンス\)** ノードをクリックして、環境内のすべての時系列インスタンスを表示します。

     ![チュートリアル 10][10]

  1. このチュートリアルでは、前日に送信されたデータを分析します。 これを行うには、**[クイック タイム]** を選択し、**[過去 24 時間]** オプションをクリックします。

     ![チュートリアル 11][11]

  1. **[Sensor_0]** を選択し、**[Show Avg Value]\(平均値の表示\)** を選択して、この時系列インスタンスから送信されるデータを視覚化します。

     ![チュートリアル 12][12]

  1. 同様に、他の時系列インスタンスのデータをプロットして、基本的な分析を実行できます。

     ![チュートリアル 13][13]

## <a name="define-a-type--hierarchy"></a>種類と階層を定義する 

このセクションでは、種類と階層を作成し、それらを時系列インスタンスに関連付けます。 [時系列モデル](./time-series-insights-update-tsm.md)の詳細を確認してください。

  1. エクスプローラーで、アプリ バーの **[モデル]** タブをクリックします。

     ![チュートリアル 14][14]

  1. [種類] セクションで、**[+ 追加]** をクリックします。 これにより、新しい時系列モデルの種類を作成できます。

     ![チュートリアル 15][15]

  1. 種類エディターで、次の図に示すように **[名前]** と **[説明]** を入力し、**[平均]**、**[最小]**、および **[最大]** の各値用の変数を入力します。 **[作成]** をクリックして種類を保存します。

     ![チュートリアル 16][16]

     ![チュートリアル 17][17]

  1. **[階層]** セクションで、**[+ 追加]** をクリックします。 これにより、新しい時系列モデルの階層を作成できます。

     ![チュートリアル 18][18]

  1. 階層エディターで、**[名前]** を入力し、次に示すように階層レベルを追加します。 **[作成]** をクリックして階層を保存します。

     ![チュートリアル 19][19]

     ![チュートリアル 20][20]

  1. **[インスタンス]** セクションで、インスタンスを選択し、**[編集]** をクリックします。 これにより、このインスタンスに種類と階層を関連付けることができます。

     ![チュートリアル 21][21]

  1. インスタンス エディターで、手順 3 と 手順 5 で定義した種類と階層を選択します。

     ![チュートリアル 22][22]

  1. または、これをすべてのインスタンスに対して同時に実行するには、ダウンロード済みの `instances.json` ファイルを編集できます。 このファイルで、すべての **typeId** フィールドと **hierarchyId** フィールドを、手順 3 と 手順 5 で取得した ID に置き換えます。

  1. **[インスタンス]** セクションで、**[JSON のアップロード]** をクリックし、次に示すように編集された `instances.json` ファイルをアップロードします。

     ![チュートリアル 23][23]

  1. **[分析]** タブに移動し、ブラウザーを更新します。 上で定義した種類と階層に関連付けられているすべてのインスタンスが表示されます。

     ![チュートリアル 24][24]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。  

* TSI (プレビュー) 環境を作成する。
* TSI (プレビュー) 環境をイベント ハブに接続する。
* 風力発電地帯のシミュレーションを実行して、TSI (プレビュー) 環境にデータをストリーミングする。
* データの基本的な分析を実行する。
* 時系列モデルの種類と階層を定義し、それらをインスタンスに関連付ける。

これで、ご自身の TSI 更新環境を作成する方法を理解できたので、TSI の主要な概念の詳細を確認してください。

TSI のストレージ構成を確認する。

> [!div class="nextstepaction"]
> [Azure TSI (プレビュー) のストレージとイングレス](./time-series-insights-update-storage-ingress.md)

時系列モデルの詳細を確認する。

> [!div class="nextstepaction"]
> [Azure TSI (プレビュー) のデータ モデリング](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png