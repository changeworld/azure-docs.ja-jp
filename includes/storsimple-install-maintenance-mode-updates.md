<properties
   pageTitle="メンテナンス モードの更新プログラムのインストール"
   description="StorSimple 用 Windows PowerShell を使用してメンテナンス モードの更新プログラムをインストールする方法について説明します。"
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
   ms.date="04/21/2015"
   ms.author="v-sharos" />

### StorSimple 用 Windows PowerShell を使用してメンテナンス モードの更新プログラムをインストールするには

1. デバイスのシリアル コンソールにアクセスし、オプション 1 の **[フル アクセスによるログイン]** を選択します (まだの場合)。 

2. パスワードを入力します。既定のパスワードは **Password1** です。

3. コマンド プロンプトに、次のコマンドを入力します。

   **Get-HcsUpdateAvailability**
    
    You will be notified if updates are available and whether the updates are disruptive or non-disruptive.

4. 中断を伴う更新プログラムを適用するには、デバイスをメンテナンス モードにする必要があります。手順については、「[メンテナンス モードを開始するには](#to-enter-maintenance-mode)」を参照してください。

5. デバイスがメンテナンス モードになっている状態で、コマンド プロンプトで次のコマンドを入力します。

    **Start-HcsUpdate**

6. 確認を求められます。更新プログラムを確認すると、現在アクセスしているコントローラーにインストールされます。更新プログラムがインストールされると、コントローラーが再起動されます。

7. 最初のコントローラーの再起動が完了したら、その他のコントローラーに接続し、手順 1. ～ 6. を実行します。

8. 両方のコントローラーが更新されたら、メンテナンス モードを終了します。手順については、「[メンテナンス モードを終了するには](#to-exit-maintenance-mode)」を参照してください。

<!--HONumber=52-->
