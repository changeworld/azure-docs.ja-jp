<properties title="Scripting Elastic Scale with Scripts" pageTitle="スクリプトによる Elastic Scale の操作" description="PowerShell および Azure Automation Service Runbook を使用した、Elastic Scale のスクリプト タスク。" metaKeywords="Azure SQL Database, elastic scale, powershell scripts" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# スクリプトによる Elastic Scale の管理


## Azure Automation サービス 

[Azure Automation](http://azure.microsoft.com/ja-jp/documentation/services/automation/) により、望まれていた強力な PowerShell Workflow 実行サービスを Azure プラットフォームで利用できるようになりました。これで、一般的な Azure ポータルのエクスペリエンス内から難しいメンテナンス タスクを自動化することができます。必要な作業は、PowerShell ワークフロー (Azure Automation では **Runbook** と呼ばれます) を作成してクラウドにアップロードし、Runbook を実行するスケジュールを設定するだけです。このドキュメントでは、いくつかのシャードの弾力性の例に対して Azure Automation を設定する手順を詳しく説明していきます。詳細については、[プレビューに関する発表](http://blogs.technet.com/b/in_the_cloud/archive/2014/04/15/announcing-the-microsoft-azure-automation-preview.aspx)を参照してください。または、Azure [サブスクリプション](https://account.windowsazure.com/PreviewFeatures?fid=automation)にサインアップしてください。

この例では、Azure Automation をスケジュールとワークロードの実行フレームワークとして使用しています。Azure Automation は、[クラウド内の SQL エージェント](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/)と考えてください。 

このドキュメントに加えて、次のリソースも有用です。

* [Azure オートメーションの使用](http://azure.microsoft.com/ja-jp/documentation/articles/automation-create-runbook-from-samples/)
* [Step-by-Step: Getting Started with NEW Microsoft Azure Automation preview feature (ステップ バイ ステップ: 新しい Microsoft Azure Automation プレビュー機能の使用)](http://blogs.technet.com/b/keithmayer/archive/2014/04/04/step-by-step-getting-started-with-windows-azure-automation.aspx) 
* [Microsoft Azure Automation](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx) 
* [Automation フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)で Azure Automation に関する質問をする。  


## 前提条件

Microsoft Azure Automation プレビュー サービスに[サインアップ](http://azure.microsoft.com/ja-jp/services/preview/)してこのサービスを[把握](http://azure.microsoft.com/ja-jp/documentation/articles/automation-create-runbook-from-samples/)してください。 


## シャードの弾力性に関する PowerShell ファイル

次の PowerShell ファイルのセットには、Azure Automation を使用して水平スケーリング シナリオと垂直スケーリング シナリオを実現するための基本的なコマンドが含まれています。 

これらの例は、PowerShell のサンプル モジュールを使用して基本的なシャードの弾力性に関するタスクを実行する方法を示しています。Microsoft Azure Automation サービスと対応する Azure Automation Runbook を組み合わせることにより、自動化およびスケジュールされたジョブを作成して、新しいシャードのプロビジョニングを行ったり、一連のルールに基づいて特定のシャードのパフォーマンス レベルを変更したりすることができます。 

**SetupShardedEnvironment.ps1**: この PowerShell Runbook は、シャード マップ マネージャーと範囲シャード マップを備えたシャード化された環境の 1 回限りのセットアップを実行します。 

**ProvisionByDate.ps1**: 後日のワークロード用に新しいデータベースを事前にプロビジョニングします。作成されたデータベースには、日付スタンプ (YYYYMMDD) に基づいて名前が付けられます。このデータベースには、範囲としてシャード マップ マネージャーに登録されます [YYYYMMDD, YYYYMMDD + 1D)。 

**ProvisionBySize.ps1**: 現在のデータベースの容量が不足したときに新しいデータベースをプロビジョニングします。 

**ReduceServiceTier.ps1**: 提供されているシャード マップ内でのシャードを反復処理して、個々のシャードがパフォーマンス層の縮小の候補であるかどうかを判断します。シャードが候補であるかどうかは、1) シャードの現在のサービス層、2) データベースの有効期間の 2 つの条件に基づいて判定されます。  

**ShardManagement.psm1**: シャード マップ マネージャーと対話するためのメソッドのセットを提供します。 

**SqlDatabaseHelpers.psm1**: Azure SQL データベースと対話するためのメソッドのセットを提供します。 

**ShardElasticity.psm1**: 水平スケーリングと垂直スケーリングを実行するためのメソッドのセットを提供します。 

**ShardElasticityModule.psd1**: Elastic Scale および Azure SQL DB と対話するためのメソッドのセットを提供します。 

## 料金

PowerShell サンプル スクリプトを実行すると、サブスクリプションの所有者に対する課金が実際に発生するデータベースが作成されることに注意してください。基になる Azure SQL DB には、他の Azure SQL DB データベースと変わらない料金が[課金](http://azure.microsoft.com/ja-jp/pricing/details/sql-database/)されます。11 月 1 日以降の料金は、次のとおりです。 

* SetupShardedEnvironment Runbook は、シャード マップ マネージャーを Basic データベース (1 時間あたり $0.0069) に作成し、最初のシャードを Basic データベース (1 時間あたり $0.0069) にプロビジョニングします。 

* ProvisionBySize と ProvisionByDate の Runbook は、どちらも Standard S0 データベース (1 時間あたり $0.0208) をプロビジョニングします。この料金を軽減するために ReduceServiceTier Runbook と組み合わせた場合、新たにプロビジョニングされたデータベースのサービス層は 1 日分の時間が経過した後に Standard S0 (1 時間あたり $0.0208) から Basic (1 時間あたり $0.0069) に引き下げられます。 

最後に、提供されている例のスコープ内では、現在のところ、[Azure Automation](http://azure.microsoft.com/ja-jp/pricing/details/automation/) の使用によってサブスクリプションの所有者が課金されることはありません。詳細については、[Azure Automation の料金に関するページ](http://azure.microsoft.com/ja-jp/pricing/details/automation/)を参照してください。 

## Runbook を読み込むには 

1. **ShardElasticity.zip** ファイルをダウンロードし、内容を抽出します。
2. [NuGet を使用して、Elastic Scale バイナリへの参照を追加します。](./sql-database-elastic-scale-add-references-visual-studio.md)
3. Elastic Scale クライアントのバイナリ (**Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll**) を見つけます。
4. この DLL を ShardElasticityModule フォルダーに配置し、フォルダーを zip 圧縮します。 
3. Azure Automation アカウントで、ShardElasticityModule.zip ファイルを**アセット**としてアップロードします。 
4. Azure Automation で、Azure SQL Database サーバーのユーザー名とパスワードを含む、*ElasticScaleCredential* という名前の**アセット資格情報**を作成します。 
5. Azure SQL Database サーバーの完全修飾名を格納する、*SqlServerName* という名前の**変数アセット**を作成します。 
5. **SetupShardedEnvironment.ps1**、**ProvisionBySize.ps1**、**ProvisionByDate.ps1**、および **ProvisionByDate.ps1** を Runbook としてアップロードします。 
6. 1 回限りの操作として、**SetupShardedEnvironment.ps1** Runbook をテストしてシャーディング環境をセットアップします。 
7. 1 つまたは複数の残りの Runbook を発行し、その Runbook をスケジュールにリンクします。 
8. **[ジョブ]** タブを使用して、Runbook の出力を確認します。 

この簡易版の手順で操作が成功しなかった場合は、以下の詳細な手順を参照してください。  

##  Runbook を使用するには

1. PowerShell モジュールを作成してパッケージ化する 
2. Microsoft Azure Automation アカウントを作成する 
3. PowerShell モジュールをアセットとして Azure Automation にアップロードする 
4. Azure Automation の資格情報アセットと変数アセットを作成する 
5. PowerShell Runbook を Azure Automation にアップロードする 
6. シャード化された環境をセットアップする 
7. Automation Runbook をテストする 
8. Runbook を発行する 
9. Runbook をスケジュールする 


## PowerShell モジュールを作成してパッケージ化する 

最初に、Elastic Scale アセンブリを参照する PowerShell モジュールを作成し、アセットとして Azure Automation サービスにアップロードできるようにこのモジュールをパッケージ化します。 

1. "ShardElasticity.zip" ファイルをダウンロードします。
2. すべての内容を抽出します。

    ![Extract all][1]
3. Elastic Scale クライアントの DLL (Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll) を見つけ、手順 1. でダウンロードしたローカルの "ShardElasticityModule" フォルダーに次のファイルをコピーします。そのためには、1) DLL を Elastic Scale NuGet パッケージ [リンク]からダウンロードするか、または 2) Elastic Scale Starter Kit プロジェクト [リンク] (ビルドする必要あり) の \bin\Debug\ から DLL を取得します。

    ![Obtain Dll][2]

4. ShardElasticityModule フォルダーを zip 圧縮します。 

注: Azure Automation では、いくつかの名前付け規則に従う必要があります。モジュール名が ShardElasticityModule.psm1 である場合、zip ファイル名はこの名前に正確に一致する必要があります (ShardElasticityModule.zip)。zip ファイルには ShardElasticityModule というモジュール名に一致する名前のフォルダーが含まれ、このフォルダーに psm1 ファイルが含まれます。この構造に従わない場合、Azure Automation はモジュールをアンパックできません。

5.    zip 圧縮したフォルダーの内容と構造が要件を満たしていることを確認したら、次の手順に進みます。次のような構造になっていることを確認します。

    ![dll][3]


## Azure Automation プレビューにサインアップする

1. [Azure プレビュー機能のページ](http://azure.microsoft.com/ja-jp/services/preview/)に移動します。
    
2. **[試用版]** をクリックします。

    ![Click Try It][8]

2. [Microsoft Azure ポータル](https://manage.windowsazure.com/microsoft.onmicrosoft.com)に移動します。
3. **[オートメーション]** をクリックします。

    ![Automation][4]
4. 画面の下部にある **[作成]** をクリックします。 
5. 次の図に示すプロンプトに対して有効なアカウント名を入力し、ボックスの右下隅にあるチェック マークをクリックします。

    ![Prompt][5] 
5. 次の手順に進みます。成功すると、次のような画面が表示されます。

    ![success][6]

## PowerShell モジュールをアセットとして Azure Automation にアップロードする 

前の手順で作成した PowerShell モジュールを Azure Automation アカウントにアップロードします。たとえば、モジュールには、シャードの弾力性機能と、Runbook から参照できる Elastic Scale DLL のセットが含まれます。 

1. 画面上部のリボンにある **[アセット]** をクリックします。
2. ページの下部にある **[モジュールのインポート]** をクリックします。 
3. **[ファイルの参照]** をクリックし、前の手順で作成した **ShardElasticityModule.zip** ファイルを参照します。 
4. 正しいファイルを選択したら、ボックスの右下隅にあるチェック マークをクリックしてファイルをインポートします。Azure Automation サービスによってモジュールがインポートされます。 
5. 次の手順に進みます。成功すると、次のような画面が表示されます。モジュールが正常にインポートされなかった場合は、上記の規則に zip ファイルが従っていることを確認してください。

    ![Assets][10] 
      
## Azure Automation の資格情報アセットと変数アセットを作成する 

Azure Automation では、資格情報やよく使用する変数を Runbook にハード コーディングする代わりに、複数の Runbook 間で参照できる資格情報アセットと変数アセットを作成できます。これにより、パスワードを変更する作業を 1 か所で済ませることができます。 

1. 前の手順で作成した新しい Azure Automation アカウントを選択します。
2. **ShardElasticityExamples** アカウントで、リボンの **[アセット]** をクリックします。
3. 画面の下部にある **[設定の追加]** をクリックします。  
4. **[資格情報の追加]** をクリックします。 

    ![Add credential][9]
4. **[資格情報の種類]** で **[Windows PowerShell 資格情報]** を選択し、[名前] で **[ElasticScaleCredential]** を選択します。説明は省略できます。  
5. ボックスの右下隅にある矢印をクリックします。 

注: 変更を加えずに Runbook を使用するには、手順に示されているとおりの変数名を使用します。これらの変数名は Runbook から参照されます。 
5. シャードの弾力性の例を実行する Azure SQL DB サーバーのユーザー名とパスワード (2 回) を入力します。 
 
6. 変数アセットを作成するために、**[設定の追加]** をクリックし、**[変数の追加]** を選択します。 

    ![Add variable][7]
7. このチュートリアルでは、シャード マップ マネージャーとシャード化されたデータベースを配置する Azure SQL DB サーバーの完全修飾名を格納する変数を作成します。**[変数の型]** で **[文字列]** を選択し、「**SqlServerName**」と入力します。矢印をクリックして続行します。 
8. [値] に Azure SQL DB サーバーの完全修飾名を入力し、チェック マークをクリックします。 
9. これで、シャードの弾力性 Runbook で使用される資格情報と変数アセットの両方が作成されました。次の手順に進みます。成功すると、次のような画面が表示されます。 
    

## PowerShell Runbook を Azure Automation にアップロードする 

提供されている 4 つのサンプル PowerShell Runbook をアップロードします。 

1. 新しい Azure Automation の Runbook をアップロードするために、リボンの **[Runbook]** をクリックします。 
2. 画面の下部にある **[インポート]** をクリックします。 
3. ファイルが含まれているフォルダーに移動して **SetupShardedEnvironment.ps1** を選択し、チェック マークをクリックします。 
4. 残りの 3 つの PowerShell Runbook (**ProvisionByDate.ps1**、**ProvisionBySize.ps1**、**ReduceServiceTier.ps1**) について、手順 2. と 3. を繰り返します。 
5. 次の手順に進みます。 

## シャード化された環境をセットアップする 

次に、シャード マップ マネージャー DB、範囲シャード マップ、および現在の日付のシャードを備えたシャード化環境をプロビジョニングする **SetupShardedEnvironment** を実行します。   

1. 名前をクリックして **SetupShardedEnvironment** Runbook を選択します。 
2. リボンの **[作成者]** をクリックします。 
3. この画面には、Runbook を構成するコードが表示されます (必要に応じてコードを編集できます)。シャード化された環境をセットアップするために、画面の下部にある **[テスト]** ボタンをクリックします。Runbook を保存してテストすることを確認します。 
4. ジョブの状態は出力ウィンドウで確認できます。処理が完了すると、指定した Azure SQL DB サーバーに、シャード データベースと共にシャード マップ マネージャー データベースが設定されます。**SetupShardedEnvironment** Runbook は、シャード化された環境をプロビジョニングすることのみを目的としていて、繰り返し発生するスケジュールに従って実行することは目的としていません。次の手順に進みます。  

## Automation Runbook をテストする 

Runbook を公開してスケジュールする前に、これらの Runbook が正常に実行されるかどうかをテストします。 

1. ページの上部のリボンにある **[Runbook]** をクリックします。 
2. **[ProvisionByDate]** Runbook をクリックします。
3. ページの上部のリボンにある **[作成者]** をクリックします。 
4. **[保存]**、**[テスト]** の順にクリックします。
5. **ReduceServiceTier** に対して同じ手順を繰り返します。 

**ProvisionBySize** と **ProvisionByDate** はどちらも (異なるアルゴリズムを使用して) 新しいシャードをプロビジョニングするため、今回は **ProvisionByDate** を実行する必要はありません。 

## Runbook を発行する 
次に、Runbook を発行して、定期実行スケジュールを設定できるようにします。 

1. ページの下部にある **[発行]** をクリックします。 
2. **[発行済み]** をクリックします。 
3. 次の手順に進みます。
 
## Runbook をスケジュールする 

最後の手順では、前の手順で発行した Runbook のスケジュールを作成してリンクします。 

1. ページの上部にある **[スケジュール]** をクリックします。 
2. **[新しいスケジュールへのリンク]** をクリックします。
3. スケジュールに適切な名前を付け、右矢印ボタンをクリックします。
4. スケジュールを構成します。
5. 操作が完了したら、ボックスの下部にあるチェック マークをクリックします。
6. 前もって設定したスケジュールに基づいてジョブが実行されたら、ページ上部のリボンの **[ジョブ]** オプションをクリックし、スケジュールされたジョブを選択します。 

## まとめ 

これで、PowerShell モジュールを作成してパッケージ化した後、Azure Automation にアセットとしてアップロードし、Runbook を作成、テスト、発行、およびスケジュールすることができました。Runbook の正常性とステータスを監視するには、ダッシュ ボードとジョブのタブを使用します。 

ここで示した例は、Elastic Scale、Azure SQL DB、Azure Automation を組み合わせることによって実行できることのほんの一部です。これらの例を基にして、水平スケーリング、垂直スケーリング、またはその両方を可能にする独自の Azure SQL DB Elastic Scale アプリケーションを作成してください。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-scripting/success.png
[7]: ./media/sql-database-elastic-scale-scripting/add-variable.png
[8]: ./media/sql-database-elastic-scale-scripting/sign-up.png
[9]: ./media/sql-database-elastic-scale-scripting/add-credential.png
[10]: ./media/sql-database-elastic-scale-scripting/assets.png
