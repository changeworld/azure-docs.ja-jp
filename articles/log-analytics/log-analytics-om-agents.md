<properties
	pageTitle="Operations Manager を Log Analytics に接続する | Microsoft Azure"
	description="Operations Manager と OMS ワークスペースを統合することにより、System Center Operations Manager における既存の投資を維持しながら、Log Analytics で拡張機能を使用することができます。"
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

# Operations Manager を Log Analytics に接続する

Operations Manager と OMS ワークスペースを統合することにより、System Center Operations Manager における既存の投資を維持しながら、Log Analytics で拡張機能を使用することができます。この統合により、Operations Manager を使用して以下に示す処理を引き続き行いながら、OMS によってもたらされる利点を活用することができます。

- Operations Manager による IT サービスの正常性の監視を継続する
- インシデントおよび問題の管理をサポートする ITSM ソリューションとの統合を維持する
- Operations Manager で監視するオンプレミスおよびパブリック クラウド IaaS 仮想マシンにデプロイされたエージェントのライフ サイクルを管理する

System Center Operations Manager との統合を行うと、Operations Manager でのデータの収集、格納、および分析に OMS の速度と効率の高さを生かすことで、サービス運用戦略に価値を付加することができます。OMS を利用すれば、既存の問題管理プロセスをサポートするにあたり、問題の原因の特定と再発の抽出という作業を相互に関連付けして、それらに取り組むことができます。パフォーマンス、イベント、およびアラート データを調べ、豊富なダッシュ ボードとレポート機能でそのデータをわかりやすく表示するという検索エンジンの柔軟性は、OMS が Operations Manager を補完する際にもたらす強みを説明するものです。　

Operations Manager 管理グループに報告を行うエージェントは、Log Analytics データ ソースと、OMS サブスクリプションで有効にしたソリューションに基づいて、サーバーからデータを収集します。ソリューションからのデータは、有効にしているソリューションに応じて、Operations Manager 管理サーバーから OMS Web サービスに直接送信される場合と、エージェント管理システムで収集されるデータ量の関係で、エージェントから直接 OMS Web サービスに送信される場合があります。管理サーバーは OMS データを OMS 　Web サービスに直接転送するので、OMS データが OperationsManager または OperationsManagerDW データベースに書き込まれることはありません。管理サーバーは OMS Web サービスとの接続が失われると、OMS との通信が再度確立されるまでデータをローカルのキャッシュに入れます。管理サーバーが計画的な保守または予想外の停止のためにオフラインになった場合は、管理グループ内の別の管理サーバーが OMS との接続を再開します。

次の図では、System Center Operations Manager と OMS の関係を示します。

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## システム要件
始める前に、次の詳細を確認し、必要な前提条件が満たされているか調べてください。

- OMS では、Operations Manager 2012 SP1 UR6 以上、および Operations Manager 2012 R2 UR2 以上のみをサポートします。プロキシ サポートは、Operations Manager 2012 SP1 UR7 と Operations Manager 2012 R2 UR3 に追加されています。
- OMS サブスクリプション。詳細については、「[Get started with Log Analytics (Log Analytics の概要)](log-analytics-get-started.md)」を参照してください。

## Operations Manager を OMS に接続する
次の一連の手順を実行して、OMS ワークスペースの 1 つに接続する Operations Manager 管理グループを構成します。

1. Operations Manager コンソールで、**[管理]** ワークスペースを選択します。
2. Operations Management Suite ノードを展開し、**[接続]** をクリックします。
3. **[Operations Management Suite への登録]** リンクをクリックします。
4. **[Operations Management Suite オンボード ウィザード: 認証]** ページで、電子メール アドレスまたは電話番号と、OMS サブスクリプションに関連付けられている管理者アカウントのパスワードを入力し、**[サインイン]** をクリックします。
5. 正常に認証されたら、**[Operations Management Suite オンボード ウィザード: ワークスペースの選択]** ページで、OMS ワークスペースを選択するように促されます。複数のワークスペースがある場合は、ドロップダウン リストから Operations Manager 管理グループに登録するワークスペースを選択し、**[次へ]** をクリックします。
    >[AZURE.NOTE] Operations Manager では、一度に 1 つの OMS ワークスペースのみをサポートします。このため、以前の OMS ワークスペースに登録されていた接続とコンピューターは OMS から削除されます。
6. **[Operations Manager Suite オンボード ウィザード: 概要]** ページで、設定を確認し、問題がなければ、**[作成]** をクリックします。
7. **[Operations Manager Suite オンボード ウィザード: 完了]** ページで、設定を確認し、問題がなければ、**[閉じる]** をクリックします。

### エージェントで管理されたコンピューターを追加する
OMS ワークスペースとの統合を構成しても、OMS との接続が確立するだけで、管理グループに報告を行うエージェントはデータを収集しません。このデータ収集は、Log Analytics 用のデータを収集する、特定のエージェントで管理されたコンピューターを構成してはじめて行われるようになります。コンピューター オブジェクトを個別に選択することも、Windows コンピューター オブジェクトを含むグループを選択することもできます。論理ディスクや SQL データベースなどの別のクラスのインスタンスを含むグループを選択することはできません。

1. Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。
2. Operations Management Suite ノードを展開し、**[接続]** をクリックします。
3. ウィンドウの右側の [アクション] 見出しの下にある **[コンピューター/グループの追加]** リンクをクリックします。
4. **[コンピューターの検索]** ダイアログ ボックスでは、Operations Manager で監視するコンピューターまたはグループを検索できます。OMS に追加するコンピューターまたはグループを選択し、**[追加]** をクリックしてから、**[OK]** をクリックします。

オペレーション コンソールの **[管理]** ワークスペースのOperations Management Suite の下に、管理されたコンピューター ノードからデータを収集するように構成されたコンピューターとグループが表示されます。ここからは、必要に応じて、コンピューターおよびグループの追加または削除ができるようになります。

### オペレーション コンソールで OMS プロキシ設定を構成する
内部プロキシ サーバーが管理グループと OMS Web サービスとの間に位置する場合は、次の手順を実行します。これらの設定は管理グループで一元的に管理され、OMS 用のデータを収集するスコープに含まれている、エージェントで管理されたシステムに配信されます。この仕組みは、特定のソリューションが管理サーバーをバイパスし、データを OMS Web サービスに直接送信する場合に便利です。

1. Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。
2. Operations Management Suite を展開し、**[接続]** をクリックします。
3. [OMS の接続] ビューで、**[プロキシ サーバーの構成]** をクリックします。
4. **[Operations Management Suite ウィザード: プロキシ サーバー]** ページで **[Operations Management Suite へのアクセスにプロキシ サーバーを使用する]** を選択して、ポート番号と URL を入力し (例: http://corpproxy:80)、**[完了]** をクリックします。

プロキシ サーバーで認証が必要な場合は、次の手順を実行して、管理グループ内の OMS への報告を行う管理されたコンピューターに伝達される必要がある設定と資格情報を構成します。

1. Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。
2. **[RunAs Configuration (RunAs の構成)]** で **[Profiles (プロファイル)]** を選択します。
3. **System Center Advisor Run As Profile Proxy** というプロファイルを開きます。
4. 実行プロファイル ウィザードで [追加] をクリックし、実行アカウントを使用します。新しい[実行アカウント](https://technet.microsoft.com/library/hh321655.aspx)を作成することも、既存のアカウントを使用することもできます。このアカウントには、プロキシ サーバーを通過するための十分な権限を持たせる必要があります。
5. 管理するアカウントを設定するには、**[選択したクラス、グループ、またはオブジェクト]** を選択し、**[選択]** をクリックし、**[グループ]** をクリックして、**[グループの検索]** ボックスを開きます。
6. **Microsoft System Center Advisor Monitoring Server Group** を検索して選択します。グループを選択したら、**[OK]** をクリックして、**[グループ検索]** ボックスを閉じます。
7.	**[OK]** をクリックして、**[実行アカウントの追加]** ボックスを閉じます。
8.	**[保存]** をクリックして、ウィザードを完了し、変更を保存します。

接続が作成されてから、データの収集および OMS にデータを報告するエージェントを構成すると、管理グループで次の構成が適用されます (順番どおりでなくてもかまわない)。

- 実行アカウント **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** が作成されます。これは、実行プロファイル **Microsoft System Center Advisor Run As Profile Blob** に関連付けられ、**Collection Server** および **Operations Manager Management Group** という 2 つのクラスをターゲットにします。
- 2 つのコネクタが作成されます。1 番目のコネクタは **Microsoft.SystemCenter.Advisor.DataConnector** という名前となり、管理グループ内のすべてのクラスのインスタンスから生成されたすべてのアラートを OMS Log Analytics に転送するサブスクリプションで自動的に構成されます。2 番目のコネクタは **Advisor コネクタ** であり、OMS Web サービスとの通信およびデータの共有を担当します。
- 管理グループ内でデータを収集するために選択したエージェントとグループは、**Microsoft System Center Advisor Monitoring Server Group** に追加されます。

## 管理パックの更新
構成が完了したら、Operations Manager 管理グループは OMS サービスとの接続を確立します。管理サーバーはまず Web サービスと同期し、次に有効にされたソリューションで Operations Manager と統合されるソリューションに対応する管理パックの形式で最新の構成情報を受信します。Operations Manager はこれらの管理パックが更新されているかどうかを確認し、更新がある場合はそれらを自動的にダウンロードしてインポートします。この動作は特に 2 つのルールで制御されます。

- **Microsoft.SystemCenter.Advisor.MPUpdate** - 基本的な OMS 管理パックを更新します。既定では 12 時間おきに実行されます。
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - ワークスペースで有効にされたソリューション管理パックを更新します。既定では 5 分おきに実行されます。

この 2 つのルールは上書きすることができます。具体的には、2 つのルールを無効にして自動ダウンロードを防止することも、新しい管理パックの有無とダウンロードの必要性を判断するために行う管理サーバーと OMS の同期の頻度を変更することもできます。秒単位の値で **Frequency** パラメーターを変更して同期スケジュールに変更を加える場合、または **Enabled** パラメーターを変更してルールを無効にする場合は、「[ルールまたはモニターを上書きする方法](https://technet.microsoft.com/library/hh212869.aspx)」の手順に従ってください。Operations Manager Management Group クラスのすべてのオブジェクトに対する上書きを対象としています。

引き続き既存の変更管理プロセスに従って運用管理グループにおける管理パックのリリースを制御する場合は、ルールを無効にし、更新が許可されている特定の期間中にルールを有効にすることができます。環境内に開発または QA 管理グループが存在し、インターネットに接続できる状態にある場合は、このシナリオをサポートする OMS ワークスペースでその管理グループを構成することができます。この構成では、OMS 管理パックの反復的なリリースを確認および評価してから、運用環境の管理グループにそれらをリリースすることができます。

## Operations Manager と OMS の統合を検証する
Operations Manager と OMS の統合が正常に行われているかを確認するには数種類の方法があります。

### OMS ポータルで統合を確認するには

1.	OMS ポータルで、**[設定]** タイルをクリックします。
2.	上部のメニューで、**[接続されたソース]** をクリックします。
3.	[System Center Operations Manager] セクションの下に、**[接続された管理グループは 1 つ]** という状態が表示され、その下にある表に、管理グループの名前と共に、エージェントの数、データを最後に受信したときの状態が一覧表示されます。

### オペレーション コンソールから統合を確認するには

1.	Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。
2.	**[検索対象]** ボックスの **[管理パック]** ノードをクリックし、「**Advisor**」または「**Intelligence**」と入力します。
3.	有効にしたソリューションによって、対応する管理パックが検索結果に表示されます。たとえば、アラート管理ソリューションを有効にした場合、管理パック Microsoft System Center Advisor Alert Management がリストに表示されます。

## 次のステップ

- 機能を追加し、データを収集する方法については、「[Add Log Analytics solutions from the Solutions Gallery (ソリューションギャラリーから Log Analytics ソリューションを追加する)](log-analytics-add-solutions.md)」を参照してください。
- エージェントが Log Analytics サービスと通信できるように組織がプロキシ サーバーまたはファイアウォールを使用している場合、「[Configure proxy and firewall settings in Log Analytics (Log Analytics のプロキシとファイアウォールの設定を構成する)](log-analytics-proxy-firewall.md)」を参照してください。

<!---HONumber=AcomDC_0504_2016-->