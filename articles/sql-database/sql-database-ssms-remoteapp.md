<properties
	pageTitle="Azure RemoteApp で SQL Server Management Studio を使用して SQL Database に接続する | Microsoft Azure"
	description="このチュートリアルでは、Azure RemoteApp で SQL Server Management Studio を使用して、SQL Database に接続するときにセキュリティとパフォーマンスを確保する方法について説明します。"
	services="sql-database"
	documentationCenter=""
	authors="adhurwit"
	manager=""/>

<tags
	ms.service="sql-database"
	ms.workload="data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="adhurwit"/>

# Azure RemoteApp で SQL Server Management Studio を使用して SQL Database に接続する

## はじめに  
このチュートリアルでは、Azure RemoteApp で SQL Server Management Studio (SSMS) を使用して、SQL Database に接続する方法について説明します。Azure RemoteApp で SQL Server Management Studio を設定する手順を示し、その利点と、Azure Active Directory で使用できるセキュリティ機能について説明します。

**推定所要時間:** 45分

## Azure RemoteApp での SSMS

Azure RemoteApp は、アプリケーションを配布する Azure の RDS サービスです。詳細については、「[Azure RemoteApp とは](../remoteapp/remoteapp-whatis.md)」をご覧ください。

Azure RemoteApp で SSMS を実行すると、ローカルで実行した SSMS と同じエクスペリエンスが提供されます。

![Azure RemoteApp で実行している SSMS のスクリーンショット][1]



## メリット

Azure RemoteApp で SSMS を使用すると、次のような多くのメリットがあります。

- Azure SQL Server のポート 1433 を外部 (Azure の外部) に公開する必要がありません。
- IP アドレスの追加と削除を Azure SQL Server ファイアウォールの内側だけにする必要がありません。
- Azure RemoteApp のすべての接続は、暗号化されたリモート デスクトップ プロトコルを使用して、ポート 443 の HTTPS 経由で行われます。
- マルチ ユーザー対応であり、拡張可能です。
- SQL Database と同じリージョンに SSMS がある場合よりパフォーマンスが向上します。
- ユーザー アクティビティ レポートを備えた Azure Active Directory の Premium エディションで Azure RemoteApp の使用を監査できます。
- Multi-Factor Authentication (MFA) を有効にできます。
- iOS、Android、Mac、Windows Phone、Windows PC など、サポートされる Azure RemoteApp クライアントを使用して、どこからでも SSMS にアクセスできます。


## Azure RemoteApp コレクションの作成

ここでは、SSMS で Azure RemoteApp コレクションを作成する手順を示します。


### 1\.イメージから新しい Windows 仮想マシンを作成する
ギャラリーの "Windows Server Remote Desktop Session Host Windows Server 2012 R2" イメージを使用して、新しい VM を作成します。


### 2\.SQL Express から SSMS をインストールする

新しい VM で [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/ja-JP/download/details.aspx?id=42299) のダウンロード ページに移動します。

SSMS のみをダウンロードするオプションがあります。ダウンロードした後、インストール ディレクトリに移動し、セットアップを実行して SSMS をインストールします。

SQL Server 2014 Service Pack 1 もインストールする必要があります。[Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/ja-JP/download/details.aspx?id=46694) はこちらでダウンロードできます。

SQL Server 2014 Service Pack 1 には、Azure SQL Database を使用するための基本機能が含まれます。


### 3\.Validate スクリプトと Sysprep を実行する

VM のデスクトップに Validate という名前の PowerShell スクリプトがあります。これをダブルクリックして実行します。このスクリプトは、VM がアプリケーションのリモート ホストに使用できる状態かどうかを検証します。検証が完了すると、sysprep を実行するかどうかを尋ねられるので実行します。

sysprep は、完了すると VM をシャットダウンします。

Azure RemoteApp イメージの作成方法の詳細については、「[How to create a RemoteApp template image in Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)」をご覧ください。


### 4\.イメージをキャプチャする

VM の実行が停止したら、現在のポータルで探してキャプチャします。

イメージのキャプチャの詳細については、「[クラシック デプロイ モデルを使用して作成された Azure Windows 仮想マシンのイメージをキャプチャする](../virtual-machines/virtual-machines-windows-classic-capture-image.md)」をご覧ください。


### 5\.Azure RemoteApp テンプレート イメージに追加する

現在のポータルの Azure RemoteApp セクションで、[テンプレート イメージ] タブに移動して、[追加] をクリックします。ポップアップ ボックスで [Virtual Machines ライブラリからイメージをインポートします] を選択し、先に作成したイメージを選びます。



### 6\.クラウド コレクションを作成する

現在のポータルで、新しい Azure RemoteApp クラウド コレクションを作成します。前の手順でインポートした、SSMS がインストールされているテンプレート イメージを選びます。

![新しいクラウド コレクションの作成][2]


### 7\.SSMS を発行する

新しいクラウド コレクションの [発行] タブで、[スタート] メニューから [アプリケーションの発行] を選び、リストから SSMS を選びます。

![アプリの発行][5]

### 8\.Add users (ユーザーを追加する)

[ユーザー アクセス] タブでは、SSMS のみを含むこの Azure RemoteApp コレクションにアクセスできるユーザーを選択できます。

![ユーザーの追加][6]


### 9\.Azure RemoteApp クライアント アプリケーションをインストールする

Azure RemoteApp は、[Azure RemoteApp のダウンロード ページ](https://www.remoteapp.windowsazure.com/en/clients.aspx)からダウンロードしてインストールできます。



## Azure SQL Server の構成

必要な構成は、ファイアウォールで Azure サービスを有効にすることだけです。このソリューションを使用する場合は、ファイアウォールを開くために IP アドレスを追加する必要はありません。SQL Server に対して許可されるネットワーク トラフィックは、他の Azure サービスからのものです。


![Azure の許可][4]



## Multi-Factor Authentication (MFA)

特にこのアプリケーションを対象にして MFA を有効にできます。Azure Active Directory の [アプリケーション] タブに移動します。Microsoft Azure RemoteApp のエントリがあります。そのアプリケーションをクリックして構成する場合、次のページでこのアプリケーションに対して MFA を有効にできます。

![MFA の有効化][3]



## Azure Active Directory Premium でのユーザー アクティビティの監査

Azure AD Premium がない場合は、ディレクトリの [ライセンス] セクションで有効にする必要があります。Premium を有効にすると、ユーザーを Premium レベルに割り当てることができます。

Azure Active Directory でユーザーに移動すると、[アクティビティ] タブに移動して Azure RemoteApp へのログイン情報を確認できます。



## 次のステップ

以上の手順をすべて完了すると、Azure RemoteApp クライアントを実行し、割り当てられたユーザーでログインできるようになります。アプリケーションの 1 つとして SSMS が表示され、Azure SQL Server にアクセスできるコンピューターにインストールされている場合と同様に実行できます。

SQL Database に接続する方法の詳細については、「[SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)」をご覧ください。


今のところは以上ですべてです。機能を有効にご活用ください。



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png

<!---HONumber=AcomDC_0413_2016-->