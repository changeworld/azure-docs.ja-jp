---
title: Microsoft Azure Data Box Disk の返送| Microsoft Docs
description: このチュートリアルでは、Azure Data Box Disk を Microsoft に返送する方法について説明します。
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 783c837bbb9ce22e7354252523e6725753776836
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011060"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>チュートリアル: Azure Data Box Disk の返送と Azure へのデータ アップロードの確認

これは、Azure Data Box Disk のデプロイについて取り上げたシリーズの最後のチュートリアルです。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Data Box Disk を Microsoft に発送する
> * Azure へのデータのアップロードを確認する
> * Data Box Disk からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に、「[チュートリアル: Azure Data Box Disk にデータをコピーして確認する](data-box-disk-deploy-copy-data.md)」が完了していることを確認してください。

## <a name="ship-data-box-disk-back"></a>Data Box Disk を返送する

1. データの検証が完了したら、ディスクの接続を解除します。 接続ケーブルを取り外してください。
2. すべてのディスクと接続ケーブルをエアー クッションで包んで梱包箱に詰めます。
3. 梱包箱に貼り付けられている透明のビニール袋に入った返送ラベルを使用します。 ラベルを破損または紛失した場合は、新しい配送先住所ラベルを Azure portal からダウンロードして、デバイスに貼り付けてください。 **[概要] > [出荷ラベルをダウンロード]** に移動します。 

    ![配送先住所ラベルのダウンロード](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    この操作により、次に示した返送ラベルがダウンロードされます。

    ![配送先住所ラベルの例](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. 梱包箱を封印し、返送ラベルが見えることを確認します。
5. 米国内でデバイスを返送する場合は、UPS での集荷をスケジュールします。 ヨーロッパで DHL を使ってデバイスを返送する場合は、DHL の Web サイトにアクセスし、航空貨物運送状番号を指定して、DHL に集荷を依頼します。 該当する国の DHL Express の Web サイトにアクセスし、**[Book a Courier Collection]\(宅配便の予約\) > [eReturn Shipment]\(電子返送\)** を選択します。

    ![DHL 電子返送](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    貨物運送状番号を指定し、**[Schedule Pickup]\(集荷のスケジュール\)** をクリックして集荷の手配を行います。

      ![集荷のスケジュール](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. 運送業者によってディスクが集荷されると、ポータルの注文状態が "**集荷されました**" に更新されます。 追跡 ID も表示されます。

    ![ディスクが集荷済み](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

Microsoft がディスクを受け取ってスキャンすると、ジョブの状態が "**受取済み**" に更新されます。 

![ディスク受取済み](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Azure データセンター内のサーバーにディスクが接続されると、データが自動的にコピーされます。 データのサイズによっては、コピー処理が完了するまでに数時間から数日かかる場合があります。 コピー ジョブの進行状況は、ポータルで監視することができます。

コピーが完了すると、注文の状態が "**完了**" に更新されます。

![データのコピーが完了](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。 Azure にデータがアップロードされたことを確認するには、次の手順を実行します。

1. ディスクの注文に関連付けられているストレージ アカウントに移動します。
2. **[Blob service] > [BLOB の参照]** に移動します。 コンテナーの一覧が表示されます。 *BlockBlob* フォルダーと *PageBlob* フォルダーに作成したサブフォルダーに対応して、同じ名前のコンテナーがご利用のストレージ アカウントに作成されます。
    Azure の名前付け規則にフォルダー名が準拠していない場合、Azure へのデータのアップロードに失敗します。

4. データセット全体が読み込み済みであることを確認するには、Microsoft Azure Storage Explorer を使用します。 ディスク レンタル注文に対応するストレージ アカウントをアタッチし、BLOB コンテナーの一覧に注目します。 いずれかのコンテナーを選択し、**[その他]** をクリックして **[Folder statistics]\(フォルダーの統計情報\)** をクリックします。 **[アクティビティ]** ウィンドウに、そのフォルダーの統計情報 (BLOB 数、合計 BLOB サイズなど) が表示されます。 合計 BLOB サイズ (バイト単位) がデータセットのサイズと一致している必要があります。

    ![フォルダーの統計情報を Storage Explorer で表示](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Data Box Disk からデータを消去する

コピーが完了し、Azure Storage アカウントにデータが存在することをお客様が確認した後、ディスクは NIST 標準に従って確実に消去されます。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Disk に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box Disk を Microsoft に発送する
> * Azure へのデータのアップロードを確認する
> * Data Box Disk からデータを消去する


次の記事に進み、Azure portal で Data Box Disk を管理する方法について学習してください。

> [!div class="nextstepaction"]
> [Azure portal を使用して Azure Data Box Disk を管理する](./data-box-portal-ui-admin.md)


