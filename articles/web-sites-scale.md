<properties 
	pageTitle="Azure App Service での Web アプリの拡張" 
	description="Azure App Service で Web アプリをスケール アップまたはスケール アウトする方法について説明します (自動スケーリングを含む)。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Azure App Service での Web アプリの拡張 #

Microsoft Azure での Web アプリのスループットとパフォーマンスを高めるために、 [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用して [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) プランを**無料**モードから**共有**、**基本**、**標準**、または**プレミアム** モードに拡張できます。 

Azure で Web アプリをスケール アップするには、2 つの操作が必要です。App Service プランのモードを上位レベルに変更することと、上位レベルのサービスに切り替えた後、特定の設定を構成することです。この記事では、この 2 つのトピックについて説明します。**標準**モードや**プレミアム** モードのようにサービス レベルを高くすると、Azure 上のリソースの使用方法を決定する際の堅牢性と柔軟性が向上します。

モードの変更と構成は、管理ポータルの [規模の設定] タブで簡単に実行できます。必要に応じて、規模の拡大または縮小が可能です。これらの変更は適用に数秒を要するのみで、App Service プランに含まれるすべての Web アプリに反映されます。コードの変更やアプリケーションの再展開は必要ありません。

App Service プランに関する詳細については、「[What is an App Service Plan? (App Service プランとは)]」(web-sites-web-hosting-plan-overview.md) および「[Azure App Service Plans In-Depth Overview (Azure App Service プランの詳しい概要)](azure-web-sites-web-hosting-plans-in-depth-overview.md).」を参照してください。各 App Service プランの特徴と料金については、「[App Service Pricing Details (App Service の料金詳細)](/pricing/details/web-sites/).  」を参照してください。

> [AZURE.NOTE] Web アプリを**無料**モードから**基本**、**標準**、または**プレミアム** モードに切り替える前に、Azure App Service のサブスクリプションに設定されている使用制限を解除する必要があります。Microsoft Azure App Service サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプションに関するページ][azuresubscriptions]を参照してください。

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## 共有または基本モードへの規模の変更
<!-- ===================================== -->

1. ブラウザーで、[Azure ポータル][portal]を開きます。
	
2. Web アプリのブレードで、**[すべての設定]** をクリックし、**[スケール]** をクリックした後、**[無料プランからのアップグレードしてインスタンスを追加し、パフォーマンスを向上させる]** をクリックします。
	
	![プランを選択する][ChooseWHP]
	
4. **[価格レベルを選択]** ブレードで、**[共有]** または **[基本]** モードを選択し、**[選択]** をクリックします。
	
	操作が完了すると、**[通知]** タブで緑色の「**成功**」という文字が点滅します。 
	
5. **[インスタンス]** バーを左から右にスライドしてインスタンスの数を増やし、コマンド バーの **[保存]** をクリックします。**共有**モードでは、インスタンス サイズのオプションは利用できません。これらのインスタンス サイズの詳細については、「[Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)][vmsizes]」を参照してください。
	
	![基本モードのインスタンス サイズ][ChooseBasicInstances]
	
	操作が完了すると、**[通知]** タブで緑色の「**成功**」という文字が点滅します。 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## 標準またはプレミアム モードへの規模の変更
<!-- ================================= -->

> [AZURE.NOTE] App Service プランを**標準**または**プレミアム** モードに切り替える前に、Microsoft Azure App Service のサブスクリプションに設定されている使用制限を解除する必要があります。解除しないと、請求期間が終了する前に使用制限に達した場合に、Web アプリが使用できなくなるおそれがあります。Microsoft Azure App Service サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプションに関するページ][azuresubscriptions]を参照してください。

1. **標準**または**プレミアム** モードに拡張するには、**共有**または**基本** モードに拡張するときと同じ初期手順を実行し、その後、**[価格レベルを選択]** で**標準**または**プレミアム** モードを選択して、**[選択]** をクリックします。 
	
	操作が完了すると、**[通知]** タブで緑色の「**成功**」という文字が点滅し、**自動スケール モード**が有効になります。
	
	![標準またはプレミアム モードへの拡張][ScaleStandard]
	
	**[インスタンス]** バーをスライドして、インスタンス数を手動で拡張することもできます (先述の**基本**モードと同様)。ただし、ここでは**自動スケール モード**の使用方法について説明します。 
	
2. **自動スケール モード**では、**[パフォーマンス]** を選択し、パフォーマンス メトリックに基づいて自動スケールを行います。
	
	![自動スケール モードがパフォーマンスに設定されています][Autoscale]
	
3. **[インスタンス範囲]** で、2 つのスライダーを動かしてインスタンス数の最小値と最大値を定義し、App Service プランの規模を自動的に設定します。このチュートリアルでは、最大値のスライダーを **6** インスタンスに設定します。
	
4. コマンド バーで **[保存]** をクリックします。
	
4. **[ターゲット メトリック]** で **[>]** をクリックして、ターゲット メトリックの自動スケーリング ルールを構成します。  
	
	![ターゲット メトリックの設定][SetTargetMetrics]
	
	自動スケーリング ルールはさまざまなパフォーマンス メトリックについて構成できます (CPU、メモリ、ディスクのキュー、HTTP キュー、データ フローなど)。ここでは、CPU の使用率について次の自動スケーリングを構成します。
	
	- 過去 10 分間の CPU 使用率が 70% を超えている場合は、インスタンス数を 1 つスケール アップする
	- 過去 5 分間の CPU 使用率が 90% を超えている場合は、インスタンス数を 3 つスケール アップする
	- 過去 30 分間の CPU 使用率が 50% 未満の場合は、インスタンス数を 1 つスケール ダウンする 
	
	
4. **[メトリック]** ドロップダウンは **[CPU の割合]** のままにします。
	
5. **[スケール アップ ルール]** で、1 つ目のルールを次のように設定します:  **[条件]** は **[より大きい]**、**[しきい値]** は「**70**」(%) 、**[過去]** は「**10**」(分間) 、**[スケール アップ数]** は「**1**」(インスタンス)、**[クール ダウン]** は「**10**」(分間) 。 
	
	![1 つ目の自動スケール ルールの設定][SetFirstRule]
	
	>[AZURE.NOTE] **クール ダウン**設定とは、直前のスケール アクションが実施された後、もう一度スケール変更を実施するまでにこのルールが待機する必要のある時間の長さを指定するものです。
	
6. **[スケール アップ ルールの追加]** をクリックし、2 つ目のルールを次のように設定します:  **[条件]** は **[より大きい]**、**[しきい値]** は「**90**」(%) 、**[過去]** は「**1**」(分間) 、**[スケール アップ数]** は「**3**」(インスタンス)、**[クール ダウン]** は「**1**」(分間) 。
	
	![2 つ目の自動スケール ルールの設定][SetSecondRule]
	
5. **[スケール ダウン ルール]** で、3 つ目のルールを次のように設定します:  **[条件]** は **[より小さい]**、**[しきい値]** は「**50**」(%) 、**[過去]** は「**30**」(分間) 、**[スケール ダウン数]** は「**1**」(インスタンス)、**[クール ダウン]** は「**60**」(分間) 。 
	
	![3 つ目の自動スケール ルールの設定][SetThirdRule]
	
7. コマンド バーで **[保存]** をクリックします。自動スケール ルールが **[スケール]** ブレードに反映されます。 
	
	![自動スケール ルール設定の結果][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Web アプリに接続されている SQL Server データベースの規模の変更
Web アプリに 1 つ以上の SQL Server データベースがリンクされている場合は (App Service プランのモードにかかわらず)、ニーズに合わせてそれらをすばやく拡張できます。

1. リンクされたデータベースのいずれかを拡張するには、[Azure ポータル][portal]で Web アプリのブレードを開きます。折りたたみ可能な **[Essentials]**ドロップダウン リストで、**[リソース グループ]** リンクをクリックします。次に、リソース グループ ブレードの **[サマリー]**で、リンクされたデータベースのいずれかをクリックします。

	![リンクされたデータベース][ResourceGroup]
	
2. リンクされた SQL データベースのブレードで **[価格レベル]** をクリックし、パフォーマンス要件に基づいていずれかのレベルを選択して、**[選択]** をクリックします。 
	
	![SQL データベースの拡張][ScaleDatabase]
	
3. ジオ レプリケーションを設定して、SQL データベースの高可用性や災害復旧機能を向上させることもできます。これを行うには、 **[ジオ (主要地域) レプリケーション]** をクリックします。
	
	![SQL データベースのジオ レプリケーションの設定][GeoReplication]

<a name="devfeatures"></a>
## 開発者機能
Web アプリのモードに応じて、次の開発者向け機能を使用できます。

### ビット ###

- **基本**、**標準**、および**プレミアム** モードでは、64 ビットおよび 32 ビットのアプリケーションがサポートされます。
- **無料**および**共有**プラン モードでは、32 ビットのアプリケーションのみがサポートされます。

### デバッガー サポート ###

- **無料**、**共有**、および**基本**モードでは、デバッガー サポートを利用する場合、App Service プランあたりの同時接続数は 1 です。
- **標準**および**プレミアム** モードでは、デバッガー サポートを利用する場合、App Service プランあたりの同時接続数は 5 です。

<a name="OtherFeatures"></a>
## その他の機能

### Web エンドポイントの監視 ###

- Web エンドポイントの監視機能は、**基本**、**標準**、および**プレミアム** モードで利用できます。Web エンドポイントの監視の詳細については、「[How to Monitor Web Apps (Web アプリの監視方法)](web-sites-monitor.md).」を参照してください。

- すべてのユーザー (開発者を含む) が関心のある料金や機能など、App Service プランのその他すべての機能の詳細については、「[App Service Pricing Details (App Service の料金詳細)](/pricing/details/web-sites/).」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Try App Service (App Service を試す)](http://go.microsoft.com/fwlink/?LinkId=523751)」にアクセスすれば、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="Next Steps"></a>	
## 次のステップ

- Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト](/pricing/free-trial/).」を参照してください。
- 料金、サポート、および SLA については、次のリンクを参照してください。
	
	[データ転送の料金詳細](/pricing/details/data-transfers/)
	
	[Azure のサポート プラン](/support/plans/)
	
	[サービス レベル アグリーメント](/support/legal/sla/)
	
	[SQL データベースの料金詳細](/pricing/details/sql-database/)
	
	[Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)][vmsizes]
	
	[App Service Pricing Details (App Service の料金詳細)](/pricing/details/web-sites/)
	
	[App Service Pricing Details - SSL Connections (App Service の料金詳細 - SSL 接続)](/pricing/details/web-sites/#ssl-connections)

- スケーラビリティと回復力に優れたアーキテクチャの構築など、Azure App Service におけるベスト プラクティスについては、「[Best Practices:Azure App Service Web Apps (ベスト プラクティス: Azure App Service Web Apps)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)」を参照してください。

- Web アプリの拡張に関するビデオ:
	
	- [Azure Websites のスケールを設定するタイミング - Stefan Schackow 共演](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Azure Websites のスケールを設定する方法 - Stefan Schackow 共演](/documentation/videos/how-azure-web-sites-scale/)

## 変更に関する情報
* Websites から App Service への変更に関するガイドは、次を参照してください:[Azure App Service and Its Impact on Existing Azure Services (Azure App Service と既存の Azure サービス)](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更に関するガイドは、次を参照してください:[Reference for navigating the preview portal (プレビュー ポータルの移動に関するリファレンス)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!--HONumber=49-->