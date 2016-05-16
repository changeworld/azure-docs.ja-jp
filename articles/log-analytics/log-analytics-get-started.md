<properties
	pageTitle="Log Analytics の起動と開始 | Microsoft Azure"
	description="Log Analytics は、Microsoft Operations Management Suite (OMS) からほんの数分で起動して開始できます。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# Log Analytics の起動と開始

Log Analytics は、Microsoft Operations Management Suite (OMS) からほんの数分で起動して開始できます。OMS ワークスペース (アカウントに類似) を作成するには、2 つの方法があります。

- Microsoft Operations Management Suite の Web サイト
- Microsoft Azure サブスクリプション

OMS ワークスペースは、OMS の Web サイトで作成できます。または、Microsoft Azure サブスクリプションを使用して OMS ワークスペースを作成できます。両方のワークスペースの機能は同じです。Azure サブスクリプションを使用する場合、そのサブスクリプションを使用して他の Azure サービスにもアクセスできます。ワークスペースは、作成方法に関係なく、Microsoft アカウントまたは組織アカウントのいずれかを使用して作成します。

プロセスは次のようになります。

![ダイアグラムのオンボード](./media/log-analytics-get-started/oms-onboard-diagram.png)

## Operations Management Suite を使用した 3 ステップでのサインアップ手順

1. [Operations Management Suite](http://microsoft.com/oms) の Web サイトに進み、**[無料試用版]** をクリックします。Outlook.com などの Microsoft アカウント、または Office 365 または他の Microsoft サービスで使用する会社またはや教育機関の組織アカウントを使用してサインインします。
2. 一意のワークスペース名を指定します。ワークスペースは、管理データを保存する論理的なコンテナーです。ワークスペースのデータはそれに限定されたデータなので、これでは、組織のチームごとにデータを分割できるようになります。電子メール アドレスとデータを格納する領域を指定します。![ワークスペースを作成し、サブスクリプションをリンクする](./media/log-analytics-get-started/oms-onboard-create-workspace-link.png)
3. 次いで、新しい Azure サブスクリプションを作成するか、既存の Azure サブスクリプションにリンクします。無料評価版を使用して実行するには、**[後で]** をクリックします。

これで Operations Management Suite ポータルを使用開始する準備が整いました。

Operations Management Suite で作成したワークスペースを設定して、ワークスペースに既存の Azure アカウントをリンクする方法の詳細については、「[OMS へのアクセスの管理](log-analytics-manage-access.md)」を参照してください。

## Microsoft Azure を使用した迅速なサインアップ

1. [Azure ポータル](https://portal.azure.com)に移動してサインインし、サービスの一覧から **Log Analytics (OMS)** を選択します。![Azure ポータル](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. **[追加]**をクリックし、次の項目について選択します。
    - **[OMS ワークスペース]** の名前
    - **[サブスクリプション]** - 複数のサブスクリプションがある場合は、新しいワークスペースに関連付けるサブスクリプションを 1 つ選択します。
    - **[リソース グループ]**
    - **場所**
    - **[価格レベル]** ![簡易作成](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. **[作成]** をクリックすると、Azure ポータルにワークスペースの詳細が表示されます。![ワークスペースの詳細](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
4. **[OMS Portal]** (OMS ポータル) のリンクをクリックすると、Operations Management Suite の Web サイトに新しく作成したワークスペースが表示されます。

これで Operations Management Suite ポータルの使用を開始する準備が整いました。

Operations Management Suite で作成したワークスペースを設定して、既存のワークスペースをリンクする方法の詳細については、「[Manage access to Log Analytics](log-analytics-manage-access.md)」 (Log Analytics へのアクセスの管理) を参照してください。

## Operations Management Suite ポータルの起動と開始
ソリューションを選択して管理するサーバーに接続するには、**[設定]** タイルをクリックし、このセクションの次の手順に従います。

![作業を開始する](./media/log-analytics-get-started/oms-onboard-get-started.png)

- **ソリューションを追加する** - 使用するソリューションを選択して、**[選択したソリューションの追加]** をクリックします。![ソリューション](./media/log-analytics-get-started/oms-onboard-solutions.png)
- **データ ソースを収集する** - サーバー環境に接続し、データを収集する方法を選択します。
    - エージェントをインストールして、任意の Windows サーバーまたはクライアントに直接接続します。
    - System Center Operations Manager を使用して、管理グループまたは Operations Manager デプロイメント全体をアタッチします。
    - Windows または Linux の VM 拡張機能の Azure 診断に構成されている Azure のストレージ アカウントを使用します。![データ ソース](./media/log-analytics-get-started/oms-onboard-data-sources.png)    
- **ログを追加する** 1 つ以上のデータ ソースを構成してデータを入力し、**[保存]** を選択します。イベント ログには、監視するエラー、警告、および情報などのメッセージの種類を指定できます。    

    ![ログ](./media/log-analytics-get-started/oms-onboard-logs.png)


## 必要に応じて、エージェントをインストールして、サーバーを直接 Operations Management Suite に接続します。
1. **[設定]** タイルをクリックし、**[接続されたソース]** タブをクリックして、インストールするコンピューターのアーキテクチャで **[Windows エージェントのダウンロード]** をクリックします。エージェントは、Windows Server 2008 SP 1 以上または Windows 7 SP1 以上にのみインストールできます。
2. エージェントを 1 つ以上のサーバーにインストールします。エージェントは 1 つずつインストールすることも、[カスタム スクリプト](log-analytics-windows-agents.md)を使用した自動化された方法または所持している既存のソフトウェア配布ソリューションを使用する方法でインストールすることもできます。
3. 使用許諾契約書に同意しインストール フォルダーを選択したら、**[Microsoft Azure オペレーション インサイトにエージェントを接続する]** を選択します (OMS は以前は Operational Insights と呼ばれていました)。![エージェントを設定する](./media/log-analytics-get-started/oms-onboard-agent.png)

4. 次のページでは、ワークスペースの ID とワークスペース キーを求められます。エージェント ファイルをダウンロードした画面に、ワークスペース ID とキーが表示されます。![エージェント キー](./media/log-analytics-get-started/oms-onboard-mma-keys.png) ![サーバーをアタッチする](./media/log-analytics-get-started/oms-onboard-key.png)
5. 必要に応じて、プロキシ サーバーを設定し、認証情報を指定するには、インストール中に **[詳細設定]** をクリックします。**[次へ]** ボタンをクリックして、ワークスペースの情報画面に戻ります。
6. **[次へ]** をクリックし、ワークスペース ID およびキーを検証します。エラーが検出された場合は、**[戻る]** で修正できます。ワークスペース ID とキーが検証されたら、**[インストール]** をクリックして、エージェントのインストールを完了します。
7. Operations Management Suite ポータルに戻ってログインし、[概要] ページの **[設定]** タイルをクリックします。エージェントが Operations Management Suite サービスと通信している際には、緑のチェック マーク アイコンが表示されます。初期状態で、これには 5 ～ 10 分かかります。

>[AZURE.NOTE] Operations Management Suite に直接接続されているサーバーの容量管理および構成の査定ソリューションは現在はサポートされていません。


エージェントを System Center Operations Manager 2012 SP1 以降に接続することもできます。これを行うには、**[エージェントを System Center Operations Manager に接続する]** を選択します。このオプションを選択すると、追加のハードウェアなしに、または管理グループをロードせずに、サービスにデータを送信できます。

Operations Management Suite にエージェントを接続する方法の詳細については、「[Connect Windows computers to Log Analytics](log-analytics-windows-agents.md)」 (Log Analytics に Windows コンピューターを接続する) を参照してください。

## オプションでの System Center Operations Manager を使用したサーバーの接続

1. Operations Manager コンソールで、**[管理]** をクリックします。
2. **[オペレーション インサイト]** ノードを展開して、**[オペレーション インサイトの接続]** を選択します。

  >[AZURE.NOTE] 使用している SCOM の更新プログラム ロールアップによって、*System Center Advisor*、*Operational Insights*、*Operations Management Suite* のいずれかのノードが表示されます。

3. 右上の **[Operational Insights に登録する]** リンクをクリックして、指示に従います。
4. 登録ウィザードを完了したら、**[コンピューター/グループの追加]** リンクをクリックします。
5. **[コンピューターの検索]** ダイアログ ボックスでは、Operations Manager で監視するコンピューターまたはグループを検索できます。Log Analytics に追加するコンピューターまたはグループを選択し、**[追加]**、**[OK]** の順にクリックします。Operations Management Suite ポータルの **[使用状況]** タイルに移動すると、OMS サービスがデータを受信していることを確認できます。データは 5 ～ 10 分で表示されます。

Operations Manager を Operations Management Suite に接続する方法の詳細については、「[Connect Operations Manager to Log Analytics](log-analytics-om-agents.md)」 (Operation Manager を Log Analytics に接続する) を参照してください。

## オプションで Microsoft Azure でクラウド サービスのデータを分析する

Operations Management Suite では、Azure Cloud Services の診断を有効にすることで、クラウド サービスや仮想マシンのイベントおよび IIS ログを迅速に検索できます。また、Microsoft Monitoring Agent をインストールすることによって、Azure Virtual Machines の情報を追加で得ることもできます。Operations Management Suite を使用するように Azure 環境を構成する方法の詳細については、「[Connect Azure storage to Log Analytics](log-analytics-azure-storage.md)」 (Azure Storage を Log Analytics に接続する) を参照してください。


## 次のステップ

- 機能を追加し、データを収集する方法については、「[Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md)」 (ソリューションギャラリーから Log Analytics ソリューションを追加する) を参照してください。
- [ログ検索](log-analytics-log-searches.md)について理解を深め、ソリューションによって収集された情報の詳細を確認します。
- カスタム検索結果を保存および表示するには、[ダッシュボード](log-analytics-dashboards.md)を使用します。

<!---HONumber=AcomDC_0504_2016-->