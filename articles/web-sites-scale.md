<properties 
	pageTitle="Web サイトの規模の設定方法" 
	description="必須" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Web サイトの規模の設定方法 #

Microsoft Azure 上の Web サイトのパフォーマンスおよびスループットを向上させるために、Azure 管理ポータルを使用して、Web ホスティング プランのモードを無料から共有、基本、または標準に変更できます。 

Azure Websites の規模を拡大するには、Web ホスティング プランのモードをより高いレベルのサービスに変更することと、高いレベルのサービスに切り替えた後に特定の設定を構成することの 2 つの操作が必要になります。この記事では、この 2 つのトピックについて説明します。標準モードのようにサービス レベルを高くすると、Azure 上のリソースの使用方法を決定する際の堅牢性と柔軟性が向上します。

モードの変更と構成は、管理ポータルの [規模の設定] タブで簡単に実行できます。必要に応じて、規模の拡大または縮小が可能です。これらの変更は適用に数秒を要するのみで、Web ホスティング プランに含まれるすべての Web サイトに反映されます。コードの変更やアプリケーションの再展開は必要ありません。

Web ホスティング プランについては、[Web ホスティング プランとは](http://azure.microsoft.com/documentation/articles/web-sites-web-hosting-plan-overview/) と [Azure Websites の Web ホスティング プランの詳細情報](http://www.azure.microsoft.com/ja-jp/Documentation/Articles/azure-web-sites-web-hosting-plans-in-depth-overview/) をご覧ください。各 Web ホスティング プランの料金や機能については、[Websites の料金詳細](http://azure.microsoft.com/pricing/details/web-sites/) をご覧ください。  

> [AZURE.NOTE] Web サイトの Web ホスティング プランのモードを**無料**から**基本**または**標準**に切り替える前に、まず Azure Websites サブスクリプションに設定されている使用制限を解除する必要があります。Microsoft Azure Websites サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプション][azuresubscriptions] に関するページをご覧ください。

この記事の内容:

- [共有または基本プラン モードへの規模の変更](#scalingsharedorbasic)
- [標準プラン モードへの規模の変更](#scalingstandard)
- [サイトに接続されている SQL Server データベースの規模の変更](#ScalingSQLServer)
- [開発者機能](#devfeatures)
- [その他の機能](#OtherFeatures)

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## 共有または基本プラン モードへの規模の変更
<!-- ===================================== -->

1. ブラウザーで、[管理ポータル][portal] を開きます。
	
2. **[Web サイト]** タブで、Web サイトを選択します。
	
	![Selecting a website][SelectWebsite]
	
3. **[規模の設定]** タブをクリックします。
	
	![The scale tab][SelectScaleTab]
	
4. **[Web ホスティング プランのモード]** セクションで、**[共有]** または **[基本]** を選択します。図の例では、[基本] を選択しています。
	
	![Choose Web Hosting Plan][ChooseWHP]
	
	**[Web ホスティング プラン サイト]** セクションには、現在のプランに含まれるサイトの簡単な一覧が表示されます。現在のプランに含まれるすべてのサイトは、選択した Web ホスティング プランのモードに変更されます。
	
5. **[容量]** セクションで **[インスタンス サイズ]** を選択します。**[S]**、**[M]**、または **[L]** のオプションを使用できます。共有モードでは、インスタンス サイズのオプションは利用できません。これらのインスタンス サイズの詳細については、[Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)][vmsizes] をご覧ください。
	
	![Instance size for Basic mode][ChooseBasicInstanceSize]
	
6. スライダーを使用して、**[インスタンス数]** で必要な数を選択します。
	
	![Instance count for Basic mode][ChooseBasicInstanceCount]
	
7. コマンド バーで **[保存]** を選択します。 
	
	![Save button][SaveButton]
 	
	> [AZURE.NOTE] 必要に応じて、**[Web ホスティング プラン]**、**[インスタンス サイズ]**、および **[インスタンス数]** の設定を個別に構成して保存できます。
	
8. 確認メッセージにより、現在の Web サイトと同じ Web ホスティング プランに含まれるサイトも新しいモードに変更されることが通知されます。**[はい]** を選択して変更を完了します。 
	
	この例では、プラン モードを**基本**に変更しました。
	
	![Plan change complete][BasicComplete]
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## 標準プラン モードへの規模の変更
<!-- ================================= -->

> [AZURE.NOTE] Web ホスティング プランを標準モードに切り替える前に、Microsoft Azure Websites サブスクリプションに設定されている使用制限を解除する必要があります。解除しないと、請求期間が終了する前に使用制限に達した場合に、サイトが使用できなくなるおそれがあります。Microsoft Azure Websites サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプション][azuresubscriptions] に関するページをご覧ください。

1. 規模を標準に変更するには、まず共有または基本に規模を変更する場合と同じ手順を実行し、**[Web ホスティング プランのモード]** で **[標準]** を選択します。 
	
	![Choose Standard Plan][ChooseStandard]
	
	前回と同様に、**[Web ホスティング プラン サイト]** セクションには、現在のプランに含まれるサイトの簡単な一覧が表示されます。この例では、現在のプランに含まれるすべてのサイトが標準モードに変更されます。
	
2. **[標準]** を選択すると、**[容量]** セクションが展開され、**[インスタンス サイズ]** オプションと **[インスタンス数]** オプションが表示されます。これらのオプションは基本モードでも使用できます。**[スケジュールのスケール設定の編集]** オプションと **[メトリックによるスケール]** オプションは、標準モードでのみ使用できます。
	
	![Capacity section in Standard][CapacitySectionStandard]
	
3. **[インスタンス サイズ]** を構成します。**[S]**、**[M]**、または **[L]** のオプションを使用できます。
	
	![Choose instance size][ChooseInstanceSize]
	
	これらのインスタンス サイズの詳細については、[Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)][vmsizes] をご覧ください。
	
4. 日中か夜間か、平日か週末か、または特定の日時に応じてリソースの規模を自動的に調整 (自動スケール) する場合は、**[スケジュールのスケール設定の編集]** オプションで **[スケジュール時間の設定]** を選択します。
	
	![Set up schedule times][SetUpScheduleTimesButton]
	
5. **[スケジュール時間の設定]** ダイアログ ボックスには、構成に関するいくつかの選択肢が表示されます。
	
	![SetUpScheduleTimesDialog][SetUpScheduleTimesDialog]
	
6. 日中と夜間、または週末と平日で異なるスケジュールに基づいてリソースの規模を調整するには、**[定期的なスケジュール]** で **[日中と夜間で異なるスケールを設定する]** または **[平日と週末で異なるスケールを設定する]** を選択します。
	
	> [AZURE.NOTE] この場合、金曜日の終了時刻 (既定では午後 8 時) から月曜日の開始時刻 (既定では午前 8 時) までが週末となります。いつからいつまでを週末と見なすかは、**[時間]** セクションで定義した 1 日の開始時刻と終了時刻に基づいて決定されます。
	
7. **[時間]** で、1 日の開始時刻と終了時刻を 30 分刻みで指定し、タイム ゾーンを選択します。既定では、午前 8 時から午後 8 時までが日中と見なされます。選択したタイム ゾーンの夏時間が適用されます。 
	
8. **[特定の日付]** では、リソースの規模を設定する名前付きの時間範囲を 1 つ以上作成できます。たとえば、Web トラフィックが大幅に増大する時間帯に、販売用のリソースを追加したりイベントを開始したりできます。
	
9. 変更が終わったら、**[OK]** をクリックして **[スケジュール時間の設定]** ダイアログ ボックスを閉じます。
	
10.   **[スケジュールのスケール設定の編集]** ボックスに、変更に基づいた構成可能なスケジュールまたはイベントが表示されます。スケジュールやイベントを構成するには、定期的なスケジュールまたは特定の日付から 1 つ選択します。 
	
	![Edit scale settings for schedule][EditScaleSettingsForSchedule]
	
	**[メトリックによるスケール]** と **[インスタンス数]** を使用して、選択した各スケジュールのリソースの規模を調整します。 
	
11.  負荷が変化する場合に Web サイトで使用するインスタンス数を動的に調整するには、**[CPU]** を選択して **[メトリックによるスケール]** オプションを有効にします。
	
	![Scale By Metric][ScaleByMetric]
	
	過去 1 週間に使用されたインスタンスの数がグラフに表示されます。このグラフを使用すると、規模設定のアクティビティを監視できます。
	
12. **[メトリックによるスケール]** では、**[インスタンス数]** 機能を変更して、自動規模設定に使用する仮想マシンの最小数と最大数を設定できます。設定した制限を上回ることも下回ることもありません。
	
	![Instance count][InstanceCount]
	
13. **[メトリックによるスケール]** では、**[ターゲット CPU]** オプションを有効にして CPU 使用率の範囲を指定することもできます。この範囲は、Web サイトの平均 CPU 使用率を表します。Windows Azure は、標準インスタンスの追加や削除を行うことで、Web サイトをこの範囲内に維持します。
	
€
	
	**注**:**[メトリックによるスケール]** を有効にすると、Web サイトの CPU が 5 分ごとにチェックされ、その時点で必要であればインスタンスが追加されます。CPU 使用率が低い場合、Microsoft Azure は 2 時間おきにインスタンスを削除して、Web サイトのパフォーマンスが維持されるようにします。一般に、最少インスタンス数は 1 に設定するのが適切です。ただし、Web サイトの使用率が突然急増する場合は、インスタンスの最低数がその負荷を処理するのに十分であることを確認します。たとえば、Microsoft Azure が CPU 使用率をチェックする前の 5 分間にトラフィックが突然急増した場合、その間、サイトは反応が鈍くなります。大量のトラフィックが突然発生することが想定される場合は、最小インスタンス数を多めに設定して、負荷の急増に備えます。 
	
14. **[スケジュールのスケール設定の編集]** ボックスの項目を変更したら、ページ下部のコマンド バーにある **[保存]** アイコンをクリックします。すべてのスケジュール設定が一度に保存されます (各スケジュールを個別に保存する必要はありません)。

> [AZURE.NOTE] [Azure プレビュー ポータル](https://portal.azure.com/) では、CPU 使用率だけでなく、さらにメモリの割合、ディスク キューの長さ、HTTP キューの長さ、データイン、およびデータアウトというメトリックに基づいてスケールを設定できます。また、スケーリングのカスタム コントロールをさらに充実させるための 1 つ以上のスケールアップおよびスケールダウンのルールを作成できます。詳細については、Azure プレビュー ポータルのドキュメントの [Web サイトのスケールの変更方法](http://azure.microsoft.com/documentation/articles/insights-how-to-scale/) をご覧ください。

<a name="ScalingSQLServer"></a>
##サイトに接続されている SQL Server データベースの規模の変更	
(Web ホスティング プランのモードに関係なく) 1 つ以上の SQL Server データベースを Web サイトにリンクしている場合、これらのデータベースは、[スケール] ページ下部にある **[リンク済みリソース]** セクションに表示されます。

1. 1 つのデータベースの規模を設定するには、**[リンク済みリソース]** セクションで、データベース名の横にある **[このデータベースのスケールを管理する]** リンクをクリックします。
	
	![Linked database][LinkedResources]
	
2. Azure の管理ポータルの [SQL サーバー] タブが表示されます。ここでは、データベースの **[エディション]** と **[最大サイズ]** を構成できます。
	
	![Scale your SQL Server database][ScaleDatabase]
	
	**[エディション]** で、必要なストレージ容量に応じて **[基本]**、**[標準]**、または **[プレミアム]** を選択します。今後の **[Web]** エディションと **[ビジネス]** エディションについては、[Web and Business Edition Sunset FAQ (Web およびビジネス エディションの終了に関する FAQ)](http://msdn.microsoft.com/library/azure/dn741330.aspx) をご覧ください。
	
	**[最大サイズ]** で選択した値により、データベースの上限が指定されます。データベース料金は、実際に保存するデータの量に基づいて決定されます。そのため、**[最大サイズ]** プロパティを変更するだけではデータベース料金に影響はありません。詳細については、[Accounts and Billing in Microsoft Azure SQL Database (Microsoft Azure SQL データベースのアカウントと課金)][SQLaccountsbilling] をご覧ください。

<a name="devfeatures"></a>
## 開発者機能
Web ホスティング プランのモードに応じて、次の開発者向け機能を使用できます。

**ビット数**

- 基本プラン モードと標準プラン モードでは、64 ビットおよび 32 ビットのアプリケーションがサポートされます。
- 無料プラン モードと共有プラン モードでは、32 ビットのアプリケーションのみがサポートされます。

**デバッガー サポート**

- Web ホスティング プランの無料、共有、および基本モードでは、アプリケーションあたり 1 つの同時接続でデバッガー サポートを利用できます。
- Web ホスティング プランの標準モードでは、アプリケーションあたり 5 つの同時接続でデバッガー サポートを利用できます。

<a name="OtherFeatures"></a>
## その他の機能

**Web エンドポイントの監視**

- Web エンドポイントの監視機能は、Web ホスティング プランの基本および標準モードで使用できます。Web エンドポイントの監視の詳細については、[Web サイトの監視方法](http://azure.microsoft.com/documentation/articles/web-sites-monitor/) をご覧ください。

- すべてのユーザー (開発者を含む) が関心のある料金や機能など、Web ホスティング プランのその他すべての機能の詳細については、[Websites の料金詳細](http://azure.microsoft.com/pricing/details/web-sites/) をご覧ください。

<a name="Next Steps"></a>	
## 次のステップ

- Azure を利用し始めるには、[Microsoft Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/) をご覧ください。

- 料金、サポート、および SLA については、次のリンクをご覧ください。
	
	[データ転送の料金詳細](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Azure のサポート プラン](http://azure.microsoft.com/support/plans/)
	
	[サービス レベル アグリーメント](http://azure.microsoft.com/support/legal/sla/)
	
	[SQL データベースの料金詳細](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)][vmsizes]
	
	[Websites の料金詳細](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[Websites の料金詳細 - SSL 接続](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- スケーラビリティと回復力に優れたアーキテクチャの構築など、Azure Websites におけるベスト プラクティスについては、[ベスト プラクティス: Windows Azure Websites (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx) をご覧ください。

- Azure Websites のスケールに関するビデオ: 
	
	[Azure Websites のスケールを設定する タイミング - Stefan Schackow 共演](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)

	[Azure Websites のスケールを設定する方法 - Stefan Schackow 共演](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://manage.windowsazure.com/

<!-- IMAGES -->
[SelectWebsite]: ./media/web-sites-scale/01SelectWebSite.png
[SelectScaleTab]: ./media/web-sites-scale/02SelectScaleTab.png

[ChooseWHP]: ./media/web-sites-scale/03aChooseWHP.png
[ChooseBasicInstanceSize]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
[ChooseBasicInstanceCount]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ChooseStandard]: ./media/web-sites-scale/07ChooseStandard.png
[CapacitySectionStandard]: ./media/web-sites-scale/08CapacitySectionStandard.png
[ChooseInstanceSize]: ./media/web-sites-scale/09ChooseInstanceSize.png
[SetUpScheduleTimesButton]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
[SetUpScheduleTimesDialog]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
[EditScaleSettingsForSchedule]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
[ScaleByMetric]: ./media/web-sites-scale/13ScaleByMetric.png
[InstanceCount]: ./media/web-sites-scale/14InstanceCount.png
[TargetCPU]: ./media/web-sites-scale/15TargetCPU.png
[LinkedResources]: ./media/web-sites-scale/16LinkedResources.png
[ScaleDatabase]: ./media/web-sites-scale/17ScaleDatabase.png


<!--HONumber=42-->
