<properties linkid="websites-business-application" urlDisplayName="Azure の Web サイトに基幹業務アプリケーションを作成" pageTitle="Azure の Web サイトに基幹業務アプリケーションを作成" metaKeywords="Web サイト" description="このガイドでは、Azure の Web サイトを使用して基幹業務アプリケーションを作成する方法 (技術概要) について説明します。たとえば、認証方式、サービス バス リレー、監視を取り上げます。" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" title="Azure の Web サイトに基幹業務アプリケーションを作成" authors="" />



# Azure の Web サイトに基幹業務アプリケーションを作成

このガイドでは、Azure の Web サイトを使用して基幹業務アプリケーションを作成する方法 (技術概要) について説明します。ここで作成するのは、セキュリティで保護された社内業務用のイントラネット アプリケーションです。基幹業務アプリケーションには 2 つの特性があります。まず、認証 (通常は社内ディレクトリに対する認証) が必要です。次に、内部設置型のデータやサービスにアクセスできるようにするか、またはこれらを統合する必要があります。このガイドで紹介するのは [Azure の Web サイト][websitesoverview]にビジネス アプリケーションを構築する方法ですが、要件には [Azure のクラウド サービス][csoverview]または [Azure の仮想マシン][vmoverview]の方が適している場合もあります。「[Azure の Web サイト、クラウド サービス、仮想マシン: いつ、どれを使用するか][chooseservice]」を参照し、これらの相違点を確認してください。

このガイドの内容は次のとおりです。

- [メリットの検討](#benefits)
- [認証方式の選択](#authentication)
- [認証をサポートする Azure の Web サイトの作成](#createintranetsite)
- [サービス バスを使用して内部設置型リソースを統合](#servicebusrelay)
- [アプリケーションの監視](#monitor)

<div class="dev-callout">
<strong>メモ</strong>
<p>このガイドで取り上げるのは公開 .COM サイト開発で必要となる最も一般的な分野やタスクですが、Azure の Web サイトには特殊なニーズに対応できるその他の機能も備わっています。これらの機能については、<a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/global-web-presence-solution-overview/">グローバル Web プレゼンス</a>および<a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/digital-marketing-campaign-solution-overview">デジタル マーケティング キャンペーン</a>のガイドを参照してください。</p>
</div>

##<a name="benefits"></a>メリットの検討
通常、基幹業務アプリケーションは社内ユーザーを対象としているので、内部設置型のリソースやインフラストラクチャとクラウドを比較し、クラウドならではのメリットを検討する必要があります。クラウドへ移行した場合のメリットの 1 つとして、作業負荷の変動に合わせた規模調整 (スケール) があります。たとえば、年間業績評価を処理するアプリケーションを利用しているとします。このようなアプリケーションの場合、1 年の大半は処理すべきトラフィックがほとんど発生しませんが、考査期間になると (特に大規模な会社では) トラフィックが急増します。Azure では負荷が増大する考査期間中にインスタンスを増やし、それ以外の期間は元に戻すことができるので、コスト節約につながります。また、クラウドには、インフラストラクチャの購入と管理に伴う負荷を軽減し、アプリケーション開発に集中できるようにするというメリットがあります。

そのほかにもクラウドにはさまざまなメリットがあります。たとえば、業務アプリケーションをクラウドに配置すると、従業員やパートナーはどこからでもアプリケーションを使用できるようになります。アプリケーションを使用する際、ユーザーは社内ネットワークに接続する必要がありません。IT スタッフは複雑なリバース プロキシの設定から解放されます。社内アプリケーションへのアクセスを保護する手段として、いくつかの認証方式があります。この後のセクションでは、これらの認証方法について説明します。

##<a name="authentication"></a>認証方式の選択
基幹業務アプリケーションの場合、どのような認証方式を採用するかは最も重要な決定事項の 1 つです。次のような認証方式があります。

- [Azure の Active Directory サービス][adoverview]を使用する。単独のディレクトリとして使用するか、または内部設置型の Active Directory と同期することができます。この場合、アプリケーションは Azure の Active Directory とやり取りしてユーザーを認証します。この方法の概要については、「[Azure の Active Directory の使用][adusing]」を参照してください。
- Azure の仮想マシンと仮想ネットワークを使用して Active Directory をインストールする。この方法では、内部設置型の Active Directory 環境をクラウドへ拡張できます。さらに、Active Directory フェデレーション サービス (ADFS) を使用して、ID 要求を内部設置型 AD と連携することも可能です。Azure アプリケーションの認証は、ADFS を経由して内部設置型 Active Directory へ送られます。この方式の詳細については、「[VM での Windows Server Active Directory の実行][adwithvm]」および「[Azure の仮想マシンでの Windows Server Active Directory の展開ガイドライン][addeployguidelines]」を参照してください。
- [Azure の Access Control サービス][acs2] (ACS) などの中間サービスを介し、複数の ID サービスを使用してユーザーを認証する。これは、Active Directory または別の ID プロバイダーを介してユーザーを認証するための抽象化です。詳細については、「[Azure の Active Directory Access Control の使用][acsusing]」を参照してください。

ここで紹介する最初の基幹業務アプリケーションのシナリオでは、Azure の Active Directory を使用します。アプリケーションの認証方式を最も短期間で実装できます。これ以降の説明は Azure の Active Directory が中心となりますが、ビジネス要件によっては他の 2 つの方法の方が適している場合もあります。たとえば、ID 情報をクラウドと共有できない場合は ADFS ソリューションが適しており、他の ID プロバイダー (Facebook など) をサポートする必要がある場合は ACS ソリューションが適しています。

新しい Azure の Active Directory をセットアップする前に、既存のサービス (Office 365 や Windows Intune など) が既に Azure の Active Directory を使用しているかどうか確認してください。その場合、既存のサブスクリプションを Azure サブスクリプションに関連付けます。詳細については、「[Azure AD テナントとは][adtenant]」を参照してください。

現在、どのサービスでも Azure の Active Directory を使用していない場合は、管理ポータルで新しいディレクトリを作成できます。管理ポータルの **[Active Directory]** セクションで新しいディレクトリを作成します。

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

ディレクトリを作成すると、ユーザー、統合アプリケーション、ドメインを作成および管理するためのオプションが表示されます。

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

これらの詳しい手順については、「[Azure AD を使用して Web アプリケーションへのサインオンを追加する][adsso]」を参照してください。新しく作成したディレクトリを単独リソースとして使用する場合、次は、そのディレクトリを組み込んだアプリケーションを開発します。ただし、内部設置型の Active Directory ID を使用している場合は、それらを新しい Azure の Active Directory と同期させます。詳しい手順については、「[ディレクトリ統合の概要][dirintegration]」を参照してください。

ディレクトリを作成して設定したら、認証を要求する Web アプリケーションを作成し、それらのアプリケーションをディレクトリと連携させる必要があります。これらの手順については、この後の 2 つのセクションで説明します。

##<a name="createintranetsite"></a>認証をサポートする Azure の Web サイトの作成
"グローバル Web プレゼンス" シナリオでは、新しい Web サイトを作成して展開するためのさまざまな方法を検討しました。Azure の Web サイトを初めて使用する場合は、まず[こちらのトピック][scenarioglobalweb]をお読みください。Windows 認証を使用するイントラネット Web アプリケーションが必要な場合、通常は Visual Studio で ASP.NET アプリケーションを作成します。その理由の 1 つとして、ASP.NET と Visual Studio による緊密な統合とサポートがあります。

たとえば、Visual Studio で ASP.NET MVC 4 プロジェクトを作成する場合、プロジェクトの作成ダイアログ ボックスで **[イントラネット アプリケーション]** を選択できます。

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

これによって、Windows 認証をサポートするようにプロジェクト設定が変更されます。具体的には、web.config ファイルで、**authentication** 要素の **mode** 属性が **Windows** に設定されます。別の ASP.NET プロジェクト (Web フォーム プロジェクトなど) を作成する場合、または既存のプロジェクトで作業する場合は、この設定を手動で変更する必要があります。

MVC プロジェクトでは、[プロジェクトのプロパティ] ウィンドウでさらに 2 つの値を変更します。**[Windows 認証]** を **[有効]** に設定し、**[匿名認証]** を **[無効]** に設定します。

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

Azure の Active Directory で認証するには、このアプリケーションをディレクトリに登録し、アプリケーション構成を変更して接続できるようにする必要があります。Visual Studio にはそのための方法が 2 つ用意されています。

- [Visual Studio 向け Identity and Access ツール](#identityandaccessforvs)
- [Azure の Active Directory 向け Microsoft ASP.NET ツール](#aspnettoolsforwaad)

###<a name="identityandaccessforvs"></a>Visual Studio 向け Identity and Access ツール: 
1 つは、[Identity and Access ツール][identityandaccess]を使用する方法です (ダウンロードしてインストールできます)。このツールでは、プロジェクト コンテキスト メニューに Visual Studio が統合されています。次の手順説明とスクリーンショットは Visual Studio 2012 のものです。目的のプロジェクトを右クリックし、**[Identity and Access]** を選択します。3 つのオプションを設定する必要があります。**[Providers]** タブで、**STS メタデータ ドキュメントのパス**と **APP ID URI** を指定します (これらの値を取得する方法については「[Azure の Active Directory でアプリケーションを登録](#registerwaadapp)」を参照)。

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

最後に、**[Identity and Access]** ダイアログ ボックスの **[Configuration]** タブで構成を変更します。**[Enable web farm cookies]** チェックボックスをオンにしてください。詳しい手順については、「[Azure AD を使用して Web アプリケーションへのサインオンを追加する][adsso]」を参照してください。

####<a name="registerwaadapp"></a>Azure の Active Directory にアプリケーションを登録する:
**[Providers]** タブに情報を入力するには、Azure の Active Directory にアプリケーションを登録する必要があります。Azure 管理ポータルの **[Active Directory]** セクションで目的のディレクトリを選択し、**[アプリケーション]** タブへ移動します。Azure の Web サイトの URL を追加するためのオプションが表示されます。これらの手順を実行するときは、とりあえず、Visual Studio でのローカル デバッグ時に使用する localhost アドレスの URL を設定しておきます。後で展開する際、Web サイトの実際の URL に変更します。

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

管理ポータルに、STS メタデータ ドキュメントのパス (**フェデレーション メタデータ ドキュメントの URL**) と **APP ID URI** の両方が表示されます。これらの値は、Visual Studio の **[Identity and Access]** ダイアログ ボックスにある **[プロバイダー]** タブで使用します。

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

###<a name="aspnettoolsforwaad"></a>Azure の Active Directory 用 Microsoft ASP.NET ツール:
もう 1 つは、[Azure の Active Directory 用 Microsoft ASP.NET ツール][aspnettools]を使用する方法です。このツールを使用するには、Visual Studio の **[プロジェクト]** で **[Azure 認証を有効にする]** をクリックします。(アプリケーションの URL ではなく) Azure の Active Directory ドメインのアドレスを尋ねるシンプルなダイアログ ボックスが表示されます。

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

Active Directory ドメインの管理者である場合は、**[Azure AD でこのアプリケーションをプロビジョニングする]** チェックボックスをオンにします。これでアプリケーションが Active Directory に登録されます。管理者でない場合は、このチェックボックスをオフにして、表示された情報を管理者に通知してください。管理者は管理ポータルから Identity and Access ツールを使用し、前の手順に従って統合アプリケーションを作成できます。Azure の Active Directory 用 ASP.NET ツールを使用する詳しい手順については、「[Azure Authentication (Azure の認証)][azureauthtutorial]」を参照してください。

基幹業務アプリケーションを管理する際、サポートされているすべてのソース コード管理システムを使用して展開できます。ただし、このシナリオでは Visual Studio が密に統合されているので、Team Foundation Service (TFS) を選択するのが一般的です。その場合、Azure の Web サイトに TFS が統合されていることに注意してください。管理ポータルで、目的の Web サイトの **[ダッシュボード]** タブを開きます。次に **[ソース管理からの展開の設定]** を選択します。TFS の使用手順に従います。

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

##<a name="servicebusrelay"></a>サービス バスを使用して内部設置型リソースを統合
多くの基幹業務アプリケーションは、内部設置型のデータやサービスを統合する必要があります。いくつかの理由により、一部のデータはクラウドへ移動できません。これらの理由には実際的なものと規制によるものがあります。どのデータを Azure でホストし、どのデータを内部設置のままにするかを計画するときは、[Azure のトラスト センター][trustcenter]でリソースを確認することが重要です。ハイブリッド Web アプリケーションは Azure で実行され、内部設置のリソースにアクセスします。

仮想マシンまたはクラウド サービスを使用すれば、仮想ネットワークを介して Azure のアプリケーションを社内ネットワークに接続できます。ただし、Web サイトは仮想ネットワークをサポートしないので、[Azure のサービス バス リレー サービス][sbrelay]を使用して Web サイトを統合するのが最適といえます。サービス バス リレー サービスを使用すれば、クラウドのアプリケーションから社内ネットワーク上の WCF サービスへ安全に接続できます。その際、ファイアウォール ポートを開く必要がありません。

次の図では、クラウド アプリケーションと内部設置型 WCF サービスの両方が、以前に作成した名前空間を介してサービス バスと通信します。内部設置型の WCF サービスは、クラウドへ移動できない内部データとサービスにアクセスできます。WCF サービスはこの名前空間にエンドポイントを登録します。Azure で実行される Web サイトは、サービス バスのこのエンドポイントにも接続します。そのために必要なのは、パブリック HTTP 要求を発行することだけです。

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

さらに、サービス バスはクラウド アプリケーションを内部設置型 WCF サービスへ接続します。これは、両方 (Azure と内部設置型) のサービスやリソースを使用するハイブリッド アプリケーションを作成するための基本的なアーキテクチャです。詳細については、「[サービス バス リレー サービスの使用方法][sbrelayhowto]」および「[サービス バス リレー メッセージのチュートリアル][sbrelaytutorial]」を参照してください。この手法の具体例については、「[Enterprise Pizza - Connecting Web Sites to On-premise Using Service Bus (サービス バスを使用して Web サイトを内部環境に接続する)][enterprisepizza]」を参照してください。

##<a name="monitor"></a>アプリケーションの監視
基幹業務アプリケーションでは、規模設定 (スケール) や監視など Web サイトの標準機能が役立ちます。曜日や時間帯によって負荷が変動する業務アプリケーションでは、自動スケール (プレビュー) 機能を使ってサイトの規模を調整することでリソースを効率的に活用できます。監視機能にはエンドポイントの監視とクォータの監視があります。詳細については、[グローバル Web プレゼンス][scenarioglobalweb]および[デジタル マーケティング キャンペーン][scenariodigitalmarketing]のシナリオを参照してください。

監視要件は、基幹業務に対するビジネス アプリケーションの重要性によって異なります。重要度の高い基幹業務アプリケーションでは、[New Relic][newrelic] などサードパーティ製監視ソリューションの導入を検討してください。

一般に、基幹業務アプリケーションは IT スタッフが管理します。詳細ログを有効にしておけば、予期しないエラーや動作が発生した場合にログ データを分析し、トラブルの原因を突き止めることができます。管理ポータルで **[構成]** タブに移動し、**[アプリケーション診断]** セクションと **[サイト診断]** セクションでオプションを確認します。

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

各種のアプリケーション ログとサイト ログを使用して、Web サイトのトラブルを解決します。一部のオプションでは**ファイル システム**を指定します。サイト内の指定したファイル システムにログ ファイルが保存されます。これらのログ ファイルには、FTP、Azure PowerShell、または Azure コマンドライン ツールからアクセスできます。その他のオプションでは**ストレージ**を指定します。指定した Azure のストレージ アカウントに情報が送信されます。**[Web サーバーのログ記録]** では、ファイル システムのディスク クォータまたはストレージの保有ポリシーを指定することもできます。これは、保存するログ データが際限なく増加するのを防ぐための機能です。

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

これらのログ設定の詳細については、「[方法: Web サイトの診断を構成する/ログをダウンロードする][configurediagnostics]」を参照してください。

FTP やストレージ ユーティリティ (Azure ストレージ エクスプローラーなど) で未加工のログを表示するほか、Visual Studio でもログ情報を表示できます。トラブル発生時にこれらのログを使用する方法については、「[Visual Studio での Azure の Web サイトのトラブルシューティング][troubleshootwebsites]」を参照してください。

##<a name="summary"></a>まとめ
Azure を使用すれば、安全なイントラネット アプリケーションをクラウドでホストできます。Azure の Active Directory にはユーザー認証機能が備わっているので、自社の従業員のみにアプリケーションへのアクセスを許可できます。サービス バス リレー サービスは、Web アプリケーションが内部設置型のサービスやデータと通信するための仕組みです。このハイブリッド アプリケーション方式を採用すれば、業務用のデータやサービスをすべて移行する必要がないので、業務アプリケーションをクラウドへ容易に公開できます。基幹業務アプリケーションを展開した後は、規模設定 (スケール) や監視など、Azure の Web サイトに備わっている標準機能を利用できます。詳細については、次の技術解説記事を参照してください。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">領域</th>
   <th align="left" valign="top">リソース</th>
</tr>
<tr>
   <td valign="middle"><strong>計画</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/choose-web-app-service">Azure の Web サイト、クラウド サービス、仮想マシン: いつ、どれを使用するか</a></td>
</tr>
<tr>
   <td valign="middle"><strong>作成およびデプロイ</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ja-jp/develop/net/tutorials/get-started/">ASP.NET Web アプリケーションを Azure の Web サイトに展開する</a><br/>- <a href="http://www.windowsazure.com/ja-jp/develop/net/tutorials/web-site-with-sql-database/">安全な ASP.NET MVC アプリケーションを Azure に展開する</a></td>
</tr>
<tr>
   <td valign="middle"><strong>認証</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ja-jp/manage/windows/fundamentals/identity/">Azure ID</a><br/>- <a href="http://www.windowsazure.com/ja-jp/documentation/services/active-directory/">Azure の Active Directory サービス</a><br/>- <a href="http://technet.microsoft.com/ja-jp/library/jj573650.aspx">Azure AD テナントとは</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Azure AD を使用して Web アプリケーションへのサインオンを追加する</a><br/>- <a href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Azure の認証チュートリアル</a></td>
</tr>
<tr>
   <td valign="middle"><strong>サービス バス リレー</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/service-bus-relay/">サービス バス リレー サービスの使用方法</a><br/>- <a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/ee706736.aspx">サービス バス リレー メッセージのチュートリアル</a></td>
</tr>
<tr>
   <td valign="middle"><strong>監視</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-monitor-websites/">Web サイトの監視方法</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">方法: Azure でアラート通知を受け取り、アラート ルールを管理する</a><br/>- <a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">方法: Web サイトの診断を構成する/ログをダウンロードする</a><br/>- <a href="http://www.windowsazure.com/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/"> Visual Studio での Azure の Web サイトのトラブルシューティング</a></td>
</tr>
</table>

  [websitesoverview]:/ja-jp/documentation/services/web-sites/
  [csoverview]:/ja-jp/documentation/services/cloud-services/
  [vmoverview]:/ja-jp/documentation/services/virtual-machines/
  [chooseservice]:/ja-jp/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/ja-jp/manage/services/web-sites/global-web-presence-solution-overview/
  [scenariodigitalmarketing]:/ja-jp/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [adoverview]:/ja-jp/documentation/services/active-directory/
  [adusing]:/ja-jp/manage/windows/fundamentals/identity/#ad
  [adwithvm]:/ja-jp/manage/windows/fundamentals/identity/#adinvm
  [addeployguidelines]:http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156090.aspx
  [acs2]:http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [acsusing]:/ja-jp/manage/windows/fundamentals/identity/#ac
  [adtenant]:http://technet.microsoft.com/ja-jp/library/jj573650.aspx
  [adsso]:http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [dirintegration]:http://technet.microsoft.com/ja-jp/library/jj573653.aspx
  [identityandaccess]:http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [aspnettools]:http://go.microsoft.com/fwlink/?LinkID=282306
  [azureauthtutorial]:http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [trustcenter]:/ja-jp/support/trust-center/
  [sbrelay]:http://msdn.microsoft.com/ja-jp/library/windowsazure/jj860549.aspx
  [sbrelayhowto]:/ja-jp/develop/net/how-to-guides/service-bus-relay/
  [sbrelaytutorial]:http://msdn.microsoft.com/ja-jp/library/windowsazure/ee706736.aspx
  [enterprisepizza]:http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [newrelic]:http://newrelic.com/azure
  [configurediagnostics]:/ja-jp/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [troubleshootwebsites]:/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  
  
  
  
  
  
  
  
  
  
  
  
  
  




  




