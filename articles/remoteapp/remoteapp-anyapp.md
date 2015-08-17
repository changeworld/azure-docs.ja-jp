<properties
   pageTitle="Azure RemoteApp を使用して任意のデバイス上で任意の Windows アプリを実行する"
   description="Azure RemoteApp を使用して、ユーザーと任意の Windows アプリを共有する方法について説明します。"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/03/2015"
   ms.author="elizapo"/>

# RemoteApp を使用して任意のデバイス上で任意の Windows アプリを実行する

このチュートリアルでは、Windows アプリケーションを、どこでどんなデバイスを使っていても、今すぐ実行できるようにする方法を紹介します。Internet Explorer 6 であっても、10 年も昔に作成したカスタム アプリケーションであっても、Office アプリであっても、ユーザーは、それらの少数のアプリケーションのために特定のオペレーティング システム (Windows XP など) を使い続ける必要はもうありません。

Azure RemoteApp を使用すれば、ユーザーも、自分の Android や Apple デバイスを使用して Windows (または Windows Phone) でするのと同じエクスペリエンスが得られます。これは、インターネット接続のあるどこからでもユーザーがアクセスできる Azure 上の Windows 仮想マシンに Windows アプリケーションがホストされることにより、実現されます。

このチュートリアルでは、すべてのユーザーと Access を共有します。Access はデータベースです。せっかくのデータベースが役立つように、ユーザーが Access データ共有にアクセスできるようにするいくつかの手順を実行しましょう。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## RemoteApp コレクションの作成

コレクションの作成から始めましょう。コレクションは、アプリとユーザーのコンテナーとして機能します。各コレクションは、1 つのイメージに基づいており、独自に作成するか、サブスクリプションで提供されるものを使用することができます。このチュートリアルでは、共有しようとしているアプリを含む、Office 2013 試用版のイメージを使用します。

1. Azure 管理ポータルの左側のナビゲーションで、Azure RemoteApp が表示されるまで下へスクロールします。そのページを開きます。
2. **[RemoteApp コレクションを作成する]** をクリックします。
3. **[簡易作成]** をクリックし、コレクションの名前を入力します。
4. コレクションの作成に使用するリージョンを選択します。最適に利用するには、ユーザーがアプリにアクセスする場所と地理的に最も近いリージョンを選択します。一例として、このチュートリアルでは、ユーザーがワシントン州レドモンドにいるものと想定しましょう。最も近い Azure リージョンは **米国西部**です。
5. 使用する課金プランを選択します。Basic 課金プランでは、1 つの大型 Azure VM に 16 人のユーザーが設定され、Standard 課金プランの場合は 10 人のユーザーが設定されます。一般的に、Basic プランはデータ エントリ型のワークフローに適しています。Office のような生産性アプリの場合は、Standard プランが適しています。
6. 最後に、Office 2013 Professional のイメージを選択します。このイメージには、Office 2013 アプリが含まれています。  
7. ここで、**[RemoteApp コレクションの作成]** をクリックします。

![RemoteApp でのクラウド コレクションの作成](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

これでコレクションの作成が開始しますが、この作業には 1 時間ほどかかる場合があります。

次に、ユーザーを追加しましょう。

## ユーザーとのアプリの共有

コレクションが正常に作成されたら、ユーザーに対して Access を発行し、アクセス権を持つユーザーを追加する段階になります。

コレクションの作成時に、Azure RemoteApp のノードから移動している場合は、まず Azure ホーム ページから元の場所に戻ります。

1. 左側のナビゲーションにある **[RemoteApp]** をクリックします。
2. 作成しておいたコレクションをクリックし、追加のオプションにアクセスして、コレクションを構成します。
![新規の RemoteApp クラウド コレクション](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. **[発行]** タブの画面の下部にある **[発行]** をクリックしてから、**[スタート メニュー プログラムの発行]** をクリックします。
![RemoteApp プログラムの発行](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. 一覧から発行するアプリを選択します。ここでは Access を選択します。**[完了]** をクリックします。アプリの発行が完了するまで待機します。
![RemoteApp での Access の発行](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. アプリの発行が完了したら、**[ユーザー アクセス]** タブに移動して、アプリにアクセスする必要があるユーザーを全員追加します。ユーザーのユーザー名 (電子メール アドレス) を入力して、**[保存]** をクリックします。
![RemoteApp へのユーザーの追加](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. 次はいよいよ、これらの新しいアプリへのアクセス方法をユーザーに通知する段階です。そのためには、リモート デスクトップ クライアントのダウンロード URL を示す電子メールをユーザーに送信します。
![RemoteApp のクライアント ダウンロード URL](./media/remoteapp-anyapp/ra-anyappurl.png)

## Access へのアクセスの構成

一部のアプリケーションでは、RemoteApp でデプロイした後に追加の構成が必要になります。ここでは、特に Access で、任意のユーザーがアクセスできるファイル共有を Azure 上に作成しようとしています。(その必要がない場合は (クラウド コレクションを作成する代わりに)、ユーザーがローカル ネットワーク上のファイルと情報にアクセスできる[ハイブリッド コレクション](remoteapp-create-hybrid-deployment.md)を作成することもできます。) それから、ユーザーのコンピューターのローカル ドライブを Azure ファイル システムにマップするように、ユーザーに通知する必要があります。

管理者として行う最初の部分です。次に、ユーザーが行ういくつかの手順があります。

1. まず、コマンド ライン インターフェイス (cmd.exe) を発行します。 **[公開]** タブで **[cmd]** を選択し、次に **[発行 > パスを使用してプログラムを発行]** をクリックします。
2. アプリの名前、およびパスを入力します。ここでは、名前には「File Explorer」、パスには「%SYSTEMDRIVE%\\windows\\explorer.exe」を使用します。
![cmd.exe のファイルの発行](./media/remoteapp-anyapp/ra-publishcmd.png)
3. ここで、Azure [ストレージ アカウント](../storage-create-storage-account.md)を作成する必要があります。サンプルには「accessstorage」という名前が付いています。わかりやすい名前を 1 つ選んでください (「accessstorage」 という名前は 1 つしか使えないため)。
![Azure ストレージ アカウント](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. 次に、ダッシュボードに戻り、ストレージ (エンドポイントの場所) へのパスを取得します。しばらくの間使用するので、このパスをどこかにコピーしておいてください。

![ストレージ アカウント パス](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5.ストレージ アカウントが作成されたら、次は、プライマリ アクセス キーが必要になります。**[アクセス キーの管理]** をクリックして、プライマリ アクセス キーをコピーします。
6.ここで、ストレージ アカウントのコンテキストを設定し、Access 用の新しいファイル共有を作成します。管理者特権の Windows PowerShell ウィンドウで、次のコマンドレットを実行します。

        $ctx=New-AzureStorageContext <account name> <account key>
    	$s = New-AzureStorageShare <share name> -Context $ctx

	So for our share, these are the cmdlets we run:

	    $ctx=New-AzureStorageContext accessstorage <key>
    	$s = New-AzureStorageShare <share name> -Context $ctx


次は、ユーザーの番です。まず、ユーザーに [RemoteApp クライアント](remoteapp-clients.md)をインストールしてもらいます。次に、作成してある Azure ファイル共有にユーザーが各自のアカウントからドライブをマップし、各自の Access ファイルを追加する必要があります。以下の方法でこれを実行できます。

1. RemoteApp クライアントで、発行されたアプリにアクセスします。cmd.exe プログラムを起動します。
2. コンピューターからファイル共有にドライブをマップするには、次のコマンドを実行します。

		net use z: \<accountname>.file.core.windows.net<share name> /u:<user name> <account key>

	**/persistent** パラメーターを yes に設定した場合は、マップされたドライブがセッション間で保持されます。
1. 次に、RemoteApp から、ファイル エクスプローラーのアプリを起動します。共有アプリで使用する任意の Access ファイルを、ファイル共有にコピーします。![Azure 共有への Access ファイルの追加](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. 最後に、Access を起動し、共有したデータベースを開きます。クラウドで実行されている Access のデータが表示されるはずです。![クラウドから実行する実際の Access データベース](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

これで、どんなデバイスであっても、RemoteApp クライアントをインストールしておけば Access を使用できます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

コレクションの作成方法を習得できたので、[Office 365 を使用するコレクション](remoteapp-tutorial-o365anywhere.md)を作成してみてください。あるいは、ローカル ネットワークにアクセスする[ハイブリッド コレクション](remoteapp-create-hybrid-deployment.md)を作成することもできます。

<!--Image references-->

<!---HONumber=August15_HO6-->