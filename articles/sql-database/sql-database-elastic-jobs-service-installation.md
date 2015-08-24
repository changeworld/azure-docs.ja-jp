<properties 
	pageTitle="弾力性データベース ジョブのインストール" 
	description="弾力性ジョブの機能のインストールについて説明します。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="ddove; sidneyh"/>

# エラスティック データベース ジョブのインストールの概要
**エラスティック データベース ジョブ**のインストールには PowerShell または Azure ポータルを使用できますが、PowerShell パッケージをインストールしている場合にのみ、PowerShell API を使用してジョブを作成および管理する機能を利用できます。さらに現在のところ、PowerShell API はポータルよりもはるかに多数の機能を使用できます。**エラスティック データベース ジョブ**の詳細については、「[エラスティック データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」を参照してください。

既存の**エラスティック データベース プール**から、ポータルを使用して**エラスティック データベース ジョブ**を既にインストールしている場合、最新の PowerShell プレビューには、既存のインストールをアップグレードするスクリプトが含まれています。PowerShell API 経由で公開されている新しい機能を活用するために、最新の**エラスティック データベース ジョブ** コンポーネントにアップグレードすることを強くお勧めします。

## 前提条件
* Azure サブスクリプション。無料評価版については、「[無料評価版](http://azure.microsoft.com/pricing/free-trial/)」を参照してください。
* Azure PowerShell Version 0.8.16 以降。[Web Platform インストーラー](http://go.microsoft.com/fwlink/p/?linkid=320376)を使用して最新バージョン (0.9.5) をインストールします。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」をご覧ください。
* [NuGet Command-line Utility](https://nuget.org/nuget.exe) を使用して、エラスティック データベース ジョブ パッケージをインストールします。詳細については、http://docs.nuget.org/docs/start-here/installing-nuget を参照してください。

## エラスティック データベース ジョブ PowerShell パッケージをダウンロードしてインストールする
1. Microsoft Azure PowerShell コマンド ウィンドウを開き、NuGet Command-line Utility (nuget.exe) をダウンロードしたディレクトリに移動します。

2. 次のコマンドを使用して、**エラスティック データベース ジョブ** パッケージを現在のディレクトリにダウンロードしてインポートします。

		PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease

    **エラスティック データベース ジョブ** ファイルは、ローカル ディレクトリの **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** というフォルダー内に保存されます。この *x.x.xxxx.x* には、バージョン番号が反映されます。(必要なクライアント .dll を含む) PowerShell コマンドレットは、**tools\\ElasticDatabaseJobs** サブディレクトリに保存されます。インストール、アップグレード、およびアンインストールを行う PowerShell スクリプトも **tools** サブディレクトリに保存されます。

3. 次のように cd tools と入力して、Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x 以下の tools サブディレクトリに移動します。

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4.	.\\InstallElasticDatabaseJobsCmdlets.ps1 スクリプトを実行して、ElasticDatabaseJobs ディレクトリを $home\\Documents\\WindowsPowerShell\\Modules にコピーします。この処理で、次のように使用するモジュールも自動的にインポートされます。

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1 
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## PowerShell を使用してエラスティック データベース ジョブ コンポーネントをインストールする
1.	Microsoft Azure PowerShell コマンド ウィンドウを開き、cd \\tools と入力して、Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x フォルダー以下の \\tools サブディレクトリに移動します。

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2.	.\\InstallElasticDatabaseJobs.ps1 PowerShell スクリプトを実行し、要求された変数に値を設定します。このスクリプトで、[エラスティック データベース ジョブ コンポーネントと価格設定](sql-database-elastic-jobs-overview/#components-and-pricing)のページで説明されているコンポーネントが作成され、依存するコンポーネントを適切に使用するように Azure クラウド サービスが構成されます。

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1 
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

コマンドを実行すると、**[ユーザー名]** と **[パスワード]** の入力を求めるウィンドウが表示されます。ここで入力するユーザー名とパスワードは Azure の資格情報ではなく、新しいサーバーの管理者の資格情報となります

この呼び出し例で指定したパラメーターは、実際の設定に合わせて変更できます。次に各パラメーターの動作の詳細について説明します。

<table style="width:100%">
  <tr>
    <th>パラメーター</th>
    <th>説明</th>
  </tr>

<tr>
	<td>ResourceGroupName</td>
	<td>新しく作成された Azure コンポーネントを含めるために作成する Azure リソース グループ名を指定します。このパラメーターの既定値は “__ElasticDatabaseJob” です。この値は変更しないことをお勧めします。</td>
	</tr>

</tr>

	<tr>
	<td>ResourceGroupLocation</td>
	<td>新しく作成された Azure コンポーネントに使用する Azure のリージョンを指定します。このパラメーターの既定値は Central US (米国中央部) です。</td>
</tr>

<tr>
	<td>ServiceWorkerCount</td>
	<td>インストールするサービス ワーカーの数を指定します。このパラメーターの既定値は 1 です。ワーカー数を多くすると、サービスをスケール アウトし、高可用性を実現できます。サービスの高可用性が必要なデプロイメントの場合、"2" を使用することをお勧めします。</td>
	</tr>

</tr>
	<tr>
	<td>ServiceVmSize</td>
	<td>クラウド サービス内で使用する VM サイズを指定します。このパラメーターの既定値は A0 です。A0/A1/A2/A3 のパラメーター値を指定できます。それぞれ、worker ロールに ExtraSmall/Small/Medium/Large サイズが使用されます。worker ロール サイズの詳細については、[エラスティック データベース ジョブ コンポーネントと価格設定](sql-database-elastic-jobs-overview/#components-and-pricing) を参照してください。</td>
</tr>

</tr>
	<tr>
	<td>SqlServerDatabaseSlo</td>
	<td>Standard エディションのサービス レベル目標を指定します。このパラメーターの既定値は S0 です。S0/S1/S2/S3 のパラメーター値を指定できます。Azure SQL Database で各 SLO が使用されます。SQL Database SLO の詳細については、[エラスティック データベース ジョブ コンポーネントと価格設定](sql-database-elastic-jobs-overview/#components-and-pricing) を参照してください。</td>
</tr>

</tr>
	<tr>
	<td>SqlServerAdministratorUserName</td>
	<td>新しく作成した Azure SQL Database サーバーの管理ユーザー名を指定します。指定しない場合、PowerShell の資格情報ウィンドウが開き、資格情報の入力を求められます。</td>
</tr>

</tr>
	<tr>
	<td>SqlServerAdministratorPassword</td>
	<td>新しく作成した Azure SQL Database サーバーの管理パスワードを指定します。指定しない場合、PowerShell の資格情報ウィンドウが開き、資格情報の入力を求められます。</td>
</tr>
</table>

多数のデータベースに対して同時に実行されるジョブ数が多数あるシステムの場合、-ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2 などのパラメーターを指定することをお勧めします。

    PS C:*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## PowerShell を使用して既存のエラスティック データベース ジョブ コンポーネントのインストールを更新する

**エラスティック データベース ジョブ**は、スケールと高可用性のために既存のインストール内で更新できます。このプロセスは、将来的に、管理データベースを削除して再作成することなく、サービス コードをアップグレードすること見込んでいます。また、同じバージョン内でこのプロセスを使用して、サービス VM サイズまたはサーバーのワーカー数を変更できます。

インストールの VM サイズを更新するには、パラメーターを選択した値に更新して、次のスクリプトを実行します。

    PS C:*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>パラメーター</th>
  <th>説明</th>
</tr>

  <tr>
	<td>ResourceGroupName</td>
	<td>エラスティック データベース ジョブ コンポーネントを最初にインストールしたときに使用した Azure リソース グループ名を指定します。このパラメーターの既定値は “__ElasticDatabaseJob” です。この値を変更することは推奨されないので、このパラメーターを指定する必要はありません。</td>
	</tr>
</tr>

</tr>

  <tr>
	<td>ServiceWorkerCount</td>
	<td>インストールするサービス ワーカーの数を指定します。このパラメーターの既定値は 1 です。ワーカー数を多くすると、サービスをスケール アウトし、高可用性を実現できます。サービスの高可用性が必要なデプロイメントの場合、"2" を使用することをお勧めします。</td>
</tr>

</tr>

	<tr>
	<td>ServiceVmSize</td>
	<td>クラウド サービス内で使用する VM サイズを指定します。このパラメーターの既定値は A0 です。A0/A1/A2/A3 のパラメーター値を指定できます。それぞれ、worker ロールに ExtraSmall/Small/Medium/Large サイズが使用されます。worker ロール サイズの詳細については、[エラスティック データベース ジョブ コンポーネントと価格設定](sql-database-elastic-jobs-overview/#components-and-pricing) を参照してください。</td>
</tr>

</table>

## ポータルを使用してエラスティック データベース ジョブ コンポーネントをインストールする

[エラスティック データベース プールを作成](sql-database-elastic-pool-portal.md)すると、**エラスティック データベース ジョブ** コンポーネントをインストールして、エラスティック データベース プール内の各データベースに対して管理タスクを実行できるようになります。**エラスティック データベース ジョブ** PowerShell API を使用するときとは異なり、現在、ポータル インターフェイスは既存のプールに対する実行のみに制限されています。


**推定所要時間:** 10 分。

1. [Azure プレビュー ポータル](https://ms.portal.azure.com/#)のエラスティック データベース プールのダッシュボード ビューで、**[ジョブの作成]** をクリックします。
2. 初めてジョブを作成する場合は、**[プレビュー版の使用条件]** をクリックして、**エラスティック データベース ジョブ**をインストールする必要があります。 
3. チェック ボックスをクリックして条項に同意します。
4. [サービスのインストール] ビューで、**[ジョブの資格情報]** をクリックします。

	![サービスのインストール][1]

5. データベース管理者のユーザー名とパスワードを入力します。インストールの一環として、新しい Azure SQL Database サーバーが作成されます。この新しいサーバー内に、管理データベースと呼ばれる新しいデータベースが作成され、エラスティック データベース ジョブのメタデータの格納に使用されます。ここで作成されるユーザー名とパスワードは、管理データベースにログインするために使用されます。プール内のデータベースに対するスクリプト実行には、別の資格情報が使用されます。

	![ユーザー名とパスワードの作成][2]

6. [OK] ボタンをクリックします。新しい[リソース グループ](../resource-group-portal.md)にコンポーネントが数分で作成されます。下記に示すように、新しいリソース グループがスタート ボードに固定表示されています。作成されると、弾力性データベース ジョブ (クラウド サービス、SQL Database、Service Bus、ストレージ記憶域) は、すべてグループ内に作成されます。

	![スタート ボードのリソース グループ][3]

7. 弾力性データベース ジョブをインストール中にジョブを作成または管理しようとすると、**資格情報**の入力中に 、次のメッセージが表示されます。

	![実行中のデプロイメント][4]

アンインストールが必要な場合は、リソース グループを削除します。「[エラスティック データベース ジョブ コンポーネントのアンインストール方法](sql-database-elastic-jobs-uninstall.md)」を参照してください。

## 次のステップ

スクリプトを実行できる適切な権限を持つ資格情報をグループ内の各データベースに作成します。詳細については、「[エラスティック データベース プールにユーザーを追加する方法](sql-database-elastic-jobs-add-logins-to-dbs.md)」を参照してください。概要については、「[エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」を参照してください。

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=August15_HO7-->