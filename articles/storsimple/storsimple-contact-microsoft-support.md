<properties 
   pageTitle="Microsoft サポートに問い合わせる | Microsoft Azure"
   description="サポート要求を作成する方法と StorSimple デバイスでサポート セッションを開始する方法について説明します。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="alkohli" />

# Microsoft サポートに問い合わせる

Microsoft Azure StorSimple ソリューションで問題が発生した場合は、テクニカル サポートに対するサービス要求を作成できます。サポート エンジニアとのオンライン セッションで、StorSimple デバイスのサポート セッションを開始することが必要になる場合もあります。この記事では、サポート要求を作成するプロセスについて説明したうえで、StorSimple デバイスの Windows PowerShell インターフェイスでサポート セッションを開始するプロセスについても説明します。

## サポート要求の作成

サポート要求を作成するには、次の手順を実行します。

#### サポート要求を作成するには

1. サポート要求は、[管理ポータル](http://manage.windowsazure.com/)を使って作成できます。[管理ポータル](http://manage.windowsazure.com/)で、**アカウント名**をクリックし、**[Microsoft サポートに問い合わせる]** をクリックします。

	![Contact MS Support via ManagementPortal](./media/storsimple-contact-microsoft-support/IC777286.png)

1. **[Microsoft サポートに問い合わせる]** ダイアログで、次の操作を行います。


	- ドロップダウン リストから、StorSimple Manager サービスに関連付けられているターゲットの**サブスクリプション**を選択します。**[サポートの種類]** で **[技術]** を指定します。テクニカル サポートを利用するには、有料サポート プランに加入している必要があります。

	1. チェック マーク アイコン ![Check icon](./media/storsimple-contact-microsoft-support/IC740895.png) をクリックして**チケットを作成**します。

1. **[Microsoft サポート]** ウィンドウで、**[製品]** ボックスの一覧の **[StorSimple]** を選択します。

	![Contact Microsoft Support - Product](./media/storsimple-contact-microsoft-support/IC777288.png)

1. 画面の指示に従い、適切に要求を分類し、問題に関する明確で具体的な説明を入力します。

要求を送信した後、その要求を処理するサポート エンジニアから速やかに連絡があります。

## StorSimple 用 Windows PowerShell でのサポート セッションの開始

StorSimple デバイスで発生した問題のトラブルシューティングを行うには、Microsoft サポート チームと情報交換する必要があります。Microsoft サポートがサポート セッションを使用してユーザーのデバイスにログオンすることが必要になる場合があります。

サポート セッションを開始するには、次の手順を実行します。

#### サポート セッションを開始するには

1. シリアル コンソールから直接デバイスにアクセスするか、リモート コンピューターから telnet セッションを使用してデバイスにアクセスします。そのためには、「[PuTTY を使用してデバイスのシリアル コンソールに接続する](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」の手順を実行します。

1. 開いたセッションで、**Enter** キーを押して、コマンド プロンプトを開きます。

1. シリアル コンソール メニューで、オプション 1 の **[フル アクセスによるログイン]** を選択します。

1. プロンプトで、次のパスワードを入力します。

	`Password1`

1. プロンプトで、次のコマンドを入力します。

	`Enable-HcsSupportAccess`

1. 暗号化された文字列が表示されます。この文字列をメモ帳などのテキスト エディターにコピーします。

1. この文字列を保存し、電子メール メッセージで Microsoft サポートに送信します。

> [AZURE.IMPORTANT]サポートへのアクセスは、`Disable-HcsSupportAccess` を実行して無効にできます。また、StorSimple デバイスでは、セッションの開始後 8 時間が経過すると、サポートへのアクセスが無効になります。サポート セッションを開始した後、StorSimple デバイスの資格情報を変更することをお勧めします。

<!---HONumber=July15_HO5-->