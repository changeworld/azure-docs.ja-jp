<properties title="Azure RemoteApp FAQ" pageTitle="Azure RemoteApp よく寄せられる質問" description="Azure RemoteApp について説明します。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Azure RemoteApp よく寄せられる質問
Azure RemoteApp について次のような質問が寄せられています。他にもありますか?[RemoteApp forums (RemoteApp フォーラム)](https://social.msdn.microsoft.com/Forums/azure/ja-jp/home?forum=AzureRemoteApp) を参照して、必要な情報についてお知らせください。

##Azure RemoteAppとは?##


- **Azure RemoteApp とは?** RemoteApp は、Azure からのリモート デスクトップ サービスによってサポートされる内部設置型の Microsoft RemoteApp 機能を提供する Azure のサービスです。RemoteApp では、アプリケーションにさまざまなユーザーのデバイスからセキュリティで保護されたリモート アクセスが可能です。詳細については、「[Azure RemoteApp](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-whatis/)」を参照してください。
- **展開のオプションの 2 つの種類を教えてください。**RemoteApp の展開 (またはコレクション) には 2 つの種類があります。クラウドおよびハイブリッド型です。[deployment option (展開オプション)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-whatis/) は組織に最適です。

##サポートされる構成##


- **カスタム基幹業務 (LOB) アプリケーションはサポートされていますか。** はい。Azure RemoteApp でカスタム アプリケーションを使用するには、[custom template image (カスタム テンプレート イメージ)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-custom-image/) を作成し、RemoteApp コレクションにアップロードします。
- **カスタム LOB アプリケーションは Azure RemoteApp で動作しますか。**これを確認する最善の方法は、テストすることです。「[application compatibility requirements (アプリケーション互換性要件)](http://www.microsoft.com/ja-jp/download/details.aspx?id=18704)」を参照して、「[RD Compatibility Center (RD 互換性センター)](http://www.rdcompatibility.com/compatibility/default.aspx)」を確認します。
- **クラウドとハイブリッドでは、どちらの展開方法が自分の組織に最適でしょうか。**ハイブリッド コレクションは、シングル サインオン (SSO) を使用し、完全な統合とセキュリティで保護された内部設置型ネットワーク接続が必要な場合に、最も包括的なエクスペリエンスを提供します。クラウド コレクションは、複数の認証方法を使用して、展開を迅速で簡単に特定する方法を提供します。詳細については、「[deployment options (展開オプション)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-whatis/)」を参照してください。
- **ハイブリッド コレクションには、VNET が必要です。既存の NET を使用できますか。**現在はできませんが、使用の希望については承知しております。これについては現在、対応中ですが、[次の手順](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx)で既存の VNET を Azure RemoteApp VNET に接続できます。
- **クラウドまたは既存の仮想マシンをテンプレートとして RemoteApp コレクションに使用できますか。**現在はできませんが、これは [feedback site (フィードバック サイト)](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w) によくあるお問い合わせです。
- **内部設置型または Azure に SQL または別のデータベースを設置しています。どの展開タイプを使用したらよいでしょうか。**これは SQL またはバックエンドのデータベースの場所によります。データベースがプライベート ネットワークにある場合は、ハイブリッド コレクションを使用します。データベースがインターネットに表示され、クライアント接続で接続できる場合は、クラウド コレクションを使用できます。
- **ドライブの割り当て、USB とシリアル ポート、クリップボードの共有、およびプリンターのリダイレクトについてはどうでしょうか。**Azure RemoteApp はこれらのすべての機能についてサポートしています。クリップボードの共有とプリンターのリダイレクトは規定で有効です。ドライブの割り当てまたは USB またはシリアル ポートのリダイレクトを有効にする場合は、[お問い合わせ先 ](mailto:remoteappforum@microsoft.com?subject=Azure%20remoteapp% 20enable% 20Redirection %20request =) 連絡してください (このサポートをポータルに追加するように対応中ですが、現時点では含まれておりません)。
- **認証はどうでしょうか。どのメソッドがサポートされていますか。**クラウド コレクションは、Office 365 アカウントでもある Microsoft アカウントと Azure Active Directory アカウントをサポートしています。ハイブリッド コレクションは、Windows Server Active Directory の展開から同期されている (DirSync のようなツールを使用して) Azure Active Directory アカウントのみをサポートします。具体的には、パスワード同期オプションで同期されているか、または Active Directory フェデレーション サービス (AD FS) の構成されたフェデレーションのいずれかで同期されます。[Multi-Factor Authentication (MFA) (多要素認証 (MFA))](http://azure.microsoft.com/ja-jp/documentation/services/multi-factor-authentication/) を構成することもできます。

	**注:** Azure Active Directory ユーザーは、サブスクリプションに関連付けられているテナントからのものである必要があります (ポータルの **[設定]** タブでサブスクリプションを表示および変更できます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp (RemoteApp で使用される Azure Active Directory テナントの変更)](http://msdn.microsoft.com/ja-jp/3d6c4fd1-c981-4c57-9402-59fe31b11883)」を参照してください)。

- **なぜ Azure Active Directory アカウントにアクセスできないのでしょうか。**Azure Active Directory ユーザーは、サブスクリプションに関連付けられているディレクトリからのものである必要があります。ポータルの [設定] タブでディレクトリを表示したり変更することができます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp (RemoteApp で使用される Azure Active Directory のテナントの変更)](http://msdn.microsoft.com/ja-jp/3d6c4fd1-c981-4c57-9402-59fe31b11883)」を参照してください。
- **クライアント アプリケーションはどのデバイスとオペレーティング システムをサポートしていますか。**クライアント アプリケーションは Windows 8.1、Windows 8、Windows 7 Service Pack 1、iOS、Mac OS X、Windows RT、Android デバイス、および Windows Phone で使用できます。Windows 10 プレビューもサポートしています。
 
	RemoteApp クライアントを[ダウンロード](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx)してください。
- **Azure RemoteApp はシン クライアントをサポートしていますか。**次の Windows Embedded シン クライアントはサポートしていません。
	- Windows Embedded Standard 7 with Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **どの Windows Server バージョンは、リモート デスクトップ セッション ホスト (RDSH) でサポートされていますか。**Windows Server 2012 R2。

##サポートとフィードバック

- **このサービスを無料で試用できますか。**はい。30 日間の無償試用版があります。無償試用期間の終了後、有料アカウント(実稼働環境で使用することができます) に移行するか、またはサービスの使用を停止することができます。[manage.windowsazure.com](http://manage.windowsazure.com) に移動して無償試用版の使用を開始してください - RemoteApp の新しいインスタンスを作成します。無償試用版では、インスタンスあたり 10 人のユーザーを含む 2 つの RemoteApp のインスタンスを構築できます。この評価版の試用期間は 30 日間のみです。
- **RemoteApp のサポート プランとは何ですか。**請求とサブスクリプションの管理は無償で提供されます。テクニカル サポートは、[Azure service plans (Azure サービス プラン)](http://azure.microsoft.com/support/plans/) で利用できます。[Azure discussion forum (Azure ディスカッション フォーラム)](http://social.msdn.microsoft.com/Forums/windowsazure/ja-jp/home?forum=AzureRemoteApp) で無料のコミュニティのサポートを利用することもできます。
- **RemoteApp の料金について教えてください。**[Azure RemoteApp Pricing Details (Azure RemoteApp の料金詳細)](http://azure.microsoft.com/ja-jp/pricing/details/remoteapp/) を参照してください。
- **フィードバックはどのように送信するのですか。「**[feedback forum (フィードバック フォーラム)](http://feedback.azure.com/forums/247748-azure-remoteapp)」を参照してください。
- **Azure RemoteApp の詳細についてはどこに連絡すればよいでしょうか。**質問を投稿する最適の場所である [discussion forum (ディスカッション フォーラム)](http://social.msdn.microsoft.com/Forums/windowsazure/ja-jp/home?forum=AzureRemoteApp) に加えて RemoteApp のすべてに関して説明する [Ask the Experts webinar (エキスパートに質問するウェビナー)](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html) に参加できます。
- **RemoteApp に関するドキュメントはありますか。**お問い合わせありがとうございます。ポータルの Help Drawer のヘルプ コンテンツ (ポータルのいずれかのページの **?** をクリックするだけです) に加えて、次の資料では RemoteApp のすべての詳細を参照できます。
	- **はじめに:**
		- [RemoteApp とは何ですか。](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-whatis/)
		- [RemoteApp テンプレート イメージとは何ですか。](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-images/)
		- [How does licensing work? (ライセンスはどのように機能しますか。)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-licensing/)
		- [How do RemoteApp and Office work together? (RemoteApp と Office はどのように連携しますか。)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-o365/)
	- **デプロイ:**
		- [Create a custom template image (カスタム テンプレート イメージを作成する)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-custom-image/)
		- [Create a hybrid collection (ハイブリッド接続を作成する)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [Create a cloud collection (クラウド コレクションを作成する)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-cloud-deployment/)
		- [Configure Azure Active Directory for RemoteApp (RemoteApp に Azure Active Directory を構成する)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-ad/)
		- [RemoteApp のアプリを公開する](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-publish/)
	- **管理:**
		- [Add users (ユーザーを追加する)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-user/)
		- [Best practices for configuring and using RemoteApp (Azure RemoteApp を構成し、使用するためのベスト プラクティス)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-bestpractices/)	

	ビデオ。RemoteApp についての多くのビデオがあります。概要について説明する ([Introduction to Azure RemoteApp (Azure RemoteApp の概要)](http://azure.microsoft.com/ja-jp/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/))、展開について説明する ([Cloud deployment (クラウドの展開)](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) および [Hybrid deployment (ハイブリッドの展開)](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be))。確認してください。


<!--HONumber=35.2-->
