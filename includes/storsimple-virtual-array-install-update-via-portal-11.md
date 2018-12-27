---
title: インクルード ファイル
description: インクルード ファイル
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: a83095b8330ccf08d777e48389c17058c6d29527
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347981"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure Portal を使用して更新プログラムをインストールするには

1. StorSimple デバイス マネージャーに移動し、**[デバイス]** を選択します。 サービスに接続されているデバイスの一覧から、更新するデバイスを選択してクリックします。

2. **[設定]** で、**[デバイスの更新プログラム]** をクリックします。  

3. ソフトウェアの更新プログラムが利用可能な場合は、メッセージが表示されます。 更新プログラムを確認するには、**[スキャン]** をクリックします。 実行しているソフトウェアのバージョンをメモしておきます。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    スキャンが開始され、正常に完了すると、その旨が通知されます。
 
4. 更新プログラムのスキャンが完了したら、**[更新プログラムのダウンロード]** をクリックします。 **[新しい更新プログラム]** で、リリース ノートを確認します。 更新プログラムをダウンロードしたら、インストールを確認する必要があることにもご注意ください。 Click **OK**.

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    アップロードが開始され、正常に完了すると、その旨が通知されます。

5. **[デバイスの更新プログラム]** で、**[インストール]** をクリックします。

     ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. **[新しい更新プログラム]** で、更新には中断が伴うという警告が表示されます。 仮想アレイが単一ノード デバイスであるため、更新された後にデバイスが再起動されます。 これにより、進行中の IO が中断されます。 **[OK]** をクリックして、更新プログラムをインストールします。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    インストール ジョブの開始されると、その旨が通知されます。

7.  インストール ジョブが正常に完了したら、**[ジョブの表示]** リンクをクリックします。 **[更新プログラムのインストール]** ブレードに移動します。 ここでは、ジョブの詳細情報を確認できます。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Update 1 (10.0.10296.0) のソフトウェア バージョンを実行している仮想アレイで開始した場合は、この時点で Update 1.1 が実行されており、アップデートは完了となります。 残りの手順を省略できます。 

    Update 0.6 (10.0.10293.0) より前のソフトウェア バージョンを実行している仮想アレイで開始した場合、この時点で Update 1.0 に更新されます。 更新プログラムが利用できることを示すメッセージがまた表示されます。 手順 4 から 7 を繰り返して Update 1.1 をインストールします。

    

