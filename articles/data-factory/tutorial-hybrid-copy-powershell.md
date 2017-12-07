---
title: "Azure Data Factory を使用して SQL Server から Blob Storage にデータをコピーする | Microsoft Docs"
description: "Azure Data Factory 内のセルフホステッド統合ランタイムを使って、オンプレミスのデータ ストアから Azure クラウドにデータをコピーする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ba47f3e3f331929b884f27f49bf6e484ff363765
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>チュートリアル: オンプレミスの SQL Server から Azure Blob Storage にデータをコピーする
このチュートリアルでは、オンプレミスの SQL Server データベースから Azure BLOB ストレージにデータをコピーする Data Factory パイプラインを Azure PowerShell を使って作成します。 セルフホステッド統合ランタイムを作成して使用すると、データはオンプレミス データ ストアとクラウド データ ストア間を移動します。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。
> 
> この記事では、Data Factory サービスの概要については詳しく取り上げません。 Azure Data Factory サービスの概要については、「[Azure Data Factory の概要](introduction.md)」をご覧ください。 

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * SQL Server と Azure Storage のリンクされたサービスを作成します。 
> * SQL Server と Azure BLOB のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

## <a name="prerequisites"></a>前提条件
### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**または**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。 サブスクリプションで自分が持っているアクセス許可を表示するには、Azure Portal で右上隅にある**ユーザー名**をクリックし、**[アクセス許可]** を選択します。 複数のサブスクリプションへのアクセス権がある場合は、適切なサブスクリプションを選択します。 ロールにユーザーを追加するサンプル手順については、[ロールの追加](../billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

### <a name="sql-server-201420162017"></a>SQL Server 2014/2016/2017
このチュートリアルでは、オンプレミスの SQL Server データベースを**ソース** データ ストアとして使用します。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、このオンプレミスの SQL Server データベース (ソース) から Azure BLOB ストレージ (シンク) にデータをコピーします。 SQL Server データベース内に **emp** という名前のテーブルを作成し、このテーブルにサンプル エントリをいくつか挿入します。 

1. 自分のマシンの **SQL Server Management Studio** を起動します。 マシンに SQL Server Management Studio がない場合は、[ダウンロード センター](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)からインストールします。 
2. 自分の資格情報で SQL Server に接続します。 
3. サンプル データベースを作成します。 ツリー ビューで **[データベース]** を右クリックし、**[新しいデータベース]** をクリックします。 **[新しいデータベース]** ダイアログ ボックスで、データベースの**名前**を入力し、**[OK]** をクリックします。 
4. データベースに対して次のクエリ スクリプトを実行し、**emp** テーブルを作成して、サンプル データを挿入します。 ツリー ビューで、作成した**データベース**を右クリックし、**[新しいクエリ]** をクリックします。 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )

    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このチュートリアルでは、**コピー先/シンク** データ ストアに汎用の Azure Storage アカウント (具体的には Blob Storage) を使用します。 汎用の Azure Storage アカウントがない場合、作成方法については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、オンプレミスの SQL Server データベース (ソース) からこの Azure BLOB ストレージ (シンク) にデータをコピーします。 

#### <a name="get-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このチュートリアルでは、Azure ストレージ アカウントの名前とキーを使用します。 以下の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

1. Web ブラウザーを起動して [Azure Portal](https://portal.azure.com) にアクセスします。 Azure のユーザー名とパスワードを使用してログインします。 
2. 左側のメニューの **[その他のサービス >]** をクリックし、"**ストレージ**" というキーワードでフィルター処理して、**[ストレージ アカウント]** を選択します。

    ![ストレージ アカウントを検索](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. ストレージ アカウントの一覧で、ご利用のストレージ アカウントを (必要に応じて) フィルターで抽出し、**該当するストレージ アカウント**を選択します。 
4. **[ストレージ アカウント]** ページのメニューから **[アクセス キー]** を選択します。

    ![ストレージ アカウントの名前とキーを取得](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. **[ストレージ アカウント名]** フィールドと **[Key1]** フィールドの値をクリップボードにコピーします。 それらをメモ帳または他のエディターに貼り付けて保存します。 このチュートリアルでは、ストレージ アカウントの名前とキーを使用します。 

#### <a name="create-the-adftutorial-container"></a>adftutorial コンテナーの作成 
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Azure BLOB ストレージに作成します。 

1. **[ストレージ アカウント]** ページで **[概要]** に切り替え、**[BLOB]** をクリックします。 

    ![BLOB オプションを選択する](media/tutorial-hybrid-copy-powershell/select-blobs.png)
1. **[Blob service]** ページのツール バーで、**[+ コンテナー]** をクリックします。 

    ![コンテナーの追加ボタン](media/tutorial-hybrid-copy-powershell/add-container-button.png)
3. **[新しいコンテナー]** ダイアログ ボックスで、名前に「**adftutorial**」と入力し、**[OK]** をクリックします。 

    ![コンテナー名を入力する](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)
4. コンテナーの一覧で、**[adftutorial]** をクリックします。  

    ![コンテナーを選択する](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)
5. **adftutorial** の **[コンテナー]** ページを開いたままにしておきます。 チュートリアルの最後で、このページを使用して出力を確認します。 このコンテナーには output フォルダーが Data Factory によって自動的に作成されます。手動で作成する必要はありません。

    ![コンテナー ページ](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>PowerShell をインストールする
ご利用のマシンに最新の PowerShell がまだインストールされていない場合はインストールします。 

1. Web ブラウザーで [Azure SDK のダウンロードと SDK](https://azure.microsoft.com/downloads/) に関するページに移動します。 
2. **[コマンドライン ツール]** -> **[PowerShell]** セクションの **[Windows のインストール]** をクリックします。 
3. Azure PowerShell をインストールするには、**MSI** ファイルを実行します。 

詳しい手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-azurerm-ps)」をご覧ください。 

#### <a name="log-in-to-powershell"></a>PowerShell にログインする

1. お使いのマシンで **PowerShell** を起動します。 PowerShell ウィンドウは、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    ![PowerShell を起動する](media/tutorial-hybrid-copy-powershell/search-powershell.png)
1. 次のコマンドを実行して、Azure Portal へのサインインに使用する Azure ユーザー名とパスワードを入力します。
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 複数の Azure サブスクリプションがある場合は、次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzureRmSubscription
    ```
3. 複数の Azure サブスクリプションがある場合は、次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 (例: `"adfrg"`)。 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$resourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。
3. 後で PowerShell コマンドで使用できるように、データ ファクトリ名の変数を定義します。 名前は文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意となるように更新してください。 たとえば、ADFTutorialFactorySP1127 です。 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```
1. データ ファクトリの場所の変数を定義します。 

    ```powershell
    $location = "East US"
    ```  
5. データ ファクトリを作成するには、次の **Set-AzureRmDataFactoryV2** コマンドレットを実行します。 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFv2TutorialDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**または**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。
* 現在、Data Factory バージョン 2 でデータ ファクトリを作成できるリージョンは、米国東部、米国東部 2、および西ヨーロッパだけです。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

## <a name="create-a-self-hosted-ir"></a>セルフホステッド IR を作成する

このセクションでは、セルフホステッド統合ランタイムを作成し、SQL Server データベースがあるオンプレミスのマシンに関連付けます。 セルフホステッド統合ランタイムは、マシンの SQL Server から Azure BLOB ストレージにデータをコピーするコンポーネントです。 

1. 統合ランタイムの名前に使用する変数を作成します。 一意の名前を使用し、その名前を書き留めます。 このチュートリアルの後の方で、それを使用します。 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. セルフホステッド統合ランタイムを作成します。 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   出力例を次に示します。

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 次のコマンドを実行して、作成された統合ランタイムの状態を取得します。

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   出力例を次に示します。

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 次のコマンドを実行して、クラウドの Data Factory サービスにセルフホステッド統合ランタイムを登録するための**認証キー**を取得します。 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   出力例を次に示します。

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. 次の手順でマシンにインストールするセルフホステッド統合ランタイムを登録するために、いずれかのキーをコピーします (二重引用符は除外)。  

## <a name="install-integration-runtime"></a>統合ランタイムのインストール
1. セルフホステッド統合ランタイムをローカルの Windows マシンに[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)し、インストールを実行します。 
2. **[Welcome to Microsoft Integration Runtime Setup Wizard]\(Microsoft Integration Runtime セットアップ ウィザードへようこそ\)** で **[次へ]** をクリックします。  
3. **[使用許諾契約書]** ページで使用条件とライセンス契約に同意し、**[次へ]** をクリックします。 
4. **[インストール先フォルダー]** ページで **[次へ]** をクリックします。 
5. **[Ready to install Microsoft Integration Runtime]\(Microsoft Integration Runtime のインストール準備完了\)** で **[インストール]** をクリックします。 
6. 構成中のコンピューターが、使用されていないときはスリープ状態または休止状態に移行する旨の警告メッセージが表示された場合は、**[OK]** をクリックしてください。 
7. **[電源オプション]** ウィンドウが表示される場合は、それを閉じて、セットアップ ウィンドウに切り替えます。 
8. **[Completed the Microsoft Integration Runtime Setup Wizard]\(Microsoft Integration Runtime セットアップ ウィザードの完了\)** ページで **[完了]** をクリックします。
9. **[統合ランタイム (セルフホスト) の登録]** ページで、前のセクションで保存したキーを貼り付け、**[登録]** をクリックします。 

   ![統合ランタイムの登録](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. セルフホステッド統合ランタイムが正常に登録されると、次のメッセージが表示されます。

   ![正常に登録](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. **[新しい統合ランタイム (セルフホスト) ノード]** ページで **[次へ]** をクリックします。 

    ![[新しい統合ランタイム ノード] ページ](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. **[イントラネット通信チャネル]** で **[スキップ]** をクリックします。 複数ノードの統合ランタイム環境では、イントラノード通信に使用する TLS/SSL 証明書を選択することができます。 

    ![[イントラネット通信チャネル] ページ](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. **[統合ランタイム (セルフホスト) の登録]** ページで **[構成マネージャーの起動]** をクリックします。 
6. ノードがクラウド サービスに接続されると、次のページが表示されます。

   ![ノード接続済み](media/tutorial-hybrid-copy-powershell/node-is-connected.png)
7. ここで、SQL Server データベースへの接続をテストします。

    ![[診断] タブ](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    - **[構成マネージャー]** ウィンドウで、**[診断]** タブに切り替えます。
    - **[データ ソースの種類]** として **[SqlServer]** を選択します。
    - **サーバー**名を入力します。
    - **データベース**名を入力します。 
    - **認証**モードを選択します。 
    - **ユーザー**名を入力します。 
    - そのユーザー名の**パスワード**を入力します。
    - **[テスト]** をクリックして、統合ランタイムから SQL Server に接続できることを確認します。 接続が成功すると、緑色のチェック マークが表示されます。 それ以外の場合は、失敗に関連したエラー メッセージが表示されます。 問題を修正し、統合ランタイムから SQL Server に接続できるようにします。
    - これらの値 (認証の種類、サーバー、データベース、ユーザー、パスワード) を書き留めます。 このチュートリアルの後の方で使用します。 
    
      
## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このチュートリアルでは、Azure ストレージ アカウントとオンプレミスの SQL Server をデータ ストアにリンクします。 リンクされたサービスは、Data Factory サービスが実行時に接続するために使用する接続情報を持っています。 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage のリンクされたサービスを作成する (コピー先/シンク)
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。

1. 以下の内容を記述した **AzureStorageLinkedService.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダー内に作成します (ADFv2Tutorial フォルダーがない場合は作成します)。  

    > [!IMPORTANT]
    > &lt;accountName&gt; と &lt;accountKey&gt; を実際の **Azure ストレージ アカウント**の名前とキーに置き換えてからファイルを保存してください。 これらは[前提条件](#get-storage-account-name-and-account-key)の一部として書き留めたものです。

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

    メモ帳を使用している場合は、**[名前を付けて保存]** ダイアログ ボックスの **[ファイルの種類]** フィールドで **[すべてのファイル]** を選択します。 そうしないと、ファイルに `.txt` 拡張子が追加されることがあります。 たとえば、「 `AzureStorageLinkedService.json.txt`」のように入力します。 このファイルをエクスプローラーで作成してからメモ帳で開くと、`.txt` 拡張子は表示されない場合があります。これは、**[登録されている拡張子は表示しない]** オプションが既定で設定されているためです。 `.txt` 拡張子を削除してから次の手順に進んでください。 
2. **Azure PowerShell** で **C:\ADFv2Tutorial** フォルダーに切り替えます。

   **Set-AzureRmDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス **AzureStorageLinkedService** を作成します。 

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   出力例を次に示します。

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

    "ファイルが見つかりません" エラーが表示された場合は、 `dir` コマンドを実行して、ファイルが存在することを確認してください。 ファイル名の拡張子が `.txt` となっている場合 (例: AzureStorageLinkedService.json.txt) は、その拡張子を削除してからもう一度 PowerShell コマンドを実行してください。 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server のリンクされたサービスを作成して暗号化する (ソース)
この手順では、オンプレミス SQL Server をデータ ファクトリにリンクします。

1. 次の内容を含む **SqlServerLinkedService.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダーに作成します。SQL Server への接続に使用する**認証**に基づいて、右側のセクションを選択します。  

    > [!IMPORTANT]
    > SQL Server への接続に使用する**認証**に基づいて、右側のセクションを選択します。

    **SQL 認証 (sa) を使用している場合は、次の JSON 定義をコピーします。**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    
    **Windows 認証を使用している場合は、次の JSON 定義をコピーします。**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```
    > [!IMPORTANT]
    > - SQL Server への接続に使用する**認証**に基づいて、右側のセクションを選択します。
    > - **&lt;integration** **runtime** **name>** は、実際の統合ランタイムの名前に置き換えます。
    > - **&lt;servername>**、**&lt;databasename>**、**&lt;username>**、**&lt;password>** を実際の SQL Server の値に置き換えてから、ファイルを保存してください。
    > - ユーザー アカウントまたはサーバー名にスラッシュ文字 (`\`) を使用する必要がある場合は、エスケープ文字 (`\`) を使用します。 たとえば、「 `mydomain\\myuser`」のように入力します。 

2. 機微なデータ (ユーザー名、パスワードなど) を暗号化するには、**New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** コマンドレットを実行します。 この暗号化によって、資格情報が Data Protection Application Programming Interface (DPAPI) を使って暗号化されます。 暗号化された資格情報は、セルフホステッド統合ランタイム ノード (ローカル マシン) のローカルに格納されます。 暗号化された資格情報が含まれる出力ペイロードは別の JSON ファイル (この場合は "encryptedLinkedService.json") にリダイレクトできます。
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. 次のコマンドを実行します。これによって、**EncryptedSqlServerLinkedService** が作成されます。

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>データセットを作成する
このステップでは、コピー操作 (オンプレミスの SQL Server Database => Azure BLOB ストレージ) の入力および出力データを表す入出力データ セットを作成します。

### <a name="create-a-dataset-for-source-sql-database"></a>ソース SQL Database のデータセットを作成する
この手順では、SQL Server データベース内のデータを表すデータセットを定義します。 データセットの型は、**SqlServerTable** です。 前の手順で作成した **SQL Server のリンクされたサービス**を参照します。 リンクされたサービスは、Data Factory サービスが実行時に SQL Server に接続するために使用する**接続情報**を持っています。 このデータセットは、データが含まれる、データベース内の **SQL テーブル**を指定します。 このチュートリアルでは、ソース データが含まれている `emp` テーブルです。 

1. 以下の内容を記述した **SqlServerDataset.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダー内に作成します。  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. データセット **SqlServerDataset** を作成するには、**Set-AzureRmDataFactoryV2Dataset** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    出力例を次に示します。

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>シンク Azure Blob Storage のデータセットを作成する
この手順では、Azure Blob Storage にコピーされるデータを表すデータセットを定義します。 データセットの型は、**AzureBlob** です。 このチュートリアルの前の方で作成した **Azure Storage のリンクされたサービス**を参照します。 リンクされたサービスは、Data Factory が実行時に Azure Storage アカウントに接続するために使用する**接続情報**を持っています。 この**データセット**は、SQL Server データベースのデータのコピー先となる、Azure Storage 内の**フォルダー**を指定します。 このチュートリアルでは、このフォルダーは `adftutorial/fromonprem` です。ここで、`adftutorial` は BLOB コンテナーであり、`fromonprem` はフォルダーです。 

1. 以下の内容を記述した **AzureBlobDataset.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダー内に作成します。

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. データセット **AzureBlobDataset** を作成するには、**Set-AzureRmDataFactoryV2Dataset** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    出力例を次に示します。

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>パイプラインを作成する。
このチュートリアルでは、コピー アクティビティのあるパイプラインを作成します。 コピー アクティビティは、入力データセットとして **SqlServerDataset** を使用し、出力データセットとして **AzureBlobDataset** を使用します。 ソースの種類が **SqlSource** に設定され、シンクの種類が **BlobSink** に設定されています。

1. 以下の内容を記述した **SqlServerToBlobPipeline.json** という名前の JSON ファイルを **C:\ADFv2Tutorial** フォルダー内に作成します。

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. パイプライン **SQLServerToBlobPipeline** を作成するには、**Set-AzureRmDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    出力例を次に示します。

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```


## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する
"SQLServerToBlobPipeline" パイプラインの実行を開始し、後で監視できるようパイプライン実行 ID をキャプチャします。

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. 次のスクリプトを実行して、パイプライン **SQLServerToBlobPipeline** の実行状態を継続的にチェックし、最終的な結果を出力します。 次のスクリプトをコピーして PowerShell ウィンドウに貼り付け、Enter キーを押します。

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    サンプル実行の出力結果を次に示します。

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 次のコマンドを実行して、パイプライン **SQLServerToBlobPipeline** の実行 ID を取得し、詳細なアクティビティの実行結果をチェックすることができます。 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    サンプル実行の出力結果を次に示します。

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```

## <a name="verify-the-output"></a>出力を検証する
このパイプラインは、`adftutorial` BLOB コンテナーに対して `fromonprem` という名前の出力フォルダーを自動的に作成します。 出力フォルダーに **dbo.emp.txt** ファイルがあることを確認してください。 

1. Azure Portal の **adftutorial** コンテナー ページで **[最新の情報に更新]** をクリックして出力フォルダーを表示します。

    ![作成された出力フォルダー](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. フォルダーの一覧で、`fromonprem` をクリックします。 
3. `dbo.emp.txt` という名前のファイルが表示されていることを確認します。

    ![出力ファイル](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 以下の方法について学習しました。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * SQL Server と Azure Storage のリンクされたサービスを作成します。 
> * SQL Server と Azure BLOB のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

Azure Data Factory でサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。

次のチュートリアルに進んで、ソースからコピー先にデータを一括コピーする方法について学習しましょう。

> [!div class="nextstepaction"]
>[データを一括コピーする](tutorial-bulk-copy.md)
