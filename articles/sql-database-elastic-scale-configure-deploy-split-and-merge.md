<properties title="Split and Merge Service Tutorial" pageTitle="Azure SQL の分割およびマージ サービス チュートリアル" description="Elastic Scale による分割とマージ" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Elastic Scale の分割およびマージ サービス チュートリアル

## Split-Merge パッケージのダウンロード 
1. [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) から最新の NuGet のバージョンをダウンロードします。 
2. コマンド プロンプトを開き、nuget.exe をダウンロードしたディレクトリに移動します。 
3. 次のコマンドで最新の Split-Merge パッケージを現在のディレクトリにダウンロードします。 
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

前の手順を実行すると Split-Merge ファイルが現在のディレクトリにダウンロードされます。ファイルは **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** という名前のディレクトリに配置されます。ここで、*x.x.xxx.x* はバージョン番号です。Split-Merge サービス ファイルを **content\splitmerge\service** サブディレクトリで検索します。また、Split-Merge PowerShell スクリプト (および必要な client .dll) を **content\splitmerge\powershell** サブディレクトリで検索します。

##前提条件 

1. Split-Merge の状態データベースとして使用する Azure SQL DB を作成します。[Azure の管理ポータル](https://manage.windowsazure.com)に移動します。下部左側で、**[新規作成]** をクリックしてから、**[データ サービス]**、**[SQL データベース]**、**[カスタム作成]** の順にクリックします。データベース名を入力し、新しいユーザーとパスワードを作成します。今後の使用のために、パスワードと名前を必ず記録しておいてください。

2. 使用する Azure SQL DB サーバーに Windows Azure サービスが接続できることを確認します。[Azure の管理ポータル](https://manage.windowsazure.com)に移動し、左側のウィンドウで **[SQL データベース]** をクリックします。次に、画面上部のリボンにある **[サーバー]** をクリックし、サーバーを選択します。上部の **[構成]** をクリックして、**Windows Azure サービス**の設定が **[はい]** に設定されていることを確認します。

    ![Allowed services][1]

3. 診断の出力に使用する Azure ストレージ アカウントを作成します。[Azure の管理ポータル](https://manage.windowsazure.com)に移動します。左下の **[新規作成]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。 

4. Split-Merge サービスが含まれる Azure のクラウド サービスを作成します。[Azure の管理ポータル](https://manage.windowsazure.com)に移動します。左下の **[新規作成]** をクリックし、**[コンピューティング]**、**[クラウド サービス]**、**[簡易作成]** の順にクリックします。 


## 使用する Split-Merge サービスを構成する 

### Split-Merge サービスの構成 

1. 分割およびマージのビットをダウンロードしたフォルダーで、**ServiceConfiguration.Template.cscfg** ファイル (**SplitMergeService.cspkg** に同梱されているファイル) のコピーを作成し、**ServiceConfiguration.cscfg** という名前を付けます。

2. 使い慣れたテキスト エディターで、ServiceConfiguration.cscfg を開きます。Visual Studio を使用して証明書のサムプリントの形式などの入力値を検証することをお勧めします。 

3. 新しいデータベースを作成するか、分割およびマージ操作を行う状態データベースとして既存のデータベースを選択してそのデータベースの接続文字列を取得します。Azure SQL DB では、通常、接続文字列の形式は次のようになります。

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    cscfg ファイルのこの接続文字列を、ElasticScaleMetadata 設定のロール セクションである **SplitMergeWeb** および **SplitMergeWorker** の両方に入力します。

5.    診断ログ用のターゲットを構成するには、Azure のストレージ アカウントが必要です。分割およびマージの構成では、Azure ストレージ アカウントへの接続文字列が必要です。接続文字列の形式は次のようにする必要があります。

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
アクセス キーを判別するには、[Azure の管理ポータル](https://manage.windowsazure.com)に移動し、左側の **[ストレージ]** タブをクリックします。使用するストレージ アカウントに対応する名前を選択してから下部の **[アクセス キーの管理]** をクリックします。**[プライマリ アクセス キー]** で **[コピー]** をクリックします。

![manage access keys][2]

6.    ストレージ アカウントの名前と、ストレージの接続文字列のプレースホルダーに提供されているアクセス キーの 1 つを入力します。この接続文字列は、**Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** 設定のロール セクション **SplitMergeWeb** と **SplitMergeWorker** の両方で使用されます。異なるロールには異なるストレージ アカウントを使用できる可能性があります。 

### セキュリティの構成 
サービスのセキュリティを構成するための詳細な手順については、「[Elastic Scale のセキュリティの構成](./sql-database-elastic-scale-configure-security.md)」を参照してください。

このチュートリアルを完了するのに適した簡単なテストのデプロイという目的上、最小限の構成の手順セットを行ってサービスを起動および実行します。以下の手順では、サービスを実行している 1 つのマシン/アカウントのみがサービスと通信できます。

### 自己署名証明書の作成 

新しいディレクトリを作成し、このディレクトリで [[Visual Studio 開発者コマンド プロンプト]](http://msdn.microsoft.com/ja-jp/library/ms229859.aspx) ウィンドウを使用して次のコマンドを実行します。

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

秘密キーを保護するパスワードの入力を求められます。強力なパスワードを入力し、確認入力します。その後、このパスワードをもう一度使用するよう求められます。最後に **[はい]** をクリックして、信頼されたルート証明機関ストアにインポートします。

###    PFX ファイルの作成 

makecert が実行されたウィンドウで次のコマンドを実行します。証明書を作成するときに使用したものと同じパスワードを使用してください。

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### 個人用ストアへの証明書のインポート
1. Windows エクスプローラーで、**MyCert.pfx** をダブルクリックします。
2. **証明書のインポート ウィザード**で、**[現在のユーザー]** を選択し、**[次へ]** をクリックします。
3. ファイルのパスを確認し、**[次へ]** をクリックします。
4. パスワードを入力します。**[すべての拡張プロパティを含める]** をオンのままにして **[次へ]** をクリックします。
5. **[自動的に証明書ストアを選択する][]** をオンのままにし、**[次へ]** をクリックします。
6. **[完了]**、**[OK]** の順にクリックします。

### クラウド サービスへの PFX ファイルのアップロード

[Azure の管理ポータル](https://manage.windowsazure.com)に移動します。

1. **[クラウド サービス]** を選択します。
2. 上記で分割およびマージのサービスに作成したクラウド サービスを選択します。
3. 上部のメニューで **[証明書]** をクリックします。
4. 下部のバーの **[アップロード]** をクリックします。
5. PFX ファイルを選択して先ほどと同じパスワードを入力します。
6. 完了後に、一覧内の新しいエントリから証明書のサムプリントをコピーします。

### サービス構成ファイルの更新

上記でコピーした証明書のサムプリントを、次の設定で、サムプリントおよび属性値に貼り付けます。

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

運用デプロイメントでは、CA、サーバー証明書、クライアント証明書のそれぞれに異なる証明書を使用する必要があることに注意してください。詳しい手順については、「[Elastic Scale のセキュリティの構成](./sql-database-elastic-scale-configure-security.md)」を参照してください。

### Split-Merge サービスのデプロイ
1. [Azure の管理ポータル](https://manage.windowsazure.com)に移動します。
2. 左側の **[クラウド サービス]** タブをクリックし、先ほど作成したクラウド サービスを選択します。 
3. **[ダッシュボード]** をクリックします。 
4. ステージング環境を選択してから、**[新しいステージング環境のデプロイをアップロードします。]** をクリックします。

    ![Staging][3]

5. ダイアログ ボックスにデプロイのラベルを入力します。[パッケージ] および [構成] の両方で、[ローカルから] をクリックし、**SplitMergeService.cspkg** ファイルと先ほど構成した .cscfg ファイルを選択します。
6. **[1 つ以上のロールに単一のインスタンスが含まれている場合でもデプロイします。]** とラベルの付いたチェック ボックスがオンになっていることを確認します。
7. 右下のチェック マークをクリックしてデプロイを開始します。完了には数分かかります。

![Upload][4]


## デプロイのトラブルシューティング
Web ロールのオンライン化に失敗した場合は、セキュリティの構成に問題があると考えられます。SSL が前の説明どおりに構成されていることを確認してください。

worker ロールのオンライン化に失敗した場合に最も考えられるのは、先に作成した状態データベースへの接続に問題があることです。 

* 使用する .cscfg の接続文字列が正確であることを確認してください。 
* サーバーとデータベースが存在し、ユーザー ID とパスワードが正しいか調べます。
* Azure SQL DB の場合、接続文字列の形式は次のようにする必要があります。 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* サーバー名が **https://** で始まっていないことを確認してください。
* 使用する Azure SQL DB サーバーに Windows Azure サービスが接続できることを確認します。これを行うには、https://manage.windowsazure.com を開いて左側の [SQL Database] をクリックし、上部の [サーバー] をクリックしてから使用するサーバーを選択します。上部の **[構成]** をクリックし、**Windows Azure サービス**の設定が [はい] になっていることを確認します (この記事の冒頭にある前提条件を参照してください)。

* 分割およびマージ サービス インスタンスの診断ログを確認します。Visual Studio インスタンスを開き、メニュー バーで **[ビュー]**、**[サーバー エクスプローラー]** の順にクリックします。**Windows Azure** のアイコンをクリックして Azure サブスクリプションに接続します。次に、Windows Azure、[ストレージ]、使用するストレージ アカウント、[テーブル]、[WADLogsTable] の順に移動します。詳細については、「[サーバー エクスプローラーを使用したストレージ リソースの参照と管理](http://msdn.microsoft.com/ja-jp/library/azure/ff683677.aspx)」を参照してください。

    ![][5]

    ![][6]

## Split-Merge サービスのデプロイのテスト
### Web ブラウザーを使って接続する

Split-Merge サービスの Web エンドポイントを決定します。Azure の管理ポータルで、使用するクラウド サービスの **[ダッシュボード]** に移動し、右側の **[サイトの URL]** を検索すると見つけることができます。既定のセキュリティ設定では HTTP エンドポイントが無効のため、**http://** を **https://** に置き換えます。この URL のページをブラウザーに読み込みます。

### PowerShell スクリプトを使用したテスト

付属の PowerShell のサンプル スクリプトを実行すると、デプロイメントと使用環境をテストすることができます。

次のスクリプト ファイルが含まれています。

1. **SetupSampleSplitMergeEnvironment.ps1** - 分割とマージ用のデータ層をセットアップする (詳しい説明については下の表を参照)
2. **ExecuteSampleSplitMerge.ps1** - テスト データ層でテスト操作を実行する (詳しい説明については下の表を参照)
3. **GetMappings.ps1** - シャード マッピングの現在の状態を出力する最上位レベルのスクリプト
4. **ShardManagement.psm1**  - ShardManagement API をラップするヘルパー スクリプト
5. **SqlDatabaseHelpers.psm1** - SQL データベースを作成および管理するヘルパー スクリプト

<table style="width:100%">
  <tr>
    <th>PowerShell ファイル</th>
    <th>手順</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1. シャードのマップ マネージャー データベースを作成します。</td>
  </tr>
  <tr>
    <td>2. 2 つのシャード データベースを作成します。 
  </tr>
  <tr>
    <td>3. これらのデータベースにシャード マップを作成 (データベースにある既存のシャード マップは削除) します。 </td>
  </tr>
  <tr>
    <td>4. 両シャード上に小さいサンプル テーブルを作成し、いずれかのシャードで、このテーブルにデータを読み込みます。</td>
  </tr>
  <tr>
    <td>5. シャード化したテーブルの SchemaInfo を宣言します。</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>PowerShell ファイル</th>
    <th>手順</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1. データを半分に分割して最初のシャードから 2 番目のシャードに渡す分割要求を Split-Merge サービスの Web フロントエンドに送信します。</td>
  </tr>
  <tr>
    <td>2. 分割要求の状態を Web フロントエンドに対してポーリングし、要求が完了するまで待機します。</td>
  </tr>
  <tr>
    <td>3. 2 番目のシャードから最初のシャードにデータを戻すための移動を求めるマージ要求を、Split-Merge サービスの Web フロントエンドに送信します。</td>
  </tr>
  <tr>
    <td>4. マージ要求の状態を Web フロントエンドに対してポーリングし、要求が完了するまで待機します。</td>
  </tr>
</table>

##PowerShell によるデプロイの確認

1.    新しい PowerShell ウィンドウを開き、Split-Merge パッケージをダウンロードしたディレクトリに移動してからディレクトリ "powershell" に移動します。
2.    Azure SQL データベース サーバーを作成 (または既存のサーバーを選択) します。ここでシャード マップ マネージャーおよびシャードが作成されます。 

注: SetupSampleSplitMergeEnvironment.ps1 スクリプトは、これらのデータベースをすべて同じサーバー上に既定で作成します。これはスクリプトの簡潔さを維持するためです。Split-Merge サービス自体の制約ではありません。

Split-Merge サービスでデータを移動してシャード マップを更新するためには、DB への読み取りおよび書き込みのアクセス権のある SQL 認証ログインが必要になります。Split-Merge サービスはクラウドで実行するため、現時点では統合認証はサポートしていません。

これらのスクリプトを実行するマシンの IP アドレスからのアクセスを許可するように Azure SQL サーバーが構成されていることを確認してください。この設定は、Azure SQL サーバーから [構成]、[使用できる IP アドレス] で検索することができます。

3.    SetupSampleSplitMergeEnvironment.ps1 スクリプトを実行してサンプル環境を作成します。 

このスクリプトを実行すると、シャード マップ マネージャー データベース上にある既存のシャード マップ管理データ構造およびシャードはすべて消去されます。これは、シャード マップやシャードの再初期化を希望する場合、スクリプトを再実行するのに便利です。

    サンプル コマンド ライン: 

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Getmappings.ps1 スクリプトを実行して、サンプル環境に現存するマッピングを表示します。

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    ExecuteSampleSplitMerge.ps1 スクリプトを実行して、分割操作を実行 (データの半分を最初のシャードから 2 番目のシャードに移動) し、次にマージ操作を実行 (データを最初のシャードに戻すために移動) します。SSL が構成済みで、http エンドポイントが無効のままになっている場合は、代わりに https:// のエンドポイントを使用します。

    サンプル コマンド ライン: 

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

下記のエラーが表示された場合に最も考えられるのは、Web エンドポイントの証明書に問題があることす。任意の Web ブラウザーを使用して Web エンドポイントに接続を試み、認証エラーになるか確認してください。

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    接続できた場合、次のような出力になります。

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

6.    他のデータ型も試してみてください。これらのスクリプトはすべて、オプションの -ShardKeyType パラメーターでキーの種類を指定することができます。既定は Int32 ですが、Int64、Guid、または Binary も指定できます。 

## 独自の要求の作成 

サービスは、Web UI を使用するか、SplitMerge.psm1 PowerShell モジュールをインポートして使用する方法のいずれかで利用できます。

Split-Merge サービスは、シャード化したテーブルと参照テーブルの両方にデータを移動することができます。シャード化したテーブルにはシャーディング キー列があり、シャードごとに異なる行データがあります。参照テーブルはシャード化されないため、すべてのシャードに同じ行データが含まれています。データを頻繁には変更しない場合に参照テーブルを使用すると便利です。また、これをクエリに使用してシャード化されたテーブルとの JOIN を行います。

分割やマージの操作を実行するには、移動の対象となるシャード化したテーブルと参照テーブルを宣言する必要があります。これは **SchemaInfo** API を使用して行います。この API は **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** 名前空間にあります。

1.    シャード化するテーブルごとに、テーブルの親スキーマ名 (オプション、既定は "dbo")、テーブル名、および列名を、シャーディング キーが含まれているテーブルに記述した **ShardedTableInfo** オブジェクトを作成します。
2.    参照テーブルごとに、テーブルの親スキーマ名 (オプション、既定では "dbo") とテーブル名を記述した **ReferenceTableInfo** オブジェクトを作成します。
3.    この TableInfo オブジェクトに新しい **SchemaInfo** オブジェクトを追加します。
4.    **ShardMapManager** オブジェクトへの参照を取得し、**GetSchemaInfoCollection** を呼び出します。
5.    シャード マップ名を指定した **SchemaInfo** を **SchemaInfoCollection** に追加します。

この実行例は、SetupSampleSplitMergeEnvironment.ps1 で見ることができます。

Split-Merge サービスはターゲット データベース (またはデータベースにある任意のテーブル用のスキーマ) を作成しないことに注意してください。サービスに要求を送信する前に作成しておく必要があります。


## トラブルシューティング
Powershell のサンプル スクリプトを実行すると、次のようなメッセージが表示されることがあります。

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

このエラーは、SSL 証明書が正しく構成されていないことを意味しています。「Web ブラウザーを使って接続する」のセクションの手順に従ってください。

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/allowed-services.png
[2]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/manage.png
[3]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/staging.png
[4]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/upload.png
[5]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png
