---
title: Azure Time Series Insights プレビューのセットアップ - Azure Time Series Insights プレビューの環境設定のチュートリアル | Microsoft Docs
description: Azure Time Series Insights プレビューで環境を設定する方法について説明します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322620"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>チュートリアル:Azure Time Series Insights プレビューの環境を設定する

このチュートリアルでは、Azure Time Series Insights の従量課金制 (PAYG) プレビュー環境を作成する手順について説明します。 このチュートリアルでは、以下の内容を学習します。

* Azure Time Series Insights プレビューの環境を作成する。
* Azure Time Series Insights プレビュー環境を Azure Event Hubs のイベント ハブに接続する。
* 風力発電ファームのシミュレーションを実行して、Azure Time Series Insights プレビュー環境にデータをストリーミングする。
* データの基本的な分析を実行する。
* 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

# <a name="create-a-device-simulation"></a>デバイス シミュレーションを作成する

このセクションでは、IoT ハブにデータを送信する 3 つのシミュレートされたデバイスを作成します。

1. [Azure IoT ソリューション アクセラレータのホーム ページ](https://www.azureiotsolutions.com/Accelerators)に移動します。 Azure IoT ソリューション アクセラレータのホーム ページには、作成済みの例がいくつか表示されます。 Azure アカウントを使用してサインインします。 次に、**[Device Simulation]** を選択します。

   ![Azure IoT ソリューション アクセラレータのホーム ページ][1]

   最後に、**[今すぐ試してみる]** をクリックします。

1. **[デバイス シミュレーション ソリューションの作成]** ページで、次の必須パラメーターに入力します。

   | パラメーター | 説明 |
   | --- | --- |
   | ソリューション名 |    新しいリソース グループの作成に使用される一意の値。 一覧の Azure リソースが | 作成され、リソース グループに割り当てられます。 |
   | サブスクリプション | TSI 環境の作成に使用したものと同じサブスクリプションを指定します |
   | リージョン |   TSI の作成に使用したものと同じリージョンを指定します。 |
   | オプションの Azure リソースをデプロイする    | [IoT Hub] はオンのままにします。シミュレートされたデバイスはこれを使用してデータに接続してストリーミングします。 |

   必要なパラメーターを入力した後、**[ソリューションの作成]** をクリックします。 ソリューションがデプロイされるまで約 10 ～ 15 分待ちます。

   ![デバイス シミュレーション ソリューションを作成する][2]

1. **ソリューション アクセラレータ ダッシュボード**で、**[起動]** ボタンをクリックします。

   ![デバイス シミュレーション ソリューションを起動する][3]

1. **Microsoft Azure IoT デバイス シミュレーション**のページにリダイレクトされます。 画面の右上にある **[+ New simulation]\(+ 新しいシミュレーション\)** をクリックします。

   ![Azure IoT のシミュレーション ページ][4]

1.  次のように、必要なパラメーターを入力します。

    ![入力するパラメーター][5]

    |||
    | --- | --- |
    | **名前** | シミュレーターの一意名を入力します |
    | **説明** | 定義を入力します |
    | **Simulation Duration (シミュレーション期間)** |  `Run indefinitely` |
    | **Device Model (デバイス モデル)** | **名前**:「`Chiller`」と入力します **Amount (量)**:「`3`」と入力します |
    | **[Target IoT Hub]\(IoT Hub をターゲットにする\)** |  `Use pre-provisioned IoT Hub` |

    必要なパラメーター値を入力した後、**[Start Simulation]\(シミュレーションの開始\)** をクリックします。

1. デバイス シミュレーション ダッシュボードで、**[Active devices]\(アクティブなデバイス\)** と **[Messages per second]\(1 秒あたりのメッセージ数\)** を確認します。

    ![Azure IoT シミュレーション ダッシュボード][6]

## <a name="list-device-simulation-properties"></a>デバイス シミュレーションのプロパティを一覧表示する

Azure Time Series Insights 環境を作成する前に、お使いの IoT ハブ、サブスクリプション、リソース グループの名前が必要です。

1. **ソリューション アクセラレータ ダッシュボード**に移動し、同じ Azure サブスクリプション アカウントを使用してサインインします。 前の手順で作成したデバイス シミュレーションを探します。

1. そのデバイス シミュレーターをクリックして、**[起動]** をクリックします。 右側に表示される **[Azure Management Portal]\(Azure の管理ポータル\)** リンクをクリックします。

    ![シミュレーターの一覧][7]

1. IoT ハブ、サブスクリプション、リソース グループの名前を書き留めます。

    ![Azure ポータル][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Time Series Insights プレビューの PAYG 環境を作成する

このセクションでは、[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights プレビューの環境を作成する方法について説明します。

1. 自分のサブスクリプション アカウントを使用して、Azure portal にサインインします。

1. **[リソースの作成]** を選択します。

1. **[モノのインターネット (IoT)]** カテゴリを選択し、**[Time Series Insights]** を選択します。

   ![[リソースの作成]、[モノのインターネット (IoT)]、[Time Series Insights] を選択する][9]

1. ページのフィールドに次のように入力します。

   | | |
   | --- | ---|
   | **環境名** | Azure Time Series Insights プレビュー環境の一意名を選択します。 |
   | **サブスクリプション** | Azure Time Series Insights プレビュー環境を作成するサブスクリプションを入力します。 デバイス シミュレーターによって作成される他の IoT リソースと同じサブスクリプションを使用することをお勧めします。 |
   | **リソース グループ** | リソース グループとは、Azure リソース用のコンテナーです。 Azure Time Series Insights プレビュー環境リソースに既存のリソース グループを選択するか、新しいリソース グループを作成します。 デバイス シミュレーターによって作成される他の IoT リソースと同じリソース グループを使用することをお勧めします。 |
   | **場所** | Azure Time Series Insights プレビュー環境のデータ センター リージョンを選択します。 帯域幅のコストや待機時間の増加を防ぐために、Azure Time Series Insights プレビュー環境を IoT リソースと同じリージョンにすることをお勧めします。 |
   | **レベル** |  従量課金制を意味する [`PAYG`] を選択します。 これは、Azure Time Series Insights プレビュー製品の SKU です。 |
   | **プロパティ ID** | 自分の時系列を一意に示します。 このフィールドは変更不可であり、後で変更できないことに注意してください。 このチュートリアルでは、フィールドを「`iothub-connection-device-id`」に設定します。 タイム シリーズ ID について詳しくは、「[How to choose a Time Series ID](./time-series-insights-update-how-to-id.md)」(タイム シリーズ ID の選択方法) をご覧ください。 |
   | **ストレージ アカウント名** | 作成する新しいストレージ アカウントのグローバルな一意名を入力します。 |

   以上のフィールドを入力した後、**[Next:Event Source]\(次へ: イベント ソース\)** をクリックします。

   ![クリック: 次へ:イベント ソース][10]

1. ページでフィールドに次のように入力します。

   | | |
   | --- | --- |
   | **Create an Event Source? (イベント ソースを作成しますか)** | [`Yes`] を選択します|
   | **名前** | イベント ソースの名前に使用される一意の値が必要です。|
   | **ソースの種類** | [`IoT Hub`] を選択します |
   | **Select a Hub? (ハブを選択しますか)** | [`Select Existing`] を選択します |
   | **サブスクリプション** | デバイス シミュレーターに使用したサブスクリプションを入力します。 |
   | **IoT Hub name (IoT ハブの名前)** | デバイス シミュレーター用に作成した IoT ハブの名前を入力します。 |
   | **IoT Hub access policy (IoT ハブのアクセス ポリシー)** | [`iothubowner`] を選択します |
   | **Iot Hub consumer group (IoT Hub コンシューマー グループ)** | Azure Time Series Insights プレビュー用の一意のコンシューマー グループが必要です。 |
   | **Timestamp** | このフィールドは、受信したテレメトリ データでタイムスタンプ プロパティを識別するために使用されます。 このチュートリアルでは、フィールドに入力しないでください。 このシミュレーターでは、Time Series Insights の既定のタイムスタンプを IoT ハブからの受信に使用します。|

   一意のコンシューマー グループを作成するには:

   1. **[Iot Hub consumer group]\(IoT Hub コンシューマー グループ\)** フィールドの横にある **[新規]** をクリックします。

      ![クリック: 次へ:イベント ソース][11]

   1. コンシューマー グループに一意の名前を指定して、**[追加]** をクリックします。

      ![[追加] をクリックします。][12]

   上記のフィールドを指定した後、**[確認と作成]** をクリックします。

      ![[Review and create] (確認および作成)][13]

1. レビュー ページですべてのフィールドを確認し、**[作成]** をクリックします。

   ![Create][14]

1. デプロイの状態を確認できます。

   ![デプロイ完了][15]

1. テナントを所有している場合、自分の時系列環境へのアクセスを受け取る必要があります。 アクセスがあることを確認するには:

   * 新しく作成した Azure Time Series Insights プレビュー環境に移動します。 リソース グループを検索することによって行うことができます。 その後、自分の時系列環境をクリックします。

      ![デプロイ完了][16]

   * Azure Time Series Insights プレビュー ページで、**[Data Access Policies]\(データ アクセス ポリシー\)** に移動します。

     ![データ アクセス ポリシー][17]

   * 自分の資格情報が表示されることを確認します。

     ![資格情報を確認する][18]

   自分の資格情報が表示されない場合は、環境にアクセスするためのアクセス許可を自分自身に付与する必要があります。 アクセス許可の設定について詳しくは、「[データ アクセスの許可](./time-series-insights-data-access.md)」をご覧ください。

## <a name="analyze-data-in-your-environment"></a>環境内でデータを分析する

このセクションでは、[Azure Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)を使用して、時系列データの基本的な分析を実行します。

1. [Azure portal](https://portal.azure.com/) のリソース ページで URL をクリックして、Azure Time Series Insights プレビュー エクスプローラーに移動します。

   ![Time Series Insights エクスプローラーの URL][19]

1. エクスプローラーで、**[Unparented Instances]\(親のないインスタンス\)** ノードを選択して、環境内のすべての Azure Time Series Insights プレビューを表示します。

   ![親のないインスタンスの一覧][20]

1. 表示された時系列で、最初のインスタンスをクリックします。 次に、**[Show Avg pressure]** をクリックします。

   ![平均圧力を表示する][21]

1. 時系列グラフが右側に表示されます。

   ![時系列グラフ][22]

1. 他の 2 つの時系列で**手順 3** を繰り返します。 次に示すように、すべての時系列を表示できます。

   ![すべての時系列グラフ][23]

1. **時間範囲**を変更して、過去 1 時間の時系列の傾向を確認します。 次に示すように、**[From]\(開始\)** オプション ボックスを選択します。

   ![[From]\(開始\) オプションを選択する][24]

1. **[From]\(開始\)** オプション ボックスで、過去 1 時間のイベントが表示されるように時刻を変更します。

   ![[From]\(開始\) オプションを選択する][25]

1. 3 つのデバイスすべての過去 1 時間の圧力を比較できます。

   ![[From]\(開始\) オプションを選択する][26]

## <a name="define-and-apply-a-model"></a>モデルを定義して適用する

このセクションでは、モデルを適用してデータを構造化します。 モデルを完成するには、タイプ、階層、およびインスタンスを定義します。 データ モデリングについて詳しくは、「[Time Series Models](./time-series-insights-update-tsm.md)」(時系列モデル) をご覧ください。

1. エクスプローラーで、**[Model]\(モデル\)** タブを選択します。

   ![[Model]\(モデル\) タブを選択する][27]

1. 次に、**[+ Add]\(+ 追加\)** をクリックしてタイプを追加します。 右側にタイプ エディターが開きます。

   ![[追加] をクリックします。][28]

1. 次に、3 つの変数を定義します。気圧、温度、湿度のタイプです。 以下のフィールドを入力します。

   | | |
   | --- | ---|
   | **名前** | 「`Chiller`」と入力します |
   | **説明** | 「`This is a type definition of Chiller`」と入力します |

   * 次に、3 つの変数で "圧力" を定義します。

      | | |
      | --- | ---|
      | **名前** | 「`Avg Pressure`」と入力します |
      | **値** | **[pressure (Double)]\(圧力 (Double)\)** を選択します。 Azure Time Series Insights でイベントの受信が開始されてからこのフィールドが設定されるまで、数分かかる場合があることに注意してください |
      | **Aggregation Operation (集計操作)** | `AVG` を選択します |

      ![変数を追加する][29]

      **[+Variable]\(+ 変数\)** をクリックして、次の変数を追加します。

   * 次に、"温度" を定義します。

      | | |
      | --- | ---|
      | **名前** | 「`Avg Temperature`」と入力します |
      | **値** | **[temperature (Double)]\(温度 (Double)\)** を選択します。 Azure Time Series Insights でイベントの受信が開始されてからこのフィールドが設定されるまで、数分かかる場合があることに注意してください |
      | **Aggregation Operation (集計操作)** | `AVG` を選択します|

      !["温度" を定義する][30]

   * 次に、"湿度" を定義します。

      | | |
      | --- | ---|
      | **名前** | 「`Max Humidity`」と入力します |
      | **値** | **[humidity (Double)]\(湿度 (Double)\)** を選択します。 Azure Time Series Insights でイベントの受信が開始されてからこのフィールドが設定されるまで、数分かかる場合があることに注意してください |
      | **Aggregation Operation (集計操作)** | `MAX` を選択します|

      !["温度" を定義する][31]

   変数を定義した後、**[Create]\(作成\)** をクリックします。

1. 追加したタイプが表示されます。

   ![追加したタイプを表示する][32]

1. 次の手順では、階層を追加します。 **[Hierarchies]\(階層\)** セクションで **[+ Add]\(+ 追加\)** を選択して新しい階層を作成します。

   ![階層を追加する][33]

1. 階層を定義します。 次のようにフィールドに入力します。

   | | |
   | --- | ---|
   | **名前** | 「`Location Hierarchy`」と入力します |
   | **Level 1 (レベル 1)** | 「`Country`」と入力します |
   | **Level 2 (レベル 2)** | 「`City`」と入力します |
   | **Level 3 (レベル 3)** | 「`Building`」と入力します |

   以上のフィールドを入力した後、**[Create]\(作成\)** をクリックします。

   ![階層を定義する][34]

1. 作成された階層を確認できます。

   ![階層を表示する][35]

1. 階層を定義した後、左側の **[Instances]\(インスタンス\)** をクリックします。 インスタンスが表示されたら、最初のインスタンスをクリックして、**[Edit]\(編集\)** を選択します。

   ![インスタンスを編集する][36]

1. 右側にテキスト エディターが表示されます。 次のフィールドを追加します。

   | | |
   | --- | --- |
   | **種類** | `Chiller` を選択します |
   | **説明** | 「`Instance for Chiller-01.1`」と入力します |
   | **Hierarchies (階層)** | [`Location Hierarchy`] を有効にします |
   | **Country (国)** | 「`USA`」と入力します |
   | **City (市)** | 「`Seattle`」と入力します |
   | **Building (建物)** | 「`Space Needle`」と入力します |

    以上のフィールドを入力した後、**[Save]\(保存\)** をクリックします。

   ![冷却装置を保存する][37]

1. 他のセンサーについて前の手順を繰り返します。 以下のフィールドを使用します。

   * Chiller 01.2 の場合:

     | | |
     | --- | --- |
     | **種類** | `Chiller` を選択します |
     | **説明** | 「`Instance for Chiller-01.2`」と入力します |
     | **Hierarchies (階層)** | [`Location Hierarchy`] を有効にします |
     | **Country (国)** | 「`USA`」と入力します |
     | **City (市)** | 「`Seattle`」と入力します |
     | **Building (建物)** | 「`Pacific Science Center`」と入力します |

   * Chiller 01.3 の場合:

     | | |
     | --- | --- |
     | **種類** | `Chiller` を選択します |
     | **説明** | 「`Instance for Chiller-01.1`」と入力します |
     | **Hierarchies (階層)** | [`Location Hierarchy`] を有効にします |
     | **Country (国)** | 「`USA`」と入力します |
     | **City (市)** | 「`New York`」と入力します |
     | **Building (建物)** | 「`Empire State Building`」と入力します |

1. **[Analyze]\(分析\)** タブに移動して、ページを更新します。 すべての階層レベルを展開して、時系列を検索します。

   ![分析タブを表示する][38]

1. 過去 1 時間の時系列を調べるには、**[Quick Times]\(クイック タイム\)** を過去 1 時間に設定します。

   ![過去 1 時間を調べる][39]

1. **Pacific Science Center** の時系列をクリックし、**[Show Max Humidity]\(最大湿度の表示\)** をクリックします。

   ![最大湿度を表示する][40]

1. **最大湿度**の時系列が 1 分の間隔サイズで開きます。 領域をクリックして、範囲をフィルター処理します。 次に、右クリックして拡大し、時間枠のイベントを分析します。

   ![表示、フィルター、拡大][41]

   ![表示、フィルター、拡大][42]

1. 領域をクリックしてから右クリックして、イベントの詳細を表示することもできます。

   ![表示、フィルター、拡大][43]

   ![表示、フィルター、拡大][44]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。  

* デバイス シミュレーション アクセラレータを作成して使用する。
* Azure Time Series Insights プレビューの PAYG 環境を作成する。
* Azure Time Series Insights プレビュー環境をイベント ハブに接続する。
* 風力発電ファームのシミュレーションを実行して、Azure Time Series Insights プレビュー環境にデータをストリーミングする。
* データの基本的な分析を実行する。
* 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

Azure Time Series Insights プレビュー環境を自分で作成する方法がわかったので、Azure Time Series Insights の主要な概念について詳しく学習してください。

Azure Time Series Insights のストレージ構成について読む。

> [!div class="nextstepaction"]
> [Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)

時系列モデルの詳細を確認する。

> [!div class="nextstepaction"]
> [Azure Time Series Insights プレビューのデータ モデリング](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png