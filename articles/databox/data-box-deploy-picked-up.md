---
title: Microsoft Azure Data Box の返送 | Microsoft Docs
description: Azure Data Box を Microsoft に送付する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/03/2018
ms.author: alkohli
ms.openlocfilehash: 7676360d71dab4da58693221645517c69b56dff8
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090690"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>チュートリアル: Azure Data Box の返送と Azure へのデータ アップロードの確認

このチュートリアルでは、Azure Data Box を返送し、Azure にアップロードされたデータを確認する方法について説明します。

このチュートリアルでは、次のようなトピックについて説明します。

> [!div class="checklist"]
> * Data Box を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に、[Azure Data Box にデータをコピーして確認する方法](data-box-deploy-copy-data.md)に関するチュートリアルが完了していることを確認してください。

## <a name="ship-data-box-back"></a>Data Box を返送する

1. デバイスの電源をオフにして、ケーブル類を取り外します。 デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
2. デバイスを米国で配送する場合、配送先住所ラベルが電子インク ディスプレイに表示されていることを確認し、運送業者の集荷をスケジュールします。 ラベルを破損または紛失した場合、または電子インク ディスプレイに表示されていない場合は、配送先住所ラベルを Azure portal からダウンロードして、デバイスに貼り付けてください。 **[概要] > [出荷ラベルをダウンロード]** に移動します。 

    デバイスをヨーロッパで配送する場合、電子インク ディスプレイは配送先住所ラベルを表示しません。 代わりに、先積み配送先住所ラベルの下のクリアポーチ内に返送用配送先住所ラベルがあります。 古い配送先住所ラベルを取り除き、配送先住所ラベルがはっきり見えることを確認してください。
    
3. 米国内でデバイスを返送する場合は、UPS での集荷をスケジュールします。 ヨーロッパで DHL を使ってデバイスを返送する場合は、DHL の Web サイトにアクセスし、航空貨物運送状番号を指定して、DHL に集荷を依頼します。 該当する国の DHL Express の Web サイトにアクセスし、**[Book a Courier Collection]\(宅配便の予約\) > [eReturn Shipment]\(電子返送\)** を選択します。 

    貨物運送状番号を指定し、**[Schedule Pickup]\(集荷のスケジュール\)** をクリックして集荷の手配を行います。

4. 運送業者によって Data Box が集荷され、スキャンされると、ポータルの注文状態は "**集荷されました**" に更新されます。 追跡 ID も表示されます。

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

Microsoft がディスクを受け取ってスキャンすると、注文の状態は "**受取済み**" に更新されます。 その後、デバイスの損傷または改ざんの印がないか物理的に検証されます。 

検証が完了すると、Data Box は Azure データセンターのネットワークに接続されます。 データのコピーが自動的に開始されます。 データのサイズによっては、コピー処理が完了するまでに数時間から数日かかる場合があります。 コピー ジョブの進行状況は、ポータルで監視することができます。

コピーが完了すると、注文の状態が "**完了**" に更新されます。

コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。 

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
 Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box からデータを消去する

次の記事に進み、ローカル Web UI 経由で Data Box を管理する方法を学習します。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Azure Data Box を管理する](./data-box-local-web-ui-admin.md)


