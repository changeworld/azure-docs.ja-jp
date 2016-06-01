<properties 
	pageTitle="Azure App Service の Web アプリをスケーリングする" 
	description="Azure App Service での、自動スケールを含む Web アプリのスケール アップとスケール ダウンの方法について説明します。" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="cephalin"/>

# Azure App Service の Web アプリをスケーリングする #

Microsoft Azure での Web アプリのスループットとパフォーマンスを高めるために、[Azure ポータル](http://portal.azure.com)を使用して [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) プランを**Free** モードから**Shared**、**Basic**、**Standard**、または **Premium** モードに拡張できます。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Azure で Web アプリをスケール アップするには、2 つの操作が必要です。App Service プランのモードを上位レベルに変更することと、上位レベルのサービスに切り替えた後、特定の設定を構成することです。この記事では、この 2 つのトピックについて説明します。**Standard** モードや **Premium** モードのようにサービス レベルを高くすると、Azure 上のリソースの使用方法を決定する際の堅牢性と柔軟性が向上します。

スケール設定は適用に数秒を要するのみで、App Service プランに含まれるすべての Web アプリに反映されます。コードの変更やアプリケーションの再デプロイは必要ありません。

App Service プランの詳細については、「[App Service プラン](../app-service/app-service-how-works-readme.md)」および「[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。App Service の個々のプランの料金と機能の詳細については、「[App Service の料金の詳細](/pricing/details/web-sites/)」を参照してください。

> [AZURE.NOTE] Web アプリを **Free** モードから **Basic**、**Standard**、または **Premium** モードに切り替える前に、Azure App Service のサブスクリプションに設定されている使用制限を解除する必要があります。Microsoft Azure App Service サブスクリプションのオプションを表示または変更するには、「[Microsoft Azure サブスクリプション][azuresubscriptions]」を参照してください。

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## 共有または基本モードへの規模の変更
<!-- ===================================== -->

1. ブラウザーで、[Azure ポータル][portal]を開きます。
	
2. Web アプリのブレードで、**[すべての設定]**、**[スケールアップする]** の順にクリックします。
	
	![プランの選択][ChooseWHP]
	
4. **[価格レベルの選択]** ブレードで、**Shared** または **Basic** モードを選択して、**[選択]** をクリックします。
	
	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。
	
5. 設定で **[スケールアウトする]** をクリックし、ドロップダウン リストで *[手動で選択するインスタンス数]*を選択し、**[インスタンス]** バーを左から右にスライドさせてインスタンス数を増やしてから、コマンド バーで **[保存]** をクリックします。**Shared** モードでは、インスタンス サイズのオプションは利用できません。これらのインスタンス サイズの詳細については、「[App Service の価格][vmsizes]」を参照してください。
	
	![基本モードのインスタンス サイズ][ChooseBasicInstances]
	
	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## 標準またはプレミアム モードへの規模の変更
<!-- ================================= -->

> [AZURE.NOTE] App Service プランを **Standard** または **Premium** モードに切り替える前に、Microsoft Azure App Service のサブスクリプションに設定されている使用制限を解除する必要があります。解除しないと、請求期間が終了する前に使用制限に達した場合に、Web アプリが使用できなくなるおそれがあります。Microsoft Azure App Service サブスクリプションのオプションを表示または変更するには、「[Microsoft Azure サブスクリプション][azuresubscriptions]」を参照してください。

1. **Standar** または **Premium** モードに拡張するには、**Shared** または **Basic** モードに拡張するときと同じ初期手順を実行し、その後、**[価格レベルを選択]** で **Standard** または **Premium** モードを選択して、**[選択]** をクリックします。 
	
	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅し、**自動スケール**が有効になります。
	
	![Standard または Premium モードへの拡張][ScaleStandard]
	
	この場合も、上の **Basic** モードの場合と同様に、**インスタンス** バーをスライドして手動でインスタンス数を増やすことができます。ただし、ここではアプリの自動スケールの使用方法について説明します。
	
2. **[スケールの基準]** で、**[スケジュールおよびパフォーマンスのルール]** を選択し、アプリを自動スケールします。
	
	![自動スケール モードがパフォーマンスに設定されています][Autoscale]
	
3. **[設定]** で、**[既定、スケール 1-1]** をクリックし、2 つのスライダーを移動して、App Service 用に自動スケールを設定するインスタンスの最小数と最大数を定義します。このチュートリアルでは、最大のスライダーを **6** インスタンスに移動します。
	
4. **[OK]** をクリックします。
	
4. **[設定]** で、**[CPU の割合] > [80 (カウント数を 1 ずつ増加)]** をクリックして、既定のメトリックの自動スケール規則を構成します。
	
	![ターゲット メトリックの設定][SetTargetMetrics]
	
	CPU、メモリ、ディスク キュー、HTTP キュー、データ フローなど、さまざまなパフォーマンス メトリックの自動スケール規則を構成することができます。ここでは、次のように CPU の割合の自動スケールを構成します。
	
	- 過去 10 分間の CPU が 80% を超える場合は、インスタンスを 1 増やす
	- 過去 5 分間の CPU が 90% を超える場合は、インスタンスを 3 増やす
	- 過去 30 分間の CPU が 50% 未満の場合は、インスタンスを 1 減らす 
	
	
4. **[メトリック名]** ドロップダウンは **[CPU の割合]** のままにしておきます。
	
5. **[スケール アップ規則]** で、**[演算子]** を **[より大きい]**、**[しきい値]** を **70** (%)、 **[期間]** を **10** (分)、**[集計時間]** を[平均]、**[アクション]** を **[カウント数増加の基準]**、[値] を **1** (インスタンス)、**[クール ダウン]** を **10** (分) に設定して、最初の規則を構成します。
	
	![1 つ目の自動スケール ルールの設定][SetFirstRule]
	
	>[AZURE.NOTE] **[クール ダウン]** の設定は、直前のスケール操作が実行された後、再度スケール操作を実行するまでに、この規則が待機する時間の長さを指定します。
	
6. **[規則の追加]** をクリックし、**[演算子]** を **[より大きい]**、**[しきい値]** を **90** (%)、 **[期間]** を **1** (分)、**[集計時間]** を[平均]、**[アクション]** を **[カウント数増加の基準]**、**[値]** を **3** (インスタンス)、**[クール ダウン]** を **1** (分) に設定して、2 番目の規則を構成します。

7. **[OK]** をクリックします。
	
	![2 つ目の自動スケール ルールの設定][SetSecondRule]
	
5. **[設定]**で、 **[規則の追加]** をクリックし、**[演算子]** を **[より小さい]**、**[しきい値]** を **50** (%)、 **[期間]** を **30** (分)、**[集計時間]** を**[平均]**、**[アクション]** を **[カウント数減少の基準]**、**[値]** を **1** (インスタンス)、**[クール ダウン]** を **60** (分) に設定して、3 番目の規則を構成します。
	
	![3 つ目の自動スケール ルールの設定][SetThirdRule]
	
7. **[OK]** をクリックします。これで、自動スケール規則が **[スケール設定]** ブレードに反映されます。
	
	![自動スケール ルール設定の結果][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Web アプリに接続されている SQL Server データベースのスケール設定
(App Service のプラン モードにかかわらず) 1 つ以上の SQL Server データベースが Web アプリにリンクされている場合、ニーズに合わせてすばやくスケールを設定できます。

1. リンクされたデータベースのいずれかを拡張するには、[Azure ポータル][portal] で Web アプリのブレードを開きます。折りたたみ可能な **[Essentials]** ドロップダウンで、**[リソース グループ]** リンクをクリックします。次に、リソース グループ ブレードの **[概要]** パートで、リンクされているデータベースのいずれかをクリックします。

	![リンクされたデータベース][ResourceGroup]
	
2. リンクされている SQL Database のブレードで **[設定]**、**[価格レベル]** パートの順にクリックし、パフォーマンス要件に応じていずれかの価格レベルを選択して、**[選択]** をクリックします。
	
	![SQL Database のスケーリング][ScaleDatabase]
	
3. また、geo レプリケーションを設定して、SQL Database の高可用性と障害復旧機能を向上させることもできます。そのためには、**[Geo レプリケーション]** パートをクリックします。
	
	![SQL Database の geo レプリケーションの設定][GeoReplication]

<a name="devfeatures"></a>
## 開発者機能
Web アプリのモードに応じて、次の開発者向け機能を使用できます。

### ビット ###

- **Basic**、**Standard**、および **Premium** モードでは、64 ビットおよび 32 ビットのアプリケーションがサポートされます。
- **Free** プラン モードと **Shared** プラン モードでは、32 ビットのアプリケーションのみがサポートされます。

### デバッガー サポート ###

- **Free**、**Shared**、および **Basic** モードでは、デバッガー サポートを利用する場合、App Service プランあたりの同時接続数は 1 です。
- **Standard** および **Premium** モードでは、デバッガー サポートを利用する場合、App Service プランあたりの同時接続数は 5 です。

<a name="OtherFeatures"></a>
## その他の機能

### Web エンドポイントの監視 ###

- Web エンドポイントの監視機能は、**Basic**、**Standard**、および **Premium** モードで利用できます。Web エンドポイントの監視の詳細については、「[Web Apps の監視方法](web-sites-monitor.md)」を参照してください。

- すべてのユーザー (開発者を含む) が関心を持つ料金や機能など、App Service プランのその他すべての機能の詳細については、「[App Service の料金の詳細](/pricing/details/web-sites/)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="Next Steps"></a>
## 次のステップ

- Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト](/pricing/free-trial/)」を参照してください。
- 料金、サポート、および SLA については、次のリンクを参照してください。
	
	[データ転送の料金詳細](/pricing/details/data-transfers/)
	
	[Microsoft Azure サポート プラン](/support/plans/)
	
	[サービス レベル アグリーメント](/support/legal/sla/)
	
	[SQL Database の料金詳細](/pricing/details/sql-database/)
	
	[Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ][vmsizes]
	
	[App Service の料金の詳細](/pricing/details/web-sites/)
	
	[App Service の料金の詳細 - SSL 接続](/pricing/details/web-sites/#ssl-connections)

- 拡張性と回復力に優れたアーキテクチャの構築など、Azure App Service のベスト プラクティスについては、「[Azure App Service Web Apps のベスト プラクティス](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)」を参照してください。

- Web Apps の拡張に関するビデオ:
	
	- [Azure Websites のスケールを設定するタイミング - Stefan Schackow 共演](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Azure Websites のスケールを設定する方法 - Stefan Schackow 共演](/documentation/videos/how-azure-web-sites-scale/)

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。

<!-- LINKS -->
[vmsizes]: /pricing/details/app-service/
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
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
 

<!---HONumber=AcomDC_0518_2016-->