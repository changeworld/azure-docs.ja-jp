<properties
    pageTitle="Operational Insights を数分で理解する | Microsoft Azure"
    description="数分で Azure Operational Insights を設定する方法"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/10/2015"
    ms.author="banders"/>

# Azure Operational Insights を数分で理解する


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

数分で Azure Operational Insights を起動して使用できるようにできます。Operational Insights のワークスペースを作成するには、2 つの方法があります。これは、アカウントと類似しています。

- Microsoft Operations Management Suite
- Microsoft Azure サブスクリプション

Operations Management Suite ワークスペースは、Operations Management Suite の Web サイトを使用して作成します。また、Operational Insights のワークスペースは、Microsoft Azure サブスクリプションを使用して作成します。現時点では、両ワークスペースには同等の機能があります。2 つの唯一の違いは名前です。Azure サブスクリプションを使用する場合、そのサブスクリプションを使用して他の Azure サービスにもアクセスできます。ワークスペースは、作成方法に関係なく、Microsoft アカウントまたは組織アカウントのいずれかを使用して作成します。

プロセスは次のようになります。

![オンボード プロセス](./media/operational-insights-onboard-in-minutes/onboard-oms.png)

## Operations Management Suite を使用した 3 ステップでのサインアップ手順

1. [Operations Management Suite](http://microsoft.com/oms) の Web サイトに進み、**[無料試用版]** をクリックします。Outlook.com などの Microsoft アカウント、または Office 365 または他の Microsoft サービスで使用する会社またはや教育機関の組織アカウントを使用してサインインします。
2. 一意のワークスペース名を指定します。ワークスペースは、管理データを保存する論理的なコンテナーです。ワークスペースのデータはそれに限定されたデータなので、これでは、組織のチームごとにデータを分割できるようになります。電子メール アドレスとデータを格納する領域を指定します。![ワークスペースを作成し、サブスクリプションをリンクする](./media/operational-insights-onboard-in-minutes/create-workspace-link-sub.png)
3. 次いで、新しい Azure サブスクリプションを作成するか、既存の Azure サブスクリプションにリンクします。無料評価版を使用して実行するには、**[後で]** をクリックします。

これで Operations Management Suite ポータルを使用開始する準備が整いました。

Operations Management Suite で作成したワークスペースを設定して、ワークスペースに既存の Azure アカウントをリンクする方法の詳細については、「[ワークスペースの設定と設定の管理](operational-insights-setup-workspace.md)」を参照してください。

## Microsoft Azure を使用した迅速なサインアップ

1. [Azure ポータル](https://manage.windowsazure.com)に進み、サインインして、サービス一覧から、**[Operational Insights]** を選択します。![Azure ポータル](./media/operational-insights-onboard-in-minutes/azure-portal-op-insights.png)
2. **[ワークスペースの作成]**、**[簡易作成]** を順にクリックして、**[アカウント]** の下にワークスペース名を入力し、層を選択して、ワークスペースのデータを保存する場所を選択します。複数ある場合、使用するサブスクリプションをクリックし、**[ワークスペースの作成]** をクリックします。![Azure ポータル](./media/operational-insights-onboard-in-minutes/quick-create.png)
3. 作成したワークスペースを選択して、**[Operational Insights アカウントに移動します]** をクリックし、Operations Management Suite の Web サイトを開きます。![アカウントを参照する](./media/operational-insights-onboard-in-minutes/visit-account.png)
4. Operations Management Suite の Web サイトで、電子メール アドレスを入力し、**[確認して続行]** をクリックします。確認の電子メールが送信されます。電子メールを開き、中の **[今すぐ確認]** をクリックします。
5. Operations Management Suite の Web サイトに、[概要] ページが表示されます。クリックして **[開始する]** をクリックして続行します。

これで Operations Management Suite ポータルを使用開始する準備が整いました。

Operations Management Suite で作成したワークスペースを設定して、既存のワークスペースをリンクする方法の詳細については、「[ワークスペースの設定と設定の管理](operational-insights-setup-workspace.md)」を参照してください。

## Operations Management Suite ポータルの概要
ソリューションを選択して管理するサーバーに接続するには、**[開始する]** タイルをクリックし、このセクションの次の手順に従います。

![ワークスペースを作成し、サブスクリプションをリンクする](./media/operational-insights-onboard-in-minutes/get-started.png)

- 使用するソリューションを選択して、**[選択したソリューションの追加]** をクリックします。![ソリューション](./media/operational-insights-onboard-in-minutes/solutions.png)
- サーバー環境に接続し、データを収集する方法を選択します。
    - エージェントをインストールして、任意の Windows サーバーまたはクライアントに直接接続します。
    - System Center Operations Manager を使用して、管理グループまたは Operations Manager デプロイメント全体をアタッチします。
    - Windows または Linux の VM 拡張機能の Azure 診断に構成されている Azure のストレージ アカウントを使用します。
- データを入力するログを少なくとも 1 つ構成します。IIS ログを選択するかイベント ログを追加して、ページ下部の **[保存]** を選択します。イベント ログには、監視するエラー、警告、および情報などのメッセージの種類を指定できます。![ソリューション](./media/operational-insights-onboard-in-minutes/logs.png)

## 必要に応じて、エージェントをインストールして、サーバーを直接 Operations Management Suite に接続します。
1. [開始する] ビューで、**[データ ソースに接続]** ノードをクリックし、**[Windows エージェントのダウンロード]** をクリックします。エージェントは、Windows Server 2008 SP 1 以上または Windows 7 SP1 以上にのみインストールできます。サーバー アーキテクチャは x64 である必要があります。
2. エージェントを 1 つ以上のサーバーにインストールします。エージェントは 1 つずつインストールすることも、[カスタム スクリプト](operational-insights-direct-agent.md#configure-the-microsoft-monitoring-agent-optional)を使用した自動化された方法または所持している既存のソフトウェア配布ソリューションを使用する方法でインストールすることもできます。
3. 使用許諾契約書に同意しインストール フォルダーを選択したら、**[Microsoft Azure オペレーション インサイトにエージェントを接続する]** を選択します。![エージェントを設定する](./media/operational-insights-onboard-in-minutes/agent.png)
4. 次のページでは、ワークスペースの ID とワークスペース キーを求められます。エージェント ファイルをダウンロードした画面に、ワークスペース ID とキーが表示されます。![サーバーをアタッチする](./media/operational-insights-onboard-in-minutes/key.png)
5. 必要に応じて、プロキシ サーバーを設定し、認証情報を指定するには、インストール中に **[詳細設定]** をクリックします。**[次へ]** ボタンをクリックして、ワークスペースの情報画面に戻ります。
6. **[次へ]** をクリックし、ワークスペース ID およびキーを検証します。エラーが検出された場合は、**[戻る]** で修正できます。ワークスペース ID とキーが検証されたら、**[インストール]** をクリックして、エージェントのインストールを完了します。
7. Operations Management Suite ポータルに戻ってログインし、[概要] ページの **[設定]** タイルをクリックします。エージェントが Operations Management Suite サービスと通信している際には、緑のチェック マーク アイコンが表示されます。初期状態で、これには 5 ～ 10 分かかります。

> [AZURE.NOTE]Operations Management Suite に直接接続されているサーバーの容量管理および構成の査定ソリューションは現在はサポートされていません。

エージェントを System Center Operations Manager 2012 SP1 以降に接続することもできます。これを行うには、**[エージェントを System Center Operations Manager に接続する]** を選択します。このオプションを選択すると、追加のハードウェアなしに、または管理グループをロードせずに、サービスにデータを送信できます。

Operations Management Suite に直接エージェントを接続する方法の詳細については、「[オペレーション インサイトにコンピューターを直接接続する](operational-insights-direct-agent.md)」を参照してください。

## オプションでの System Center Operations Manager を使用したサーバーの接続

1. Operations Manager コンソールで、**[管理]** をクリックします。
2. **[オペレーション インサイト]** ノードを展開して、**[オペレーション インサイトの接続]** を選択します。
3. 右上の **[Operational Insights に登録する]** リンクをクリックして、指示に従います。
4. 登録ウィザードを完了したら、**[コンピューター/グループの追加]** リンクをクリックします。
5. **[コンピューターの検索]** ダイアログ ボックスでは、Operations Manager で監視するコンピューターまたはグループを検索できます。オペレーション インサイトに追加するコンピューターまたはグループを選択し、**[追加]**、**[OK]** の順にクリックします。Operations Management Suite ポータルの **[使用状況]** タイルに移動すると、Operational Insights サービスがデータを受信していることを確認できます。データは 5 ～ 10 分で表示されます。

Operations Manager を Operations Management Suite に接続する方法の詳細については、「[System Center Operations Manager からオペレーション インサイトに接続する](operational-insights-connect-scom.md)」を参照してください。

## オプションで Microsoft Azure でクラウド サービスのデータを分析する

Operations Management Suite では、Azure Cloud Services の診断を有効にすることで、クラウド サービスや仮想マシンのイベントおよび IIS ログを迅速に検索できます。また、Microsoft Monitoring Agent をインストールすることによって、Azure Virtual Machines の情報を追加で得ることもできます。Operations Management Suite を使用するように Azure 環境を構成する方法の詳細については、「[Microsoft Azure でサーバーからデータを分析する](operational-insights-analyze-data-azure.md)」を参照してください。


## 次のステップ
- [ソリューション](operational-insights-solutions.md)の使用を開始します。
- [検索](operational-insights-search.md)について理解します。
- カスタム検索結果を保存および表示するために[ダッシュボード](operational-insights-use-dashboards.md)を使用します。

<!---HONumber=Sept15_HO3-->