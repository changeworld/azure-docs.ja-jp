<properties urlDisplayName="Resources" pageTitle="Azure Websites でのデジタル マーケティング キャンペーンの作成" metaKeywords="" description="This guide provides a technical overview of how to use Azure Websites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Azure Websites でのデジタル マーケティング キャンペーンの作成
このガイドでは、Azure Websites を使用してデジタル マーケティング キャンペーンを作成する方法 (技術概要) を説明します。通常、デジタル マーケティング キャンペーンは、短期的なマーケティング目標を達成するため一定期間のみ実施されます。この場合、主に 2 つのシナリオが考えられます。1 つは、サードパーティのマーケティング会社が顧客のためにキャンペーンを作成し、販促期間中はそのマーケティング会社がキャンペーンを管理するシナリオです。もう 1 つは、デジタル マーケティング キャンペーンをマーケティング会社が作成した後、そのリソースの所有権を顧客へ譲渡するシナリオです。その後は、顧客が単独でデジタル マーケティング キャンペーンを運営管理します。 

[Azure Web Sites (Azure の Web サイト) ][websitesoverview] はこれら両方のシナリオに適しています。キャンペーンを短期間で作成できる、複数のフレームワークと言語をサポートする、ユーザーの需要に応じて規模を設定できる、多数のデプロイ システムやソース管理システムに対応できるなどのメリットがあります。また、Azure を使用すれば他の Azure サービス (Media Services など) にもアクセスできるため、マーケティング キャンペーンの効果がさらに高まります。

[Azure Cloud Services][csoverview] または [Azure Virtual Machines][vmoverview] を使用して Web サイトをホストすることもできますが、Azure Websites にはない重要な機能を備えているのでない限り、このシナリオには適していません。詳細については、「[Azure Web Sites、Cloud Services、VM: いつ、どれを使用するか ][chooseservice]」を参照してください。

このガイドの内容は次のとおりです。

- [既存の Web サイトのデプロイ](#deployexisting)
- [ソーシャル メディアの統合](#socialmedia)
- [ユーザーの需要に応じた規模設定 (スケーリング)](#scale)
- [その他のサービスの統合](#integrate)
- [キャンペーンの監視](#monitor)

<div class="dev-callout">
<strong>注</strong>
<p>このガイドで取り上げるのは公開 .COM サイト開発で必要となる最も一般的な分野やタスクですが、Azure Websites には特殊なニーズに対応できるその他の機能も備わっています。これらの機能については、<a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/global-web-presence-solution-overview/">グローバル Web プレゼンス</a>および<a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/business-application-solution-overview">基幹業務アプリケーション</a>のガイドを参照してください。</p>
</div>

##<a name="deployexisting"></a>既存の Web サイトのデプロイ
"グローバル Web プレゼンス" シナリオでは、新しい Web サイトを作成してデプロイするためのさまざまな方法を検討しました。Azure Websites を初めて使用する場合は、まず、["グローバル Web プレゼンス" シナリオの内容][scenarioglobalweb]を確認してください。デジタル マーケティング キャンペーンを頻繁に作成しており、既に Web アセットが存在する場合は、それらをカスタマイズして別のプロモーションで使用できます。このセクションでは、ソース管理からさまざまな種類の Web サイトをデプロイする方法について詳しく説明します。

同じ Web アセットを複数の目的で利用する場合、ソース管理システムをまだ使用していなければ、ぜひ導入を検討してください。一般的な Web ソリューションのテンプレートを保存しておき、それぞれの顧客に合わせてカスタマイズできます。Web サイトには、さまざまなソース コード リポジトリを同期する機能が備わっています。**[ダッシュボード]** タブで **[ソース管理からのデプロイの設定]** をクリックします。

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

ソース コード選択ダイアログが開き、すべての機能を備えたソース管理システム (TFS など) やシンプルなデプロイ ソリューション (Dropbox など) が表示されます。

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

各種のソース管理機能を使用し、既存のテンプレートに基づいて新しいプロジェクトを管理できます。たとえば、以前に保存したリポジトリをコピーして新しいプロジェクトを開始したり、現在のプロジェクトの要件に合わせてカスタマイズしたりできます。同じソース管理リポジトリを基にさまざまなデプロイメントを管理する方法の実例については、「[Multiple Environments with Azure Web Sites (複数の環境で Azure の Web サイトを使用)][gitstaging]」を参照してください。この記事では、Git 分岐を使用してステージング環境と運用環境を管理する方法を紹介します。

Web サイトをソース管理に接続した後は、ポータルからデプロイメントを構成してトラッキングできます。Web サイトでソース管理を使用する方法の詳細については、「[ソース管理から Azure の Web サイトへの発行][publishingwithgit]」を参照してください。

既存の Web アセットを使用する場合、各種の Web サイトをホストする柔軟性が重要になります。**[構成]** タブでは、Web サイトの .NET サポートと PHP サポートを両方選択できます。 

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

これらの構成オプションに加え、Web サイトは Python 2.7 と Node.js も自動的にサポートします。既定の Node.js バージョンは 0.10.5 です。

また、ステージング サイトをすばやく Web にデプロイできることも Azure Websites のメリットの 1 つです。サイトの計画段階、プロトタイピング、開発の初期段階で、代理店とその顧客は、キャンペーン サイトを運用する前に実際と同じ状態で動作を確認できます。サイトの運用準備が整った時点で、代理店は顧客に代わって運用デプロイを管理するか、キャンペーンをデプロイおよび管理するための Web アセットを顧客に提供できます。

##<a name="socialmedia"></a>ソーシャル メディアの統合
ほとんどのデジタル マーケティング キャンペーンでは、Facebook や Twitter などのソーシャル メディア サイトを利用します。これらを統合するには、ソーシャル メディア ID を使って認証する方法があります。ASP.NET アプリケーションでのこの手法の使用例については、「[メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトに展開する][deploysecurewebsite]」を参照してください。

ただし、多くのデジタル マーケティング キャンペーンは認証にとどまらず、重要な戦略としてソーシャル メディアを統合しています。通常、ソーシャル メディア サイトには、それぞれのサービスをアプリケーションに統合する方法を紹介する開発者セクションがあります。REST API を提供するサービスはほとんどの Web フレームワークで使用できますが、言語に固有の情報もあります。たとえば Twitter では、.NET、Node.js、PHP など、[Twitter API をサポートしているライブラリの一覧][twitter]を参照できます。この他にもさまざまな情報が公開されているので、対象となるソーシャル メディア サイトの開発者向けガイダンスを参照してください。

Facebook を対象とする ASP.NET 開発者向けとして、Visual Studio には MVC 4 Facebook アプリケーション用のテンプレートが用意されています。  

![DigitalMarketingFacebook][DigitalMarketingFacebook]

Web サイトでこのテンプレートを使用するための手順については、「[Creating a Facebook app using ASP.NET MVC Facebook Templates and hosting them for free on Azure Websites (ASP.NET MVC Facebook テンプレートを使用して Facebook アプリケーションを作成し、それらのアプリケーションを Azure Websites で無料でホストする)][fbtutorial]」を参照してください。詳しいチュートリアルとサンプル アプリケーションについては、「[ASP.NET MVC Facebook Birthday App (ASP.NET MVC Facebook の誕生日アプリケーション)][fbbirthdayapp]」および「[The new Facebook application template and library for ASP.NET MVC (ASP.NET MVC 向けの新しい Facebook アプリケーション テンプレートとライブラリ)][fbvstemplate]」を参照してください。

ASP.NET アプリケーションを開発する場合、ソーシャル メディアを統合する方法は Visual Studio に用意されているテンプレートだけでないことに注意してください。テンプレートを利用すればソーシャル メディアを容易に統合できますが、前述のように、各ソーシャル メディア サイトでは、.NET およびその他多数の言語やフレームワークから接続するさまざまな方法が公開されています。

##<a name="scale"></a>ユーザーの需要に応じた規模設定 (スケーリング)
クラウド コンピューティングは負荷を予測できない場合に役立ちます。デジタル マーケティング キャンペーンもその 1 つです。運用期間が比較的短いマーケティング サイトの需要を予測するのは容易ではありません。ユーザーの関心をどの程度引き付けられるか、関連するソーシャル メディアからマーケティング サイトへのアクセスがどの程度発生するかに大きく左右されるためです。Azure には、Web サイトとクラウド サービスの両方について、いくつかのスケーリング オプションが用意されています。

- [Azure の管理ポータル][managementportal]で手動で規模を設定する。
- [サービス管理 API][servicemanagementapi] または [PowerShell スクリプト][powershell]を使用してプログラムから規模を設定する。
- 自動スケール (プレビュー) 機能を使用して自動的に規模を設定する

管理ポータルで、Web サイトの **[スケール]** タブへ移動します。規模設定 (スケーリング) に関するいくつかの設定オプションが表示されます。最初のオプションでは Web サイトのモードを設定します。**[Free]**、**[Shared]**、または **[Standard]** を選択できます。 

![DigitalMarketingScale][DigitalMarketingScale]

サイトの拡張性はモード設定によって異なります。たとえば、**[無料]** モードのサイトはインスタンスを増やすことができませんが、 **[共有]** モードのサイトは 6 インスタンスまで拡張できます。**[標準]** モードを選択した場合も規模拡張が可能です。このモードのサイトは専用の仮想マシン上で運用され、S サイズ、M サイズ、L サイズの仮想マシンを選択できます。仮想マシンのサイズを指定した後、さらにインスタンス数を増やすこともできます。**[標準]** モードでは最大 10 インスタンスまで拡張可能です。これら 3 つのモードの相違点については、「[Web サイトの料金詳細][pricing]」を参照してください。

**[標準]** モードを選択した場合は、自動スケール (プレビュー) 機能も使用できます。自動スケールは、CPU の使用率に応じてインスタンス数を増減する機能です。**[ターゲット CPU]** は、Web サイトのインスタンスによるプロセッサ使用率の目標範囲です。CPU の平均使用率がこのターゲット値を下回る場合、Azure によってインスタンス数が減らされます。少ない数のインスタンスで同じ負荷を処理すれば、結果として CPU の使用率が高くなります。ただし、最小**インスタンス数**より小さい値を指定することはできません。同様に、CPU の平均使用率が **[ターゲット CPU]** を上回る場合は、Azure によってインスタンス数が増やされます。より多くの仮想マシンに負荷が分散されるので、仮想マシン 1 台あたりの CPU 使用率が下がります。この場合も、**最大インスタンス数**より大きい値を指定することはできません。

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

自動スケールのメリットはリソースを効率的に利用できることです。たとえば、デジタル マーケティング キャンペーンは夜間と週末にアクセスが集中します。その際、キャンペーンの需要を予測するのは容易ではありません。自動スケール機能を使用すれば、負荷の変動に応じてインスタンス (およびコスト) を効率的に増減できます。 

Web サイトのスケール機能の詳細については、「[Web サイトの規模の設定方法][scalewebsite]」を参照してください。Web サイトの規模設定は監視機能とも密接に関連しています。詳細については、このガイドの「[キャンペーンの監視](#monitor)」を参照してください。

<div class="dev-callout">
<strong>注</strong>
<p>クラウド サービスと Web ロールを使用する Web アプリケーションでは、キュー内のメッセージ数に基づく規模設定 (スケーリング) も可能です。クラウド サービスの場合、バックエンド キューを処理するロールはアーキテクチャ パターンが共通しています。クラウド サービスの規模設定の詳細については、<a href="http://www.windowsazure.com/ja-jp/manage/services/cloud-services/how-to-scale-a-cloud-service/">クラウド サービスの規模設定に関するドキュメント</a>を参照してください。</p>
</div>

##<a name="integrate"></a>その他のサービスの統合
多くのデジタル マーケティング サイトは、動画ストリーミングなどのリッチ メディアを取り入れています。Azure には、これらのサイトですぐに役立つサービスが用意されています。たとえば、Azure Media Services を使用すれば、Web サイトの動画をエンコードしてストリーミング配信できます。Media Services の詳細については、「[Azure Media Services の概念][mediaservices]」を参照してください。

Azure には、信頼性の高いアプリケーションを作成するためのサービスも用意されています。たとえば、Websites は新しい [Azure マネージ キャッシュ サービス (プレビュー)][caching] の分散キャッシュを利用できます。Azure ストレージ サービスを使用してアプリケーションのデータとリソースを格納したりできます。この場合、グラフィックスやビデオなど、サイズの大きいファイルを BLOB に永続的に格納できます。Azure SQL Database、MySQL などのデータベース サービスを使用してリレーショナル データを管理することもできます。

##<a name="monitor"></a>キャンペーンの監視
**[監視]** タブには、デジタル マーケティング サイトの運用状況や成果を評価するときに役立つメトリックが表示されます。

![DigitalMarketingMonitor][DigitalMarketingMonitor]

たとえば、**CPU 時間**、**要求数**、**送信データ**などのメトリック データから利用パターンや利用レベルがわかります。これらすべてのメトリックは、マーケティング キャンペーンが普及するにつれて値が大きくなります。利用状況に関する情報から、スケールアウトまたはスケールアップの時期を決定できます。詳細については、「[Web サイトの監視方法][monitoring]」を参照してください。

**[無料]** モードと **[共有]** モードでは、リソースのクォータにも気を配る必要があります。管理ポータルの **[ダッシュボード]** タブには、各クォータの現在の利用統計データとリセットされるタイミングが表示されます。これらの利用統計データは選択したモードによって異なります。次のスクリーンショットは **[無料]** モードの統計データです。**[共有]** モードを選択した場合は **[送信データ]** のクォータがありません。**[標準]** モードでは、**[ファイル システム ストレージ]** と **[サイズ]** のみが表示されます。

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

これらのクォータの上限に近づいていることがわかったら、**[無料]** から **[共有]** へ、または **[共有]** から **[標準]** への切り替えを検討してください。**[標準]** モードでは、S サイズ、M サイズ、または L サイズの仮想マシン上に専用リソースが配置されます。 

これらの情報を管理ポータルで閲覧するほか、高度な Web サイト監視機能を備えたサードパーティ製ツールで表示する方法もあります。Azure ストアからインストールできる New Relic アドオンもその 1 つです。New Relic での監視方法については、「[Azure の New Relic によるアプリケーション パフォーマンス管理][newrelic]」を参照してください。 

さらに、標準モードの Web サイトでは、エンドポイントの監視機能とアラート機能を使用できます。この機能では、エンドポイント モニターが定期的に Web サイトにアクセスして応答時間を報告します。さらに、応答時間が所定のしきい値を上回る場合は、その旨を電子メールで警告します。詳細については、「[グローバル Web プレゼンス][scenarioglobalweb]」シナリオの監視セクション、およびトピック「[方法: Azure でアラート通知を受け取り、アラート ルールを管理する][receivealerts]」を参照してください。

##<a name="summary"></a>まとめ
Azure Websites は、同じ Web コンテンツを個々のマーケティング キャンペーンに合わせてカスタマイズする場合に適しています。Azure Websites は一般的な多くの言語、フレームワーク、およびソース管理システムをサポートしているので、これらのアセットやワークフローをクラウドへ容易に移行できます。ASP.NET Facebook アプリケーション テンプレートを使用すれば Facebook アプリケーションを簡単に作成できますが、サードパーティのほとんどのソーシャル メディア統合機能も Web インターフェイスをサポートしています。Azure メディア サービスと Azure のその他の関連サービスには、適切に設計されたキャンペーン サイトを作成するためのツールも用意されています。また、予測が難しいユーザー需要に対応するには、手動および自動の規模設定 (スケール) 機能が役立ちます。詳細については、次の技術解説記事を参照してください。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">領域</th>
   <th align="left" valign="top">リソース</th>
</tr>
<tr>
   <td valign="middle"><strong>計画</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/choose-web-app-service">Azure Websites、Cloud Services、仮想マシン: いつ、どれを使用するか</a></td>
</tr>
<tr>
   <td valign="middle"><strong>作成</strong></td>
   <td valign="top"> - <a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-create-websites/">Web サイトの作成/デプロイ方法</a></td>
</tr>
<tr>
   <td valign="middle"><strong>展開</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-deploy/">Azure Website のデプロイ方法</a><br/>- <a href="http://www.windowsazure.com/ja-jp/develop/net/common-tasks/publishing-with-git/">Git を使用した Azure Websites への発行</a></td>
</tr>
<tr>
   <td valign="middle"><strong>ソーシャル メディア</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ja-jp/develop/net/tutorials/web-site-with-sql-database/">メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Windows Azure の Web サイトにデプロイする</a><br/>- <a href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Create a Facebook app using ASP.NET MVC Facebook Templates (ASP.NET MVC Facebook テンプレートを使用して Facebook アプリケーションを作成する)</a><br/>- <a href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Facebook application template and library for ASP.NET MVC (ASP.NET MVC の Facebook アプリケーション テンプレートとライブラリ)</a></td>
</tr>
<tr>
   <td valign="middle"><strong>スケール</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-scale-websites/">Web サイトの規模の設定方法</a></td>
</tr>
<tr>
   <td valign="middle"><strong>リッチ メディア</strong></td>
   <td valign="top">- <a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223282.aspx">Azure Media Services の概念とシナリオ</a></td>
</tr>
<tr>
   <td valign="middle"><strong>監視</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-monitor-websites/">Web サイトの監視方法</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">方法: Azure でアラート通知を受け取り、アラート ルールを管理する</a></td>
</tr>
</table>

  [websitesoverview]:/ja-jp/documentation/services/web-sites/
  [csoverview]:/ja-jp/documentation/services/cloud-services/
  [vmoverview]:/ja-jp/documentation/services/virtual-machines/
  [chooseservice]:/ja-jp/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/ja-jp/manage/services/web-sites/global-web-presence-solution-overview/
  
  
  [publishingwithgit]:/ja-jp/develop/net/common-tasks/publishing-with-git/
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [deploysecurewebsite]:/ja-jp/develop/net/tutorials/web-site-with-sql-database/
  [twitter]:https://dev.twitter.com/docs/twitter-libraries#dotnet
  [fbtutorial]:http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [fbbirthdayapp]:http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [fbvstemplate]:http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [managementportal]:http://manage.windowsazure.com/
  [servicemanagementapi]:http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460799.aspx
  [powershell]:http://msdn.microsoft.com/ja-jp/library/windowsazure/jj152841.aspx
  [pricing]:https://www.windowsazure.com/ja-jp/pricing/details/web-sites/
  [scalewebsite]:/ja-jp/manage/services/web-sites/how-to-scale-websites/
  
  [mediaservices]:http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223282.aspx
  [caching]:http://msdn.microsoft.com/ja-jp/library/windowsazure/dn386094.aspx
  [monitoring]:/ja-jp/manage/services/web-sites/how-to-monitor-websites/
  [newrelic]:/ja-jp/develop/net/how-to-guides/new-relic/
  [receivealerts]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
  
  
  
   [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  
  
  
  
  
