<properties 
   pageTitle="方法 2: Microsoft Azure 管理ポータルを使用して Update 1.2 を適用する"
   description="管理ポータルを使用して、StorSimple 8000 シリーズの Update 1.2 をインストールする方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/08/2015"
   ms.author="v-sharos" />

#### Microsoft Azure 管理ポータルから Update 1.2 をインストールするには

1. 管理ポータルで **[デバイス]** ページに移動し、デバイスを選択します。
 
2. **[デバイス]**、**[構成]** の順にクリックします。

3. **[ネットワーク インターフェイス]** で、iSCSI 対応のネットワーク インターフェイスが少なくとも 1 つは存在することをまず確認します。次に、ゲートウェイが割り当てられているネットワーク インターフェイスを見つけます (DATA 0 以外)。

4. ゲートウェイが割り当てられているネットワーク インターフェイスを無効にし、変更した構成を保存します。ネットワーク インターフェイスの設定は保持されるため、このネットワーク インターフェイスを後で再び有効にすると、ポータルは元の設定に戻ります。

7. これで、[管理ポータルを使用して Update 1.2 をインストールする](#use-the-management-portal-to-install-update-1)ことができます。この手順の手順 3. 以降の指示に従います。すべての更新プログラムをインストールした後で、無効にしたネットワーク インターフェイスを再び有効にできます。

<!---HONumber=Sept15_HO2-->