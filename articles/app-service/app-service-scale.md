<properties 
	pageTitle="Azure App Service における価格レベルのスケール" 
	description="Azure App Service で Web Apps、Mobile Apps、API Apps、Logic Apps をスケールする方法 (自動スケールを含む) について説明します。" 
	services="app-service" 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="stepsic"/>

# Azure App Service における価格レベルのスケール

Microsoft Azure でのアプリのスループットとパフォーマンスを高めるために、[Azure ポータル](https://portal.azure.com/)を使用して App Service プランを **Free** から **Shared**、**Basic**、**Standard**、または **Premium** にスケールアップできます。

App Service プランに含まれているサービスのレベルは、プランの[*価格レベル*に基づいています。](/pricing/details/app-service/)**Standard** や **Premium** のように価格レベルを高くすると、Azure 上のリソースの使用方法を決定する際の堅牢性と柔軟性が向上します。価格レベルを変更すると、サービスに含まれるコア数とメモリ容量が影響を受けます。これを*スケールアップ* (または*スケールダウン*) と呼びます。

価格レベルのスケールアップ以外にも、サービスに含まれるインスタンス数を増やすことができます。これを*スケールアウト*または*スケールイン*と呼びます。*スケールアウト*と*スケールイン*の詳細については、[インスタンス数を手動または自動でスケールする方法](../insights-how-to-scale.md)に関する記事を参照してください。

App Service プランの詳細については、[App Service プラン](../web-sites-web-hosting-plan-overview.md)に関するページおよび「[Azure App Service プランの詳細な概要](azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。App Service の個々のプランの価格と機能の詳細については、[App Service の価格の詳細](/pricing/details/app-service/)に関するページを参照してください。

専用の [App Service 環境](app-service-app-service-environment-intro.md)を使用する場合、スケーリングの動作は異なります。詳細については、「[App Service 環境内での Web アプリのスケーリング](app-service-web-scale-a-web-app-in-an-app-service-environment.md)」を参照してください。

> [AZURE.NOTE]**Free** モードから **Basic**、**Standard**、または **Premium** モードに切り替える前に、Azure App Service のサブスクリプションに設定されている使用制限を解除する必要があります。Microsoft Azure App Service サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプション][azuresubscriptions]に関するページを参照してください。

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## 価格レベルのスケーリング

1. ブラウザーで、[Azure ポータル][portal]を開き、スケールする必要のあるアプリを参照します。
	
2. アプリの **[Essentials]** で、**[App Service プラン/価格レベル]** リンクをクリックします。

3. **[価格レベル]** をクリックすると、プランに用意されているサービス レベルの一覧が表示されます。各レベルには、そのレベルの平均コストを把握できるように、見積もり価格も表示されます。
	
	![プランの選択](./media/app-service-scale/ChoosePricingTier.png)
	
4. レベルを選択したら、**[選択]** をクリックします。
	
	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。
 
Azure に存在する各種コンピューティング階層については、[こちら](http://go.microsoft.com/fwlink/?LinkId=309169)をご覧ください。
	
<a name="ScalingSQLServer"></a>
##スケーリングに関連するリソース
アプリが SQL や Storage などの他のサービスに依存している場合は、これらもニーズに合わせてスケールできます。

1. **[Essentials]** で、**[リソース グループ]** リンクをクリックします。

2. 次に、リソース グループ ブレードの **[概要]** パートで、データベースのいずれか (またはスケールする他のリソース) をクリックします。

	![リンクされたデータベース](./media/app-service-scale/ResourceGroup.png)
	
3. リンクされているリソースのブレードで **[価格レベル]** パートをクリックし、パフォーマンス要件に応じていずれかの価格レベルを選択して、**[選択]** をクリックします。
	
	![SQL Database のスケール](./media/app-service-scale/ScaleDatabase.png)
	
4. アプリで Storage を使用する場合、Storage をサポートしている価格レベルを選択すると、geo レプリケーションが自動的に設定します。これに対して、SQL を使用する場合は、SQL Database の高可用性と障害復旧機能を向上させるために、geo レプリケーションを手動で構成する必要があります。そのためには、**[geo レプリケーション]** パートをクリックします。
	
	![SQL Database の geo レプリケーションの設定](./media/app-service-scale/GeoReplication.png)
	
<a name="devfeatures"></a>
## 開発者機能
価格レベルに応じて、次の開発者向け機能を使用できます。

### ビット ###

- **Basic**、**Standard**、および **Premium** レベルでは、64 ビットおよび 32 ビットのアプリケーションがサポートされます。
- **Free** プラン レベルと **Shared** プラン レベルでは、32 ビットのアプリケーションのみがサポートされます。

### デバッガー サポート ###

- **Free**、**Shared**、および **Basic** モードでは、デバッガー サポートを利用する場合、App Service プランあたりの同時接続数は 1 です。
- **Standard** および **Premium** モードでは、デバッガー サポートを利用する場合、App Service プランあたりの同時接続数は 5 です。

<a name="OtherFeatures"></a>
## その他の機能

- すべてのユーザー (開発者を含む) が関心を持つ価格や機能など、App Service プランのその他すべての機能の詳細については、[App Service の価格の詳細](/pricing/details/web-sites/)に関するページを参照してください。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="Next Steps"></a>
## 次のステップ

- Azure を利用し始めるには、[Microsoft Azure の無料評価版](/pricing/free-trial/)のページを参照してください。
- 価格、サポート、および SLA については、次のリンクを参照してください。
	
	[データ転送の価格詳細](/pricing/details/data-transfers/)
	
	[Microsoft Azure サポート プラン](/support/plans/)
	
	[サービス レベル アグリーメント](/support/legal/sla/)
	
	[SQL Database の価格詳細](/pricing/details/sql-database/)
	
	[Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ][vmsizes]
	
	[App Service の価格の詳細](/pricing/details/app-service/)
	
	[App Service の価格の詳細 - SSL 接続](/pricing/details/web-sites/#ssl-connections)

- スケーラブルで回復力に優れたアーキテクチャの構築など、Azure App Service のベスト プラクティスについては、「[Azure App Service Web Apps のベスト プラクティス](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)」を参照してください。

- Web Apps の拡張に関するビデオ:
	
	- [Azure Websites のスケールを設定するタイミング - Stefan Schackow 共演](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Azure Websites のスケールを設定する方法 - Stefan Schackow 共演](/documentation/videos/how-azure-web-sites-scale/)

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)をご覧ください。

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

<!---HONumber=July15_HO4-->