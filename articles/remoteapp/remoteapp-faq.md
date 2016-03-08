<properties 
	pageTitle="Azure RemoteApp の FAQ | Microsoft Azure" 
	description="Azure RemoteApp についてよく寄せられる質問の回答を確認する。" 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/25/2016" 
	ms.author="elizapo"/>

# Azure RemoteApp よく寄せられる質問
Azure RemoteApp について次のような質問が寄せられています。他にもありますか? 「[RemoteApp フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp)」にアクセスして必要な情報についてお知らせいただくか、下のコメント欄にご記入ください。

## Azure RemoteApp とは ##


- **Azure RemoteApp とは。** RemoteApp とは、多くのさまざまなユーザー デバイスからアプリケーションへのセキュリティで保護されたリモート アクセスを提供する際に役立つ Azure サービスです。詳細については、「[Azure RemoteApp とは](remoteapp-whatis.md)」を参照してください。
- **デプロイメント オプションにはどのようなものがありますか。** RemoteApp のコレクションには、クラウドとハイブリッドの 2 つの種類があります。ドメインへの参加の要不要など、さまざまな要素によって必要なコレクションが決まります。この決定については、[こちらのページ](remoteapp-collections.md)で詳しく説明しています。

## Azure RemoteApp の使用に関するクイック ヒント ##
- **切断されるまでの時間はどのくらいですか。 どれくらいアイドル状態が続くとサインアウトされますか。** 4 時間です。ユーザーのアイドル状態が 4 時間続くと、自動的に Azure RemoteApp からサインアウトされます。その他の既定の設定については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。
- **このサービスは無料で試すことができますか。** はい。30 日間使える無料試用版があります。試行期間が終了すると、(運用環境で使用可能な) 有料アカウントに切り替えるか、サービスの使用を終了することができます。[portal.azure.com](http://portal.azure.com) にアクセスして無料試用版を開始し、RemoteApp の新しいインスタンスを作成してみてください。無料試用版では、2 つの RemoteApp インスタンスを作成でき、インスタンスあたり 10 ユーザーをサポートできます。 試行期間は、30 日であることに注意してください。
## Azure RemoteApp サブスクリプションの詳細 ##

- **サービスの制限とは何ですか。** Azure RemoteApp の既定の設定とサービスの制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。他に質問がある場合は、お知らせください。
- **必要なユーザー数は、どの程度でしょうか。** 少なくとも 20 ユーザーが必要です。明確にするために繰り返しますと、最低ユーザー数は 20 となっています。つまり、20 ユーザー分の代金が課金されることになります。 
- **RemoteApp の料金についてはどうですか。** 「[Azure RemoteApp の料金体系について](../../../pricing/details/remoteapp/)」を参照してください。
- **コレクションの種類によってはコストがかかるものはありますか。** コレクションに対する要件によってはそうなる場合があります。ハイブリッド コレクションの場合、Azure RemoteApp からオンプレミス ネットワークへの接続が必要です。既存の VNET と Express のルートを使用する場合、追加コストはありません。しかし新しい Azure VNET と、さらにゲートウェイまたは ExpressRoute を使用する場合、[VPN ゲートウェイ](../../../pricing/details/vpn-gateway)または [Express Route](../../../pricing/details/expressroute/) が課金されます。このコスト (詳細はリンクを参照) が、毎月の Azure の RemoteApp のコストに追加されます。

## サポートされているコレクションや使用すべきコレクションなど
- **カスタム基幹業務 (LOB) アプリケーションはサポートされていますか。** はい。Azure RemoteApp でカスタム アプリケーションを使用するには、[カスタム テンプレート イメージ](remoteapp-create-custom-image.md)を作成し、RemoteApp コレクションにアップロードします。
- **自社のカスタム LOB アプリケーションは、Azure RemoteApp で動作しますか。** この質問の回答を見つけ出す最善の方法は、テストすることです。[アプリケーションの互換性要件](http://www.microsoft.com/download/details.aspx?id=18704)を確認し、[RD 互換性センター](http://www.rdcompatibility.com/compatibility/default.aspx)で提供される内容をチェックします。
- **組織にとって最適なデプロイメント方法は、クラウドとハイブリッドのどちらですか。** ハイブリッド コレクションは、シングル サインオン (SSO) との完全な統合、およびセキュリティで保護されたオンプレミスのネットワーク接続を必要としている場合に、最も包括的なエクスペリエンスを提供します。クラウド コレクションでは、複数の認証方法を使用することにより、アジャイルかつ簡単な方法を使用して、分離されたデプロイメントを構築できます。デプロイメント オプションの詳細については、「[Azure RemoteApp とは](remoteapp-whatis.md)」を参照してください。
- **SQL または別のデータベースが、オンプレミスまたは Azure にあります。どちらの種類のデプロイメントを使用すべきでしょうか。** これは SQL またはバックエンドのデータベースの場所によります。データベースがプライベート ネットワークにある場合は、ハイブリッド コレクションを使用します。データベースがインターネットに表示され、クライアント接続で接続できる場合は、クラウド コレクションを使用できます。
- **ドライブの割り当て、USB とシリアル ポート、クリップボードの共有、およびプリンターのリダイレクトについてはどうでしょうか。** Azure RemoteApp は、これらのすべての機能についてサポートしています。クリップボードの共有とプリンターのリダイレクトは、既定で有効になります。リダイレクトの詳細については、「[Using redirection in Azure RemoteApp](remoteapp-redirection.md)」を参照してください。 


## テンプレート イメージ
- **クラウドまたは既存の仮想マシンを、RemoteApp コレクションのテンプレートとして使用できますか。** はい。 Azure VM に基づくイメージを作成することも、サブスクリプションに含まれるイメージの 1 つを使用することも、またはカスタム イメージを作成することもできます。[RemoteApp のイメージ オプション](remoteapp-imageoptions.md)を参照してみてください。


## ネットワーク オプション
- **ハイブリッド コレクションは、VNET を必要とします。既存の VNET を使用できますか。** 既存の VNET が Azure VNET であれば、使用できます。詳細については、[ハイブリッド コレクションの手順](remoteapp-create-hybrid-deployment.md)に関するページの「手順 1. 仮想ネットワークをセットアップする」を参照してください。
- **クラウドのコレクションで VNET を使用できますか。** はい、できます。詳細については、「[クラウド コレクションを作成する](remoteapp-create-cloud-deployment.md)」の特に手順 1 を参照してください。

## 認証オプション



- **認証については、 どの方法がサポートされていますか。** クラウド コレクションは、Office 365 アカウントでもある Microsoft アカウントと Azure Active Directory アカウントをサポートしています。ハイブリッド コレクションは、([Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx) のようなツールを使用して) Windows Server Active Directory のデプロイメントから同期されている Azure Active Directory アカウントのみをサポートします。具体的には、パスワード同期オプションで同期されているか、または Active Directory フェデレーション サービス (AD FS) の構成されたフェデレーションのいずれかで同期されます。[多要素認証 (MFA)](../../services/multi-factor-authentication/) を構成することもできます。

>[AZURE.NOTE]Azure Active Directory ユーザーは、サブスクリプションに関連付けられているテナントに属している必要があります(サブスクリプションは、ポータルの **[設定]** タブで表示および変更できます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp](remoteapp-changetenant.md)」を参照してください)。

- **使用している Azure Active Directory アカウントにアクセス許可を付与できないのはなぜですか。** Azure Active Directory ユーザーは、サブスクリプションに関連付けられているディレクトリに属している必要があります。このディレクトリは、ポータルの [設定] タブで表示および変更できます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp](remoteapp-changetenant.md)」を参照してください。

## クライアント - Azure RemoteApp へのアクセスにはどのようなデバイスを使用できますか?
さまざまなクライアントのインストール手順を含む、クライアントに関するよい情報は、「[Azure RemoteApp でのアプリへのアクセス](remoteapp-clients.md)」を参照してください。

- **クライアント アプリケーションがサポートするデバイスとオペレーティング システムはどれでしょう。** コンピューターとタブレット: 
	- Windows 10 (クライアント プレビュー)
	- Windows 8.1 および Windows 8
	- Windows 7 Service Pack 1
	- Mac OS X
	- Windows RT
	- Android タブレット
	- iPad と電話:
	- iPhone
	- Android フォン
	- Windows Phone
 
	今すぐ RemoteApp を[ダウンロード](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx)。
- **Azure RemoteApp はシン クライアントをサポートしますか。** はい、次の Windows Embedded シン クライアントがサポートされます。
	- Windows Embedded Standard 7
	- Windows Embedded 8 Standard
	- Windows Embedded 8.1 Industry Pro
	- Windows 10 IoT Enterprise

- **リモート デスクトップ セッション ホスト (RDSH) 用にサポートされる Windows Server のバージョンはどれですか。** Windows Server 2012 R2 です。

##サポートとフィードバック


- **RemoteApp のサポート プランについてはどうですか。** 課金およびサブスクリプション管理が、無料でサポートされます。テクニカル サポートが [Azure のサービス プラン](../../../support/plans/)を通して提供されます。[Azure のディスカッション フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)を通して、無料のコミュニティ サポートも得ることができます。 
- **フィードバックを発行する方法を教えてください。** 「[フィードバック フォーラム](https://feedback.azure.com/forums/247748-azure-remoteapp/)」にアクセスしてください。
- **Azure RemoteApp についての問い合わせ先を教えてください。** 質問を投稿するのに最適な場所である[ディスカッション フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)に加えて、RemoteApp のあらゆることに関する情報が週次に提供される「[Ask the Experts Webinar](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)」にも参加できます。
- **RemoteApp に関するドキュメントはありますか。** ご問い合わせありがとうございます。ポータルのヘルプ ドロアーのヘルプ コンテンツ (ポータルのいずれかのページの **?** をクリックするだけです) に加えて、RemoteApp のすべてについて詳細に説明する次の資料を参照できます。
	- **概要:**
		- [Azure RemoteApp とは](remoteapp-whatis.md)
		- [RemoteApp テンプレート イメージとは何ですか。](remoteapp-images.md)
		- [How does licensing work? (ライセンスはどのように機能しますか。)](remoteapp-licensing.md)
		- [RemoteApp と Office は、どうやって一緒に使うのですか?](remoteapp-o365.md)
		- [RemoteApp でリダイレクトはどのように使用できますか](remoteapp-redirection.md)?
	- **デプロイ:**
		- [RemoteApp のカスタム テンプレート イメージの作成方法](remoteapp-create-custom-image.md)
		- [RemoteApp のハイブリッド デプロイメントの作成方法](remoteapp-create-hybrid-deployment.md)
		- [RemoteApp のクラウド コレクションの作成方法](remoteapp-create-cloud-deployment.md)
		- [Configure Azure Active Directory for RemoteApp (RemoteApp に Azure Active Directory を構成する)](remoteapp-ad.md)
		- [RemoteApp のアプリを公開する](remoteapp-publish.md)
	- **管理:**
		- [ユーザーの追加](remoteapp-user.md)
		- [Best practices for configuring and using RemoteApp (Azure RemoteApp を構成し、使用するためのベスト プラクティス)](remoteapp-bestpractices.md)	

	ビデオ。 RemoteApp についての多くのビデオがあります。これらの一部は概要 ([Azure RemoteApp の概要](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) について説明し、一部はデプロイメント ([Cloud Deployment Overview](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) (クラウドのデプロイメント概要) および [Hybrid Deployment](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be) (ハイブリッドのデプロイメント)) について説明します。確認してください。

 
### サポートのお願い 
記事を評価したり、下にコメントを投稿したりするだけでなく、記事自体を変更できることを知っていましたか。 説明不足ですか。 間違いがありますか。 わかりにくいことが書いてありますか。 上にスクロールし、**[GitHub で編集]** をクリックすると変更できます。届いたら確認されます。サインオフ後、変更と改善をここで確認できます。

<!---HONumber=AcomDC_0302_2016-->