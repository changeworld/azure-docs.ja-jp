<properties
   pageTitle="通常の修正プログラムのインストール"
   description="StorSimple 用 Windows PowerShell を使用して通常の修正プログラムをインストールする方法について説明します。"
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
   ms.date="04/23/2015"
   ms.author="v-sharos" />
### StorSimple 用 Windows PowerShell を使用して通常の修正プログラムをインストールするには

1. デバイスのシリアル コンソールに接続します。詳細については、「[シリアル コンソール経由で接続するには](#to-connect-through-the-serial-console)」を参照してください。

2. シリアル コンソール メニューで、オプション 1 の **\[フル アクセスによるログイン\]** を選択します。パスワードを入力します。既定のパスワードは **Password1** です。

3. コマンド プロンプトに、次のコマンドを入力します。

     **Start-HcsHotfix**

   >[AZURE.IMPORTANT]

   > - このコマンドは通常の修正プログラムのみに適用されます。このコマンドは 1 つのコントローラーでのみ実行しますが、両方のコントローラーが更新されます。
   > - 更新プロセス中にコントローラーのフェールオーバーが行われても、システムの可用性または処理には影響しません。

4. メッセージが表示されたら、修正プログラムのファイルが含まれているネットワーク共有フォルダーへのパスを入力します。

5. 確認を求められます。「**Y**」と入力して修正プログラムのインストールを続行します。

<!--HONumber=52-->
