---
title: "Azure IoT Hub から取得したリアルタイム センサー データの視覚化 – Web Apps | Microsoft Docs"
description: "Azure Web Apps を使用して、センサーから収集されて Azure IoT ハブに送信された気温と湿度のデータを視覚化します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "リアルタイム データの視覚化, ライブ データの視覚化, センサー データの視覚化"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: f3bb01da7764e467963a47d3d5485679411c9167
ms.lasthandoff: 04/25/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>Azure Web Apps を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学習内容

このレッスンでは、Azure Web アプリでホストされた Web アプリケーションを実行して、Azure IoT ハブに届いたリアルタイム センサー データを視覚化する方法について説明します。 Power BI を使用して IoT ハブのデータを視覚化したい場合は、[Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する方法](iot-hub-live-data-visualization-in-power-bi.md)に関するページを参照してください。

## <a name="what-you-do"></a>作業内容

- Azure Portal で Azure Web アプリを作成する。
- コンシューマー グループを追加して、データにアクセスできるよう IoT ハブを準備する。
- IoT ハブからセンサー データを読み取る Web アプリを構成する。
- Web アプリでホストされる Web アプリケーションをアップロードする。
- Web アプリを開いて、IoT ハブから取得したリアルタイムの温度と湿度のデータを確認する。

## <a name="what-you-need"></a>必要なもの

- 次の要件に対応するために、[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)に関するチュートリアルを完了しておきます。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT Hub。
  - Azure IoT ハブにメッセージを送信するクライアント アプリケーション。
- Git ([Git のダウンロード](https://www.git-scm.com/downloads))。

## <a name="create-an-azure-web-app"></a>Azure Web アプリの作成

1. [Azure Portal](https://ms.portal.azure.com/) で、**[新規]** > **[Web + モバイル]** > **[Web アプリ]** の順にクリックします。
1. 一意のジョブ名を入力してサブスクリプションを確認し、リソース グループと場所を指定します。次に、**[ダッシュボードにピン留めする]** をオンにし、**[作成]** をクリックします。

   リソース グループと同じ場所を選択することをお勧めします。 そうすることで、データ転送の処理を高速化してコストを抑えることができます。

   ![Azure Web アプリの作成](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>IoT ハブからデータを読み取る Web アプリの構成

1. プロビジョニングしたばかりの Web アプリを開きます。
1. **[アプリケーション設定]** をクリックし、**[アプリの設定]** で次のキー/値のペアを追加します。

   | キー                                   | 値                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | iothub-explorer から取得                                |
   | Azure.IoT.IoTHub.DeviceId             | iothub-explorer から取得                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | IoT ハブに追加するコンシューマー グループの名前  |

   ![キー/値のペアを使用して Azure Web アプリに設定を追加する](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Web アプリでホストされる Web アプリケーションのアップロード

GitHub には、IoT ハブからのリアルタイム センサー データを表示するための Web アプリケーションが Microsoft によって用意されています。 必要な作業は、Git リポジトリを使用するよう Web アプリを構成し、GitHub から Web アプリケーションをダウンロードしてから、Web アプリでホストされるよう Azure にそれをアップロードすることだけです。

1. Web アプリで、**[デプロイ オプション]** > **[ソースの選択]** > **[ローカル Git リポジトリ]** の順にクリックします。

   ![ローカル Git リポジトリを使用するよう Azure Web アプリのデプロイを構成する](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. **[接続のセットアップ]** をクリックし、Git リポジトリに接続する際に Azure で使用されるユーザー名とパスワードを作成し、**[OK]** をクリックします。

   ![Web アプリに関して Azure で Git リポジトリのユーザー名とパスワードを設定する](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. **[OK]** をクリックして構成を終了します。
1. **[概要]** をクリックし、**[Git クローン URL]** の値を書き留めます。

   ![Azure Web アプリの Git クローン URL の取得](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. ローカル コンピューターでコマンド ウィンドウまたはターミナル ウィンドウを開きます。
1. GitHub から Web アプリケーションをダウンロードし、それが Web アプリでホストされるよう Azure にアップロードします。 これを行うには、次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > \<Git clone URL\> は、Web アプリの **[概要]** ページにあった Git リポジトリの URL になります。

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Web アプリを開いて IoT ハブから取得したリアルタイムの温度と湿度のデータを確認する

Web アプリの **[概要]** ページで、URL をクリックして Web アプリを開きます。

![Azure Web アプリの URL の取得](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

IoT ハブから取得したリアルタイムの温度と湿度のデータが表示されます。

![リアルタイムの温度と湿度を示す Azure Web アプリのページ](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>次のステップ
Azure Web アプリを使用して、Azure IoT ハブからのリアルタイム センサー データを視覚化することができました。

このほかにも、Azure IoT Hub からのデータを視覚化する方法があります。 [Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する方法](iot-hub-live-data-visualization-in-power-bi.md)に関するページを参照してください。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
