<properties urlDisplayName="Capture an image" pageTitle="Windows Server を実行する仮想マシンのイメージのキャプチャ" metaKeywords="Azure capture image vm, capturing vm" description="Windows Server 2008 R2 を実行する Azure 仮想マシン (VM) のイメージをキャプチャする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Windows Server が実行されている仮想マシンのイメージをキャプチャする方法" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# テンプレートとして使用する Windows 仮想マシンのキャプチャ方法

この記事では、Windows 仮想マシンをキャプチャして、他の仮想マシンを作成するためのテンプレートとして使用する方法を説明します。仮想マシン テンプレートには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。ネットワーク構成は含まれないため、テンプレートを使用する他の仮想マシンを作成するときは、ネットワーク構成を行う必要があります。

仮想マシンをキャプチャした後、仮想マシンの作成時に **[マイ イメージ]** をクリックするとイメージ ファイルを使用できます。イメージ ファイルはストレージ アカウント内に VHD として保存されます。イメージの詳細については、「[ディスクおよびイメージの管理][ディスクおよびイメージの管理]」を参照してください。

## 開始する前に

これらの手順は、すでに Azure 仮想マシンを作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。まだ完了していない場合は、下記の手順を確認してください。

-   [カスタム仮想マシンの作成方法][カスタム仮想マシンの作成方法]
-   [データ ディスクを仮想マシンに接続する方法][データ ディスクを仮想マシンに接続する方法]

## 仮想マシンをキャプチャする

1.  コマンド バーで **[接続]** をクリックして、仮想マシンに接続します。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法][Windows Server が実行されている仮想マシンにログオンする方法]」を参照してください。

2.  管理者としてコマンド プロンプト ウィンドウを開きます。

3.  ディレクトリを `%windir%\system32\sysprep` に変更し、sysprep.exe を実行します。

4.  **[システム準備ツール]** ダイアログ ボックスが表示されます。以下の手順を実行します。

    -   **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。Sysprep の使い方の詳細については、「[Sysprep の使用方法: 紹介][Sysprep の使用方法: 紹介]」を参照してください。

    -   **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

    -   **[OK]** をクリックします。

    ![Sysprep を実行する][Sysprep を実行する]

5.  Sysprep コマンドにより仮想マシンがシャットダウンされます。これにより、[管理ポータル][管理ポータル]の仮想マシンの状態が **[停止済み]** に変わります。

6.  **[仮想マシン]** をクリックし、キャプチャする仮想マシンを選択します。

7.  コマンド バーで、**[取り込み]** をクリックします。

    ![仮想マシンをキャプチャする][仮想マシンをキャプチャする]

    **[仮想マシンからイメージをキャプチャします]** ダイアログ ボックスが表示されます。

8.  **[イメージの名前]** に新しいイメージの名前を入力します。

9.  一連のカスタム イメージを Windows Server のイメージを追加する前に、前の手順で指示されたとおりに Sysprep を実行して一般化する必要があります。**[仮想マシンで Sysprep を実行しました]** をクリックして、この一般化が完了したことを通知します。

10. チェック マークをクリックしてイメージをキャプチャします。一般化した仮想マシンのイメージをキャプチャしたら、その仮想マシンは削除されます。

    これで **[イメージ]** で新しいイメージが使用可能になりました。

    ![イメージのキャプチャの成功][イメージのキャプチャの成功]

## 次のステップ

イメージの準備ができましたので、これをテンプレートとして使用して仮想マシンを作成します。そのためには、**[ギャラリーから]** の方法を使用して、先ほど作成したイメージを選択することで、カスタム仮想マシンを作成します。手順については、「[カスタム仮想マシンの作成方法][カスタム仮想マシンの作成方法]」を参照してください。

  [ディスクおよびイメージの管理]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [カスタム仮想マシンの作成方法]: ../virtual-machines-create-custom/
  [データ ディスクを仮想マシンに接続する方法]: ../storage-windows-attach-disk/
  [Windows Server が実行されている仮想マシンにログオンする方法]: http://www.windowsazure.com/ja-jp/manage/windows/how-to-guides/log-on-a-windows-vm/
  [Sysprep の使用方法: 紹介]: http://technet.microsoft.com/ja-jp/library/bb457073.aspx
  [Sysprep を実行する]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
  [管理ポータル]: http://manage.windowsazure.com
  [仮想マシンをキャプチャする]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
  [イメージのキャプチャの成功]: ./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png
