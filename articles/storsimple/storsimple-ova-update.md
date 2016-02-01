<properties 
   pageTitle="StorSimple Virtual Array の更新プログラムの適用 | Microsoft Azure"
   description="StorSimple Virtual Array の Web UI を使用して、更新プログラムと修正プログラムを適用する方法について説明します"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# StorSimple Virtual Array への更新プログラムと修正プログラムの適用 (プレビュー)

## 概要

StorSimple Virtual Array を最新の状態に保つために、ソフトウェアの更新プログラムまたは修正プログラムを適用することが必要な場合があります。通常は、Azure クラシック ポータルから更新プログラムをインストールすることをお勧めします。ただし、ポータルが使用できない場合は、ローカル Web UI を使用して修正プログラムや更新プログラムを適用できます。このチュートリアルでは、ローカル Web UI を使用して更新プログラムまたは修正プログラムを適用する方法について説明します (クラシック ポータルから更新プログラムをインストールする場合の手順については、「[Azure クラシック ポータルを使用した通常の更新プログラムのインストール](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal)」をご覧ください)。

更新プログラムまたは修正プログラムをインストールすると、デバイスが再起動される場合があることに注意してください。StorSimple Virtual Array は単一ノード デバイスであることから、実行中のすべての IO が中断され、デバイスである程度のダウンタイムが発生します。

## ローカル Web UI を使用した更新プログラムまたは修正プログラムの適用

更新プログラムまたは修正プログラムをインストールする前に、更新プログラムまたは修正プログラムがローカルでホストにダウンロードされているか、ネットワーク共有を介してアクセス可能であることを確認してください。

#### 更新プログラムまたは修正プログラムをインストールするには

1. ローカル Web UI で、**[Maintenance]** > **[Software Update]** に移動します。

2. **[Update file path]** に、更新プログラムまたは修正プログラムのファイル名を入力します。更新プログラムまたは修正プログラムのインストール ファイルがネットワーク共有にある場合は、ファイルを参照することもできます。更新が開始されます。更新が完了すると通知されます。

    ![デバイスの更新](./media/storsimple-ova-update/image43.png)

## 次のステップ

[StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。

<!---HONumber=AcomDC_0121_2016-->