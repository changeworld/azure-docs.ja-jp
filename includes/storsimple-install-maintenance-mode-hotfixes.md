<properties
   pageTitle="メンテナンス モードの修正プログラムのインストール"
   description="StorSimple 用 Windows PowerShell を使用してメンテナンス モードの修正プログラムをインストールする方法について説明します。"
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

### StorSimple 用 Windows PowerShell を使用してメンテナンス モードの修正プログラムをインストールするには

> [AZURE.IMPORTANT]メンテナンス モードでは、1 つのコントローラーに最初に修正プログラムを適用してから、その他のコントローラーに適用する必要があります。

1. デバイスをメンテナンス モードにします。メンテナンス モードにする方法の手順については、「デバイス モードの変更」を参照してください。

2. 修正プログラムを適用するには、次のように入力します。

     **Start-HcsHotfix**

3. メッセージが表示されたら、修正プログラムのファイルが含まれているネットワーク共有フォルダーへのパスを入力します。

4. 確認を求められます。「**Y**」と入力して修正プログラムのインストールを続行します。

5. 1 つのコントローラーで修正プログラムを適用した後、別のコントローラーにログオンします。前のコントローラーと同様に修正プログラムを適用します。

6. 修正プログラムが適用されたら、メンテナンス モードを終了します。手順については、「[メンテナンス モードを終了するには](#to-exit-maintenance-mode)」を参照してください。

<!--HONumber=52-->
