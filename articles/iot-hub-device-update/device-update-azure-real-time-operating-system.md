---
title: Azure Real-Time Operating System の Device Update | Microsoft Docs
description: Azure Real-Time Operating System の Device Update を使ってみる
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629055"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Azure Real Time Operating System (RTOS) を使用した Device Update for Azure IoT Hub のチュートリアル

このチュートリアルでは、Azure RTOS NetX Duo で Device Update for IoT Hub エージェントを作成する方法について説明します。 また、アプリケーションに Device Update 機能を統合したいと考える開発者のために簡単な API を紹介します。 主要な半導体評価ボードの[サンプル](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)をご覧ください。デバイスへの無線 (OTA) での更新を構成、構築、デプロイする方法が学べるファースト ステップ ガイドが含まれています。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * はじめに
> * デバイスにタグを付ける
> * デバイス グループを作成する
> * イメージの更新プログラムをデプロイする
> * 更新プログラムのデプロイを監視する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
* IoT Hub へのアクセス。 S1 (Standard) レベル以上を使用することをお勧めします。
* ご使用の IoT ハブにリンクされている Device Update インスタンスおよびアカウント。 [Device Update アカウントの作成とリンク](http://create-device-update-account.md/)がまだ済んでいない場合は、ガイドに従って行ってください。

## <a name="get-started"></a>はじめに

ボード固有のサンプル Azure RTOS プロジェクトにはそれぞれ、そのボードに Device Update for IoT Hub を使用する方法を紹介したコードとドキュメントが含まれています。 
1. [Azure RTOS と Device Update サンプル](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)のページから、ボード固有のサンプル ファイルをダウンロードします。
2. ダウンロードしたサンプルの docs フォルダーを見つけます。
3. ドキュメントには、Azure のリソースとアカウントを準備し、そこに IoT デバイスを登録する方法が記載されているので、その手順に従います。
5. 次に、ドキュメントに従って新しいファームウェア イメージを作成し、ボードのマニフェストをインポートします。
6. 次に、ファームウェア イメージとマニフェストを Device Update for IoT Hub に発行します。
7. 最後に、プロジェクトをデバイスにダウンロードして実行します。

[Azure RTOS](https://docs.microsoft.com/azure/rtos/) の詳細情報を参照してください。  

## <a name="tag-your-device"></a>デバイスにタグを付ける

1. 前の手順で使用したデバイス アプリケーションは実行したままにします。
2. [Azure portal](https://portal.azure.com) にログインし、IoT Hub に移動します。
3. 左側のナビゲーション ペインの [IoT デバイス] から、対象の IoT デバイスを見つけて、[デバイス ツイン] に移動します。
4. デバイス ツインで、Device Update の既存のタグ値を null に設定することで削除します。
5. 次に示すように、Device Update の新しいタグ値を追加します。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>更新プログラム グループを作成する

1. 以前に Device Update インスタンスに接続した IoT Hub にアクセスします。
2. 左側のナビゲーション バーの [自動デバイス管理] にある [デバイスの更新] オプションを選択します。
3. ページの上部にある [グループ] タブを選択します。 
4. [追加] ボタンを選択して、新しいグループを作成します。
5. 前の手順で作成した IoT Hub のタグを一覧から選択します。 [更新プログラム グループの作成] を選択します。

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="タグの選択を示すスクリーンショット。" lightbox="media/create-update-group/select-tag.PNG":::

タグの追加と更新プログラム グループの作成に関する[詳細情報](create-update-group.md)

## <a name="deploy-new-firmware"></a>新しいファームウェアをデプロイする

1. グループが作成されると、デバイス グループで利用可能な新しい更新プログラムが表示され、[保留中の更新プログラム] の下にその更新プログラムへのリンクが表示されます。 場合によっては、1 回更新する必要があります。 
2. 利用可能な更新プログラムをクリックします。
3. 対象のグループとして、適切なグループが選択されていることを確認します。 展開をスケジュールし、[Deploy update]\(更新プログラムの展開\) を選択します。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="更新プログラムを選択する" lightbox="media/deploy-update/select-update.png":::

4. コンプライアンス チャートを表示します。 更新が進行中であると表示されます。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="更新が進行中です" lightbox="media/deploy-update/update-in-progress.png":::

5. デバイスが正常に更新されると、コンプライアンス チャートと展開の詳細が、同じ内容を反映するように更新されていることがわかります。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新に成功しました" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>更新プログラムの展開を監視する

1. ページの上部にある [展開] タブを選択します。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="[展開] タブ" lightbox="media/deploy-update/deployments-tab.png":::

2. 作成した展開を選択して、展開の詳細を表示します。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="展開の詳細" lightbox="media/deploy-update/deployment-details.png":::

3. [更新] を選択し、最新の状態の詳細を表示します。 状態が [成功] に変わるまで、このプロセスを続行します。

これで、Raspberry Pi 3 B+ デバイス上で Device Update for IoT Hub を使用した、エンド ツー エンドのイメージ更新が正常に完了しました。 

## <a name="cleanup-resources"></a>リソースをクリーンアップする

不要になったら、デバイス更新アカウント、インスタンス、IoT Hub、IoT デバイスをクリーンアップします。 

## <a name="next-steps"></a>次のステップ

Azure RTOS とその Azure IoT での動作について詳しくは、 https://azure.com/rtos を参照してください。
