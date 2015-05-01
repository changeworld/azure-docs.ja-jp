<properties 
   pageTitle="オペレーション インサイトで使用される Operations Manager の実行アカウント"
   description="オペレーション インサイトで使用する Operations Manager の実行アカウントを構成する方法について説明します。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# オペレーション インサイトで使用される Operations Manager の実行アカウント

Microsoft Azure オペレーション インサイトは、データの収集とオペレーション インサイト サービスへのデータの送信に、Operations Manager エージェントと管理グループを使用します。オペレーション インサイトは、ワークロード用の管理パックを基に付加価値サービスを実現しています。それぞれのワークロードがさまざまなセキュリティ コンテキストで管理パックを実行するためには、ワークロード固有の特権が必要となります (ドメイン アカウントなど)。Operations Manager の実行アカウントを構成して資格情報を与えることが必要です。

以降のセクションでは、次のワークロードを対象に、Operations Manager の実行アカウントを設定する方法について説明しています。

- SQL の評価
- Virtual Machine Manager
- Lync Server
- SharePoint

## SQL の評価に使用する実行アカウントの設定

 SQL Server 管理パックを既に使用している場合、SQL Server の実行アカウントを使用する必要があります。

### オペレーション コンソールで SQL の実行アカウントを構成するには 

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[オペレーション インサイト SQL の評価の実行プロファイル]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. SQL Server に必要な資格情報を含んだ Windows 実行アカウントを選択するか、**[新規]** をクリックして新たに作成します。
	>[AZURE.NOTE] 実行アカウントの種類は Windows であることが必要です。さらに、SQL Server インスタンスをホストするすべての Windows Server 上のローカルの Administrators グループに、その実行アカウントが属している必要があります。

5. **[保存]** をクリックします。

6. 次の T-SQL サンプルに変更を加えて、各 SQL Server インスタンスで実行します。実行アカウントで SQL の評価を行うために必要な最低限の権限が付与されます。ただし、実行アカウントが既に SQL Server インスタンスの sysadmin サーバー ロールに属している場合、この作業は不要です。

---
    -- <UserName> の部分は、実行アカウントとして使用する実際のユーザー名に置き換えてください。
    USE master
    
    -- ユーザーのログインを作成します。ログインを作成済みである場合は、この行をコメント化してください。
    CREATE LOGIN [<UserName>] FROM WINDOWS
    
    -- ユーザーに権限を付与します。
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]
    
    -- SQL Server インスタンス上に存在するすべてのデータベースのユーザーを追加します。個々のデータベースに接続するために必要となります。
    -- メモ:SQL Server インスタンスに新しいデータベースを追加したときは、都度このコマンドを実行する必要があります。
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

### Windows PowerShell を使用して SQL 実行アカウントを構成するには

PowerShell ウィンドウを開き、次のスクリプトに自分の情報を反映して実行します。

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## Virtual Machine Manager の実行アカウントの設定

実行アカウントには、次の操作の特権が付与されている必要があります。

- VMM Windows PowerShell モジュールを使用する

- VMM データベースに対してクエリを実行する

- 仮想化ホスト上で動作する VMM エージェントをリモートから管理する

オペレーション インサイトを Operations Manager に接続するときは、以下の手順に従ってアカウントを設定してください。

### VMM の資格情報を設定するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[オペレーション インサイト VMM 実行アカウント]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. VMM に必要な資格情報を含んだ Windows 実行アカウントを選択するか、**[新規]** をクリックして新たに作成します。
	>[AZURE.NOTE] 実行アカウントの種類は Windows であることが必要です。

5. **[保存]** をクリックします。


## Lync Server に使用する実行アカウントの設定

 実行アカウントは、ローカルの Administrators グループと Lync RTCUniversalUserAdmins セキュリティ グループの両方に属している必要があります。

### Lync アカウントの資格情報を設定するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[オペレーション インサイト Lync 実行アカウント]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. ローカルの Administrators グループと Lync RTCUniversalUserAdmins セキュリティ グループの両方に属している Windows 実行アカウントを選択します。 
	>[AZURE.NOTE] 実行アカウントの種類は Windows であることが必要です。

5. **[保存]** をクリックします。


## SharePoint に使用する実行アカウントの設定


### SharePoint アカウントの資格情報を設定するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[オペレーション インサイト SharePoint 実行アカウント]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. SharePoint に必要な資格情報を含んだ Windows 実行アカウントを選択するか、**[新規]** をクリックして新たに作成します。 
	>[AZURE.NOTE] 実行アカウントの種類は Windows であることが必要です。

5. **[保存]** をクリックします。



<!--HONumber=52-->