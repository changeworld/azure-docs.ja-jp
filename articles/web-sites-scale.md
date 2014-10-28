<properties title="How to Scale Websites" pageTitle="How to Scale Websites" description="required" metaKeywords="scaling Azure websites" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Web サイトの規模の設定方法

Microsoft Azure 上の Web サイトのパフォーマンスおよびスループットを向上させるために、Azure 管理ポータルを使用して、Web ホスティング プランのモードを Free から Shared、Basic、または Standard に変更できます。

Azure Websites のスケールアップには、次の関連する 2 つのアクションが必要です。Web ホスティング プランのモードをより高いレベルのサービスに変更することと、高いレベルのサービスに切り替えた後に特定の設定を構成することの 2 点です。この記事では、この 2 つのトピックについて説明します。標準モードのようにサービス レベルを高くすると、Azure 上のリソースの使用方法を決定する際の堅牢性と柔軟性が向上します。

モードの変更と構成は、管理ポータルの [規模の設定] タブで簡単に実行できます。必要に応じて、規模の拡大または縮小が可能です。これらの変更は適用に数秒を要するのみで、Web ホスティング プランに含まれるすべての Web サイトに反映されます。コードの変更やアプリケーションの再展開は必要ありません。

Web ホスティング プランについては、「[Web ホスティング プランとは][Web ホスティング プランとは]」と [Azure Websites の Web ホスティング プランの詳細情報][Azure Websites の Web ホスティング プランの詳細情報]を参照してください。各 Web ホスティング プランの料金や機能については、「[Websites の料金詳細][Websites の料金詳細]」を参照してください。

> [WACOM.NOTE] Web サイトの Web ホスティング プランのモードを **Free** から **Basic** または **Standard** に切り替える前に、まず Azure Websites サブスクリプションに設定されている使用制限を解除する必要があります。Microsoft Azure Websites サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプションに関するページ][Microsoft Azure サブスクリプションに関するページ]を参照してください。

この記事の内容:

-   [共有または基本プラン モードへの規模の変更][共有または基本プラン モードへの規模の変更]
-   [標準プラン モードへの規模の変更][標準プラン モードへの規模の変更]
-   [サイトに接続されている SQL Server データベースの規模の変更][サイトに接続されている SQL Server データベースの規模の変更]
-   [開発者機能][開発者機能]
-   [その他の機能][その他の機能]

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->

## 共有または基本プラン モードへの規模の変更

<!-- ===================================== -->

1.  ブラウザーで、[管理ポータル][管理ポータル]を開きます。

2.  **[Web サイト]** タブで、Web サイトを選択します。

    ![Web サイトの選択][Web サイトの選択]

3.  **[規模の設定]** タブをクリックします。

    ![スケール タブ][スケール タブ]

4.  **[Web ホスティング プランのモード]** セクションで、**[Shared]** または **[Basic]** を選択します。図の例では、[基本] を選択しています。

    ![Web ホスティング プランの選択][Web ホスティング プランの選択]

    **[Web ホスティング プラン サイト]** セクションには、現在のプランに含まれるサイトの簡単な一覧が表示されます。現在のプランに含まれるすべてのサイトは、選択した Web ホスティング プランのモードに変更されます。

5.  **[容量]** セクションで **[インスタンス サイズ]** を選択します。**[S]**、**[M]**、または **[L]** のオプションを使用できます。共有モードでは、インスタンス サイズのオプションは利用できません。これらのインスタンス サイズの詳細については、「[Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)][Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)]」を参照してください。

    ![基本モードのインスタンス サイズ][基本モードのインスタンス サイズ]

6.  スライダーを使用して、**[インスタンス数]** で必要な数を選択します。

    ![基本モードのインスタンス数][基本モードのインスタンス数]

7.  コマンド バーで、**[保存]** を選択します。

    ![保存ボタン][保存ボタン]

    > [WACOM.NOTE] 必要に応じて、**[Web ホスティング プラン]**、**[インスタンス サイズ]**、および **[インスタンス数]** の設定を個別に構成して保存できます。

8.  確認メッセージにより、現在の Web サイトと同じ Web ホスティング プランに含まれるサイトも新しいモードに変更されることが通知されます。**[はい]** を選択して変更を完了します。

    この例では、プラン モードを**基本**に変更しました。

    ![プランの変更が完了][プランの変更が完了]

<a name="scalingstandard"></a>
<!-- ================================= -->

## Standard プラン モードへのスケールの変更

<!-- ================================= -->

> [WACOM.NOTE] Web ホスティング プランを Standard モードに切り替える前に、Microsoft Azure Websites サブスクリプションに設定されている使用制限を解除する必要があります。解除しないと、請求期間が終了する前に使用制限に達した場合に、サイトが使用できなくなるおそれがあります。Microsoft Azure Websites サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプションに関するページ][Microsoft Azure サブスクリプションに関するページ]を参照してください。

1.  スケールを Standard に変更するには、まず Shared または Basic にスケールを変更する場合と同じ手順を実行し、**[Web ホスティング プランのモード]** で **[Standard]** を選択します。

    ![標準プランの選択][標準プランの選択]

    前回と同様に、**[Web ホスティング プラン サイト]** セクションには、現在のプランに含まれるサイトの簡単な一覧が表示されます。この例では、現在のプランに含まれるすべてのサイトが標準モードに変更されます。

2.  **[Standard]** を選択すると、**[容量]** セクションが展開され、**[インスタンス サイズ]** オプションと **[インスタンス数]** オプションが表示されます。これらのオプションは Basic モードでも使用できます。**[スケジュールのスケール設定の編集]** オプションと **[メトリックによるスケール]** オプションは、標準モードでのみ使用できます。

    ![標準モードでの 容量 セクション][標準モードでの 容量 セクション]

3.  **[インスタンス サイズ]** を構成します。**[S]**、**[M]**、または **[L]** のオプションを使用できます。

    ![インスタンス サイズの選択][インスタンス サイズの選択]

    これらのインスタンス サイズの詳細については、「[Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)][Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)]」を参照してください。

4.  日中か夜間か、平日か週末か、または特定の日時に応じてリソースの規模を自動的に調整 (自動スケール) する場合は、**[スケジュールのスケール設定の編集]** オプションで **[スケジュール時間の設定]** を選択します。

    ![スケジュール時間の設定][スケジュール時間の設定]

5.  **[スケジュール時間の設定]** ダイアログ ボックスには、構成に関するいくつかの選択肢が表示されます。

    ![スケジュール時間の設定 ダイアログ][スケジュール時間の設定 ダイアログ]

6.  日中と夜間、または週末と平日で異なるスケジュールに基づいてリソースの規模を調整するには、**[定期的なスケジュール]** で **[日中と夜間で異なるスケールを設定する]** または **[平日と週末で異なるスケールを設定する]** を選択します。

    > [WACOM.NOTE] この場合、金曜日の終了時刻 (既定では午後 8 時) から月曜日の開始時刻 (既定では午前 8 時) までが週末となります。いつからいつまでを週末と見なすかは、**[時間]** セクションで定義した 1 日の開始時刻と終了時刻に基づいて決定されます。

7.  **[時間]** で、1 日の開始時刻と終了時刻を 30 分刻みで指定し、タイム ゾーンを選択します。既定では、午前 8 時から午後 8 時までが日中と見なされます。選択したタイム ゾーンの夏時間が適用されます。

8.  **[特定の日付]** では、リソースの規模を設定する名前付きの時間範囲を 1 つ以上作成できます。たとえば、Web トラフィックが大幅に増大する時間帯に、販売用のリソースを追加したりイベントを開始したりすることができます。

9.  選択を行ったら、**[OK]** をクリックして **[スケジュール時間]** ダイアログ ボックスを閉じます。

10. **[スケジュールのスケール設定の編集]** ボックスに、変更に基づいた構成可能なスケジュールまたはイベントが表示されます。スケジュールやイベントを構成するには、定期的なスケジュールまたは特定の日付から 1 つ選択します。

    ![スケジュールのスケール設定の編集][スケジュールのスケール設定の編集]

    **[メトリックによるスケール]** と **[インスタンス数]** を使用して、選択した各スケジュールのリソースの規模を調整します。

11. 負荷が変化する場合に Web サイトで使用するインスタンス数を動的に調整するには、**[CPU]** を選択して **[メトリックによるスケール]** オプションを有効にします。

    ![メトリックによるスケール][メトリックによるスケール]

    過去 1 週間に使用されたインスタンスの数がグラフに表示されます。このグラフを使用すると、規模設定のアクティビティを監視できます。

12. **[メトリックによるスケール]** では、**[インスタンス数]** 機能を変更して、自動規模設定に使用する仮想マシンの最小数と最大数を設定できます。設定した制限を上回ることも下回ることもありません。

    ![インスタンス数][インスタンス数]

13. **[メトリックによるスケール]** では、**[ターゲット CPU]** オプションを有効にして CPU 使用率の範囲を指定することもできます。この範囲は、Web サイトの平均 CPU 使用率を表します。Windows Azure は、標準インスタンスの追加や削除を行うことで、Web サイトをこの範囲内に維持します。

    ![ターゲット CPU][ターゲット CPU]

    **メモ**:**[メトリックによるスケール]** を有効にすると、Web サイトの CPU が 5 分ごとにチェックされ、その時点で必要であればインスタンスが追加されます。CPU 使用率が低い場合、Microsoft Azure は 2 時間おきにインスタンスを削除して、Web サイトのパフォーマンスが維持されるようにします。一般に、最少インスタンス数は 1 に設定するのが適切です。ただし、Web サイトの使用率が突然急増する場合は、インスタンスの最低数がその負荷を処理するのに十分であることを確認します。たとえば、Microsoft Azure が CPU 使用率をチェックする前の 5 分間にトラフィックが突然急増した場合、その間、サイトは反応が鈍くなります。大量のトラフィックが突然発生することが想定される場合は、最小インスタンス数を多めに設定して、負荷の急増に備えます。

14. **[スケジュールのスケール設定の編集]** ボックスの項目を変更したら、ページ下部のコマンド バーにある **[保存]** アイコンをクリックします。すべてのスケジュール設定が一度に保存されます (各スケジュールを個別に保存する必要はありません)。

> [WACOM.NOTE] [Azure プレビュー ポータル][Azure プレビュー ポータル]では、CPU 使用率だけでなく、さらにメモリの割合、ディスク キューの長さ、HTTP キューの長さ、データイン、およびデータアウトというメトリックに基づいてスケールを設定することができます。また、スケーリングのカスタム コントロールをさらに充実させるための 1 つ以上のスケールアップおよびスケールダウンのルールを作成できます。詳細については、Azure プレビュー ポータルのドキュメントの「[Web サイトのスケールの変更方法][Web サイトのスケールの変更方法]」を参照してください。

<a name="ScalingSQLServer"></a>

## サイトに接続されている SQL Server データベースの規模の変更

(Web ホスティング プランのモードに関係なく) 1 つ以上の SQL Server データベースを Web サイトにリンクしている場合、これらのデータベースは、[スケール] ページ下部にある **[リンク済みリソース]** セクションに表示されます。

1.  1 つのデータベースのスケールを設定するには、**[リンク済みリソース]** セクションで、データベース名の横にある **[このデータベースのスケールを管理する]** リンクをクリックします。

    ![リンクされたデータベース][リンクされたデータベース]

2.  Azure 管理ポータルの [SQL サーバー] タブが表示されます。ここでは、データベースの **[エディション]** と **[最大サイズ]** を構成できます。

    ![SQL Server データベースの規模の設定][SQL Server データベースの規模の設定]

    **[エディション]** で、必要なストレージ容量に応じて **[Web]** または **[Business]** を選択します。**[Web]** エディションは容量が少なめで、**[Business]** エディションは容量が多めです。

    **[最大サイズ]** で選択した値により、データベースの上限が指定されます。データベース料金は、実際に保存するデータの量に基づいて決定されます。そのため、**[最大サイズ]** プロパティを変更するだけではデータベース料金に影響はありません。詳細については、「[Accounts and Billing in Microsoft Azure SQL Database (Microsoft Azure SQL データベースのアカウントと課金)][Accounts and Billing in Microsoft Azure SQL Database (Microsoft Azure SQL データベースのアカウントと課金)]」を参照してください。

<a name="devfeatures"></a>

## 開発者機能

Web ホスティング プランのモードに応じて、次の開発者向け機能を使用できます。

**ビット**

-   Basic プラン モードと Standard プラン モードでは、64 ビットおよび 32 ビットのアプリケーションがサポートされます。
-   Free プラン モードと Shared プラン モードでは、32 ビットのアプリケーションのみがサポートされます。

**デバッガー サポート**

-   Web ホスティング プランの Free、Shared、および Basic モードでは、アプリケーションあたり 1 つの同時接続でデバッガー サポートを利用できます。
-   Web ホスティング プランの Standard モードでは、アプリケーションあたり 5 つの同時接続でデバッガー サポートを利用できます。

<a name="OtherFeatures"></a>

## その他の機能

**Web エンドポイントの監視**

-   Web エンドポイントの監視機能は、Web ホスティング プランの Basic および Standard モードで使用できます。Web エンドポイントの監視の詳細については、「[Web サイトの監視方法][Web サイトの監視方法]」を参照してください。

-   すべてのユーザー (開発者を含む) が関心のある料金や機能など、Web ホスティング プランのその他すべての機能の詳細については、「[Websites の料金詳細][Websites の料金詳細]」を参照してください。

<a name="Next Steps"></a>

## 次のステップ

-   Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト][Microsoft Azure の無料評価版サイト]」を参照してください。

-   料金、サポート、および SLA については、次のリンクを参照してください。

    [データ転送の料金詳細][データ転送の料金詳細]

    [Azure のサポート プラン][Azure のサポート プラン]

    [サービス レベル アグリーメント][サービス レベル アグリーメント]

    [SQL データベースの料金詳細][SQL データベースの料金詳細]

    [Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)][Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)]

    [Websites の料金詳細][Websites の料金詳細]

    [Websites の料金詳細 - SSL 接続][Websites の料金詳細 - SSL 接続]

-   スケーラビリティと回復力に優れたアーキテクチャの構築など、Azure Websites におけるベスト プラクティスについては、「[ベスト プラクティス:Windows Azure Websites (WAWS)][ベスト プラクティス:Windows Azure Websites (WAWS)]」を参照してください。

-   Azure Websites のスケールに関するビデオ:

    [Azure Websites のスケールを設定するタイミング - Stefan Schackow 共演][Azure Websites のスケールを設定するタイミング - Stefan Schackow 共演]

    [Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演][Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演]

    [Azure Websites のスケールを設定する方法 - Stefan Schackow 共演][Azure Websites のスケールを設定する方法 - Stefan Schackow 共演]

<!-- LINKS --> 

<!-- IMAGES -->

  [Web ホスティング プランとは]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-web-hosting-plan-overview/
  [Azure Websites の Web ホスティング プランの詳細情報]: http://www.azure.microsoft.com/ja-jp/Documentation/Articles/azure-web-sites-web-hosting-plans-in-depth-overview/
  [Websites の料金詳細]: http://www.windowsazure.com/ja-jp/pricing/details/web-sites/
  [Microsoft Azure サブスクリプションに関するページ]: http://go.microsoft.com/fwlink/?LinkID=235288
  [共有または基本プラン モードへの規模の変更]: #scalingsharedorbasic
  [標準プラン モードへの規模の変更]: #scalingstandard
  [サイトに接続されている SQL Server データベースの規模の変更]: #ScalingSQLServer
  [開発者機能]: #devfeatures
  [その他の機能]: #OtherFeatures
  [管理ポータル]: https://manage.windowsazure.com/
  [Web サイトの選択]: ./media/web-sites-scale/01SelectWebSite.png
  [スケール タブ]: ./media/web-sites-scale/02SelectScaleTab.png
  [Web ホスティング プランの選択]: ./media/web-sites-scale/03aChooseWHP.png
  [Virtual Machine and Cloud Service Sizes for Microsoft Azure (Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ)]: http://go.microsoft.com/fwlink/?LinkId=309169
  [基本モードのインスタンス サイズ]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
  [基本モードのインスタンス数]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
  [保存ボタン]: ./media/web-sites-scale/05SaveButton.png
  [プランの変更が完了]: ./media/web-sites-scale/06BasicComplete.png
  [標準プランの選択]: ./media/web-sites-scale/07ChooseStandard.png
  [標準モードでの 容量 セクション]: ./media/web-sites-scale/08CapacitySectionStandard.png
  [インスタンス サイズの選択]: ./media/web-sites-scale/09ChooseInstanceSize.png
  [スケジュール時間の設定]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
  [スケジュール時間の設定 ダイアログ]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
  [スケジュールのスケール設定の編集]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
  [メトリックによるスケール]: ./media/web-sites-scale/13ScaleByMetric.png
  [インスタンス数]: ./media/web-sites-scale/14InstanceCount.png
  [ターゲット CPU]: ./media/web-sites-scale/15TargetCPU.png
  [Azure プレビュー ポータル]: https://portal.azure.com/
  [Web サイトのスケールの変更方法]: http://azure.microsoft.com/ja-jp/documentation/articles/insights-how-to-scale/
  [リンクされたデータベース]: ./media/web-sites-scale/16LinkedResources.png
  [SQL Server データベースの規模の設定]: ./media/web-sites-scale/17ScaleDatabase.png
  [Accounts and Billing in Microsoft Azure SQL Database (Microsoft Azure SQL データベースのアカウントと課金)]: http://go.microsoft.com/fwlink/?LinkId=234930
  [Web サイトの監視方法]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-monitor/
  [Microsoft Azure の無料評価版サイト]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [データ転送の料金詳細]: http://www.windowsazure.com/ja-jp/pricing/details/data-transfers/
  [Azure のサポート プラン]: http://www.windowsazure.com/ja-jp/support/plans/
  [サービス レベル アグリーメント]: http://www.windowsazure.com/ja-jp/support/legal/sla/
  [SQL データベースの料金詳細]: http://www.windowsazure.com/ja-jp/pricing/details/sql-database/
  [Websites の料金詳細 - SSL 接続]: http://www.windowsazure.com/ja-jp/pricing/details/web-sites/#ssl-connections
  [ベスト プラクティス:Windows Azure Websites (WAWS)]: http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx
  [Azure Websites のスケールを設定するタイミング - Stefan Schackow 共演]: http://www.windowsazure.com/ja-jp/documentation/videos/azure-web-sites-free-vs-standard-scaling/
  [Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演]: http://www.windowsazure.com/ja-jp/documentation/videos/auto-scaling-azure-web-sites/
  [Azure Websites のスケールを設定する方法 - Stefan Schackow 共演]: http://www.windowsazure.com/ja-jp/documentation/videos/how-azure-web-sites-scale/
