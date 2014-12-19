<properties title="Split and Merge Service Tutorial" pageTitle="Azure SQL の分割および結合サービスのチュートリアル" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#柔軟な拡張の分割および結合サービスのチュートリアル

## 分割-結合パッケージのダウンロード 
1. [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) から最新の NuGet バージョンをダウンロードします。 
2. コマンド プロンプトを開き、nuget.exe をダウンロードしたディレクトリに移動します。 
3. 次のコマンドを使用して、最新の分割-結合パッケージを現在のディレクトリにダウンロードします。 
nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge  

前の手順により、現在のディレクトリに分割-結合ファイルがダウンロードされました。ファイルは、**Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** という名前のディレクトリに配置されます。*x.x.xxx.x* はバージョン番号です。**content\splitmerge\service** サブディレクトリに分割-結合サービス ファイル、**content\splitmerge\powershell** サブディレクトリに分割-結合 PowerShell スクリプト (および必要な .dll) が格納されています。

##前提条件 

1. 分割-結合ステータス データベースとして使用される Azure SQL DB データベースを作成します。[Azure の管理ポータル](https://manage.windowsazure.com)に移動します。左下の **[新規]** をクリックし、**[データ サービス]**、**[SQL Database]**、**[カスタム作成]** の順にクリックします。データベース名を入力し、新しいユーザーとパスワードを作成します。後で使用するため、名前とパスワードを必ず記録してください。

2. Azure SQL DB サーバーで、Microsoft Azure サービスにそのサーバーへの接続が許可されていることを確認します。[Azure の管理ポータル](https://manage.windowsazure.com)に移動し、左側のウィンドウで、**[SQL Databases]** をクリックします。次に、画面上部のリボンで **[サーバー]** をクリックし、サーバーを選択します。次に、上部にある **[構成]** をクリックし、**[Microsoft Azure サービス]** の値が **[はい]** に設定されていることを確認します。

    ![Allowed services][1]

3. 診断の出力に使用される Azure Storage アカウントを作成します。[Azure の管理ポータル](https://manage.windowsazure.com)に移動します。左下の **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。 

4. 分割-結合サービスを含む Azure クラウド サービスを作成します。[Azure の管理ポータル](https://manage.windowsazure.com)に移動します。左下の **[新規]** をクリックし、**[コンピューティング]**、**[クラウド サービス]**、**[簡易作成]** の順にクリックします。 


## 分割-結合サービスの構成 

### 分割-結合サービスの構成 

1. 分割/結合ビットをダウンロードしたフォルダーで、**SplitMergeService.cspkg** に付属の **ServiceConfiguration.Template.cscfg** ファイルのコピーを作成し、**ServiceConfiguration.cscfg** という名前を付けます。

2. 使い慣れたテキスト エディターで、ServiceConfiguration.cscfg を開きます。証明書の拇印の形式などの入力値が検証されるため、Visual Studio を使用することをお勧めします。 

3. 新しいデータベースを作成するか、分割/結合操作のためのステータス データベースとして使用する既存のデータベースを選択し、そのデータベースの接続文字列を取得します。Azure SQL DB では、通常、接続文字列は次の形式です。

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    ElasticScaleMetadata 設定の **SplitMergeWeb** ロールと **SplitMergeWorker** ロールの両方のセクションに cscfg ファイルの接続文字列を入力します。

5.    診断ログのターゲットの構成には、Azure Storage のアカウントが必要です。分割/結合の構成には、Azure Storage のアカウントへの接続文字列が必要です。接続文字列は、次の形式にする必要があります。

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
アクセス キーを確認するには、[Azure の管理ポータル](https://manage.windowsazure.com)に移動し、左側の **[ストレージ]** タブをクリックして、ストレージ アカウントに対応する名前を選択し、下部の **[アクセス キーの管理]** をクリックします。**[プライマリ アクセス キー]** の**コピー** ボタンをクリックします。

![manage access keys][2]

6.    ストレージ アカウントの名前と、提供されたアクセス キーのいずれか 1 つを、ストレージ接続文字列内のプレースホルダーに入力します。この接続文字列は、**Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** 設定の **SplitMergeWeb** ロールと **SplitMergeWorker** ロールの両方のセクションで使用されます。異なるロールには異なるストレージ アカウントを使用できます。 

### セキュリティの構成 
サービスのセキュリティを構成する詳細な手順については、「[Elastic Scale Security Configurations (柔軟な拡張のセキュリティの構成)](./sql-database-elastic-scale-configure-security.md)」を参照してください。

このチュートリアルを完了するために適したシンプルなテスト デプロイメントを目的としているため、サービスを稼働するための最小限の構成手順を実行します。以下の手順では、サービスを実行している 1 つのマシン/アカウントのみがサービスと通信できます。

### 自己署名証明書の作成 

新しいディレクトリを作成し、このディレクトリから、[[開発者コマンド プロンプト for Visual Studio]](http://msdn.microsoft.com/ja-jp/library/ms229859.aspx) ウィンドウを使用して次のコマンドを実行します。

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

秘密キーを保護するためのパスワードの入力が求められます。強力なパスワードを入力し、確定します。その後、パスワードをもう一度使用するよう求められます。最後に **[はい]** をクリックし、それを信頼されたルート証明機関ストアにインポートします。

###    PFX ファイルの作成 

makecert を実行した同じウィンドウから次のコマンドを実行します。証明書の作成に使用したものと同じパスワードを使用します。

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### 個人用ストアに、クライアント証明書をインポートします。
1. Windows エクスプローラーで、**MyCert.pfx** をダブルクリックします。
2. **証明書のインポート ウィザード**で **[現在のユーザー]** を選択し、**[次へ]** をクリックします。
3. ファイルのパスを確認し、**[次へ]** をクリックします。
4. パスワードを入力し、**[すべての拡張プロパティを含める]** をオンのままにして **[次へ]** をクリックします。
5. **[自動的に証明書ストアを選択する][]** をオンのままにして、**[次へ]** をクリックします。
6. **[完了]**、**[OK]** の順にクリックします。

### PFX ファイルのクラウド サービスへのアップロード

[Azure の管理ポータル](https://manage.windowsazure.com)に移動します。

1. **[クラウド サービス]** を選択します。
2. 分割/結合サービス用に上で作成したクラウド サービスを選択します。
3. 上部のメニューで、**[証明書]** をクリックします。
4. 下部のバーで **[アップロード]** をクリックします。
5. PFX ファイルを選択し、前述と同じパスワードを入力します。
6. 完了したら、一覧内の新しいエントリから証明書の拇印をコピーします。

### サービス構成ファイルの更新

上でコピーした証明書の拇印を、次の設定の拇印/値属性に貼り付けます。

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

運用デプロイメントでは、CA、サーバーの証明書、およびクライアントの証明書にそれぞれ別の証明書を使用する必要がある点に注意してください。この詳細な手順については、[セキュリティの構成に関するページ](./sql-database-elastic-scale-configure-security.md)を参照してください。

### 分割-結合サービスのデプロイ
1. [Azure の管理ポータル](https://manage.windowsazure.com)に移動します。
2. 左側の **[クラウド サービス]** タブをクリックし、先ほど作成したクラウド サービスを選択します。 
3. **[ダッシュボード]** をクリックします。 
4. ステージング環境を選択し、**[新しいステージング環境のデプロイをアップロードします。]** をクリックします。

    ![Staging][3]

5. ダイアログ ボックスで、デプロイメント ラベルを入力します。[パッケージ] と [構成] の両方で [ローカルから] をクリックし、**SplitMergeService.cspkg** ファイルと、先ほど構成した .cscfg ファイルを選択します。
6. **[1 つ以上のロールに単一のインスタンスが含まれている場合でもデプロイします。]** チェック ボックスがオンになっていることを確認します。
7. 右下のチェックマーク ボタンをクリックし、デプロイメントを開始します。完了するまでに数分かかります。

![Upload][4]


## デプロイメントのトラブルシューティング
Web ロールをオンラインにできない場合は、セキュリティ構成に問題がある可能性があります。上記で説明したように、SSL が構成されていることを確認します。

ワーカー ロールはオンラインにできず、Web ロールは正常にオンラインになる場合は、先ほど作成したステータス データベースへの接続に問題がある可能性があります。 

* .cscfg 内の接続文字列が正確であることを確認します。 
* サーバーとデータベースが存在し、ユーザー ID とパスワードが正しいことを確認します。
* Azure SQL DB の場合、接続文字列は次の形式にする必要があります。 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* サーバー名が **https://** で始まっていないことを確認します。
* Azure SQL DB サーバーで、Microsoft Azure サービスにそのサーバーへの接続が許可されていることを確認します。確認するには、https://manage.windowsazure.com を開き、左側の [SQL Databases] をクリックし、上部の [サーバー] をクリックしてサーバーを選択します。上部の **[構成]** をクリックし、**[Microsoft Azure サービス]** の値が [はい] に設定されていることを確認します (この記事の「前提条件」を参照してください)。

* 分割/結合サービス インスタンスの診断ログを確認します。Visual Studio のインスタンスを開き、メニュー バーで **[ビュー]**、**[サーバー エクスプローラー]** の順にクリックします。**Microsoft Azure** アイコンをクリックして、Azure サブスクリプションに接続します。その後、[Microsoft Azure]、[ストレージ]、[<ストレージ アカウント>]、[テーブル]、[WADLogsTable] の順に選択します。詳細については、「[サーバー エクスプローラーを使用したストレージ リソースの参照](http://msdn.microsoft.com/ja-jp/library/azure/ff683677.aspx)」を参照してください。

    ![][5]

    ![][6]

## 分割-結合サービス デプロイメントのテスト
### Web ブラウザーでの接続

分割-結合サービスのエンドポイントを決定します。エンドポイントを見つけるには、Azure の管理ポータルでクラウド サービスの **[ダッシュ ボード]** に移動し、右側の **[サイトの URL]** を検索します。既定のセキュリティ設定では HTTP エンドポイントは無効なため、**http://** を **https://** で置き換えます。この URL のページをブラウザーに読み込みます。

### PowerShell スクリプトでのテスト

付属の PowerShell スクリプト サンプルを実行して、デプロイメントと環境をテストできます。

付属のスクリプト ファイルは、次のとおりです。

1. **SetupSampleSplitMergeEnvironment.ps1** - 分割/結合のテスト データ層を設定します (詳細については、次の表を参照してください)
2. **ExecuteSampleSplitMerge.ps1** - テスト データ層でテスト操作を実行します (詳細については、次の表を参照してください)
3. **GetMappings.ps1** - シャード マッピングの現在の状態を出力する最上位のサンプル スクリプトです
4. **ShardManagement.psm1** - ShardManagement API をラップするヘルパー スクリプトです
5. **SqlDatabaseHelpers.psm1** - SQL データベースを作成および管理するためのヘルパー スクリプトです

<table style="width:100%">
  <tr>
    <th>PowerShell ファイル</th>
    <th>手順</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    シャード マップ マネージャー データベースを作成します。</td>
  </tr>
  <tr>
    <td>2.    2 つのシャード データベースを作成します。 
  </tr>
  <tr>
    <td>3.    これらのデータベースのシャード マップを作成します (それらのデータベースに既存のシャード マップを削除します)。 </td>
  </tr>
  <tr>
    <td>4.    両方のシャードで小さなサンプル テーブルを作成し、一方のシャードでテーブルにデータを入力します。</td>
  </tr>
  <tr>
    <td>5.    シャード テーブルの SchemaInfo を宣言します。</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>PowerShell ファイル</th>
    <th>手順</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    分割要求を分割-結合サービス Web フロントエンドに送信します。最初のシャードからのデータが半分に分割され、2 番目のシャードに渡されます。</td>
  </tr>
  <tr>
    <td>2.    Web フロントエンドで分割要求のステータスをポーリングし、要求が完了するまで待機します。</td>
  </tr>
  <tr>
    <td>3.    結合要求を分割-結合サービス Web フロントエンドに送信します。2 番目のシャードからのデータは最初のシャードに戻されます。</td>
  </tr>
  <tr>
    <td>4.    Web フロントエンドで結合要求のステータスをポーリングし、要求が完了するまで待機します。</td>
  </tr>
</table>

##PowerShell を使用したデプロイメントの確認

1.    新しい PowerShell ウィンドウを開き、分割-結合パッケージをダウンロードしたディレクトリに移動し、"powershell" ディレクトリに移動します。
2.    シャード マップ マネージャーとシャードが作成される、Azure SQL データベース サーバーを作成 (または、既存のサーバーを選択) します。 

注: スクリプトを簡潔にするため、SetupSampleSplitMergeEnvironment.ps1 スクリプトでは、これらのデータベースが既定ですべて同じサーバーに作成されます。これは、分割-結合サービス自体の制限ではありません。

    分割-結合サービスで、データを移動したり、シャード マップを更新したりするには、DB への読み取り/書き込みアクセス権を持つ SQL 認証ログインが必要です。分割-結合サービスはクラウド内で実行されるため、現在は統合認証はサポートされていません。

    これらのスクリプトを実行しているマシンの IP アドレスからのアクセスを許可するように Azure SQL Server が構成されていることを確認します。この設定は、[Azure SQL Server]、[構成]、[使用できる IP アドレス] の順に選択すると確認できます。

3.    サンプル環境を作成するには、SetupSampleSplitMergeEnvironment.ps1 スクリプトを実行します。 

    このスクリプトを実行すると、シャード マップ マネージャー データベースとシャード上の既存のシャード マップ管理データ構造はワイプされます。シャード マップまたはシャードを再初期化する場合は、スクリプトを再実行すると便利です。

    サンプルのコマンド ライン:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    サンプル環境に現在あるマッピングを表示するには、Getmappings.ps1 スクリプトを実行します。

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    分割操作 (最初のシャードのデータの半数を 2 番目のシャードに移動) を実行してから、結合操作 (データを最初のシャードに戻す) を実行するには、ExecuteSampleSplitMerge.ps1 スクリプトを実行します。SSL を構成し、http エンドポイントが無効になっている場合は、代わりに https:// エンドポイントを使用してください。

    サンプルのコマンド ライン:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    次のエラーが表示された場合は、Web エンドポイントの証明書に問題がある可能性があります。任意の Web ブラウザーを使用して Web エンドポイントへの接続を試みて、証明書エラーが生じるかどうか確認します。

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    成功した場合、出力は次のようになります。

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request. 
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    他のデータ型を試してください。これらのすべてのスクリプトでは、オプションの -ShardKeyType パラメーターを使用してキーの種類を指定することができます。既定値は Int32 ですが、Int64、Guid、またはバイナリも指定できます。 

## 独自の要求の作成 

Web UI を使用するか、または SplitMerge.psm1 PowerShell モジュールをインポートして使用することで、サービスを使用できます。

分割-結合サービスは、シャード テーブルと参照テーブルの両方でデータを移動できます。シャード テーブルには、シャード キー列があり、シャードごとに異なる行データを保持します。参照テーブルはシャードされないため、すべてのシャードに同じ行データが含まれています。参照テーブルは、頻繁に変更されないデータの場合に効果的で、クエリでのシャード テーブルとの結合に使用されます。

分割-結合操作を実行するには、移動対象のシャード テーブルと参照テーブルを宣言する必要があります。これは **SchemaInfo** API で行います。この API は、**Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** 名前空間にあります。

1.    シャード テーブルごとに、テーブルの親のスキーマ名 (オプション、既定値は "dbo")、テーブル名、およびシャード キーが含まれているテーブル内の列名を記述する **ShardedTableInfo** オブジェクトを作成します。
2.    参照テーブルごとに、テーブルの親のスキーマ名 (オプション、既定値は "dbo") とテーブル名を記述する **ReferenceTableInfo** オブジェクトを作成します。
3.    新しい **SchemaInfo** オブジェクトに、前の TableInfo オブジェクトを追加します。
4.    **ShardMapManager** オブジェクトへの参照を取得し、**GetSchemaInfoCollection** を呼び出します。
5.    **SchemaInfoCollection** に **SchemaInfo** を追加し、シャード マップ名を入力します。

この例は、SetupSampleSplitMergeEnvironment.ps1 スクリプトで確認できます。

分割-結合サービスでは、ターゲット データベース (または、データベースのテーブルに対応するスキーマ) が自動的に作成されない点に注意してください。サービスに要求を送信する前に、事前に作成しておく必要があります。


## トラブルシューティング
サンプル Powershell スクリプトの実行中に、次のメッセージが表示されることがあります。

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

このエラーは、SSL 証明書が正しく構成されていないことを意味しています。「Web ブラウザーでの接続」セクションの手順に従ってください。

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/allowed-services.png
[2]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/manage.png
[3]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/staging.png
[4]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/upload.png
[5]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png
