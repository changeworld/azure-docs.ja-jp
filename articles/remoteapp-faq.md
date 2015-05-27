<properties 
	pageTitle="Azure RemoteApp よく寄せられる質問" 
	description="Azure RemoteApp についてよく寄せられる質問" 
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
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="elizapo"/>

# Azure RemoteApp よく寄せられる質問
Azure RemoteApp について次のような質問が寄せられています。他にもありますか? 「[RemoteApp フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp)」を参照して、必要な情報についてお知らせください。

## Azure RemoteApp とは ##


- **Azure RemoteApp とは。** RemoteApp は、リモート デスクトップ サービスを基盤とし、内部設置型の Microsoft RemoteApp の機能を Azure で使用できるようにする Azure サービスです。 RemoteApp は、多くのさまざまなユーザーのデバイスのアプリケーションにセキュリティで保護されたリモート アクセスを提供します。詳細については、「[Azure RemoteApp とは](remoteapp-whatis.md)」を参照してください。
- **デプロイメントのオプションの 2 つの種類を教えてください。** RemoteApp のデプロイ (またはコレクション) には、クラウドおよびハイブリッドの 2 つの種類があります。組織にとって、どの[デプロイ オプション](remoteapp-whatis.md)が最適であるかを見つけ出します。

## サポートされる構成 ##


- **カスタム基幹業務 (LOB) アプリケーションはサポートされていますか。** はい。Azure RemoteApp でカスタム アプリケーションを使用するには、[カスタム テンプレート イメージ](remoteapp-create-custom-image.md)を作成し、RemoteApp コレクションにアップロードします。
- **自社のカスタム LOB アプリケーションは、Azure RemoteApp で動作しますか。** この質問の回答を見つけ出す最善の方法は、テストすることです。[アプリケーションの互換性要件](http://www.microsoft.com/download/details.aspx?id=18704)を確認し、[RD 互換性センター](http://www.rdcompatibility.com/compatibility/default.aspx)で提供される内容をチェックします。
- **組織にとって最適なデプロイ方法は、クラウドとハイブリッドのどちらですか。** ハイブリッド コレクションは、シングル サインオン (SSO) との完全な統合、およびセキュリティで保護された内部設置型のネットワーク接続を必要としている場合に、最も包括的なエクスペリエンスを提供します。クラウド コレクションでは、複数の認証方法を使用することにより、アジャイルかつ簡単な方法を使用して、分離されたデプロイメントを構築できます。デプロイメント オプションの詳細については、「[Azure RemoteApp とは](remoteapp-whatis.md)」を参照してください。
- **ハイブリッド コレクションは、VNET を必要とします。既存の NET を使用できますか。** 現在はできませんが、使用の希望については承知しております。これについては現在、対応中ですが、[ここに示されている手順](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx)に従うことによって、既存の VNET を Azure RemoteApp VNET に接続できます。
- **クラウドまたは既存の仮想マシンを、RemoteApp コレクションのテンプレートとして使用できますか。** はい。 Azure VM に基づくイメージを作成することも、サブスクリプションに含まれるイメージの 1 つを使用することも、またはカスタム イメージを作成することもできます。[RemoteApp のイメージ オプション](remoteapp-imageoptions.md)を参照してみてください。
- **SQL または別のデータベースが、社内または Azure にあります。どちらの種類のデプロイメントを使用すべきでしょうか。** これは SQL またはバックエンドのデータベースの場所によります。データベースがプライベート ネットワークにある場合は、ハイブリッド コレクションを使用します。データベースがインターネットに表示され、クライアント接続で接続できる場合は、クラウド コレクションを使用できます。
- **ドライブの割り当て、USB とシリアル ポート、クリップボードの共有、およびプリンターのリダイレクトについてはどうでしょうか。** Azure RemoteApp は、これらのすべての機能についてサポートしています。クリップボードの共有とプリンターのリダイレクトは、既定で有効になります。リダイレクトの詳細については、「[Using redirection in Azure RemoteApp](remoteapp-redirection.md)」を参照してください。 


- **認証については、 どの方法がサポートされていますか。** クラウド コレクションは、Office 365 アカウントでもある Microsoft アカウントと Azure Active Directory アカウントをサポートしています。ハイブリッド コレクションは、([Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx) のようなツールを使用して) Windows Server Active Directory のデプロイメントから同期されている Azure Active Directory アカウントのみをサポートします。具体的には、パスワード同期オプションで同期されているか、または Active Directory フェデレーション サービス (AD FS) の構成されたフェデレーションのいずれかで同期されます。[多要素認証 (MFA)](../../services/multi-factor-authentication/) を構成することもできます。

	**注:** Azure Active Directory ユーザーは、サブスクリプションに関連付けられているテナントに属している必要があります (サブスクリプションは、ポータルの **[設定]** タブで表示および変更できます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp](remoteapp-changetenant.md)」を参照してください)。

- **使用している Azure Active Directory アカウントにアクセス許可を付与できないのはなぜですか。** Azure Active Directory ユーザーは、サブスクリプションに関連付けられているディレクトリに属している必要があります。このディレクトリは、ポータルの [設定] タブで表示および変更できます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp](remoteapp-changetenant.md)」を参照してください。
- **クライアント アプリケーションがサポートするデバイスとオペレーティング システムはどれでしょう。** クライアント アプリケーションは、Windows 8.1、Windows 8、Windows 7 SP1、iOS、Mac OS X、Windows RT、Android デバイス、および Windows Phone で動作します。Windows 10 Preview もサポートされます。
 
	今すぐ RemoteApp を[ダウンロード](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx)。
- **Azure RemoteApp はシン クライアントをサポートしますか。** はい、次の Windows Embedded シン クライアントがサポートされます。
	- Windows Embedded Standard 7 with Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **リモート デスクトップ セッション ホスト (RDSH) 用にサポートされる Windows Server のバージョンはどれですか。** Windows Server 2012 R2 です。

##サポートとフィードバック

- **このサービスは無料で試すことができますか。** はい。30 日間、無料で試すことができます。試行期間が終了すると、(運用環境で使用可能な) 有料アカウントに移行するか、サービスの使用を終了することができます。[manage.windowsazure.com](http://manage.windowsazure.com) にアクセスして無料評価版を開始し、RemoteApp の新しいインスタンスを作成してみてください。無料評価版では、2 つの RemoteApp インスタンスを作成でき、インスタンスあたり 10 ユーザーをサポートできます。  試行期間は、30 日であることに注意してください。
- **RemoteApp のサポート プランについてはどうですか。** 課金およびサブスクリプション管理が、無料でサポートされます。テクニカル サポートが [Azure のサービス プラン](../../../support/plans/)を通して提供されます。[Azure のディスカッション フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)を通して、無料のコミュニティ サポートも得ることができます。 
- **RemoteApp の料金についてはどうですか。** 「[Azure の料金体系について](../../../pricing/details/remoteapp/)」を参照してください。
- **フィードバックを発行する方法を教えてください。** 「[フィードバック フォーラム](http://feedback.azure.com/forums/247748-azure-remoteapp)」にアクセスしてください。
- **Azure RemoteApp についての問い合わせ先を教えてください。** 質問を投稿するのに最適な場所である[ディスカッション フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)に加えて、RemoteApp のあらゆることに関する情報が週次に提供される「[Ask the Experts Webinar](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)」にも参加できます。
- **RemoteApp に関するドキュメントはありますか。** ご問い合わせありがとうございます。ポータルのヘルプ ドロアーのヘルプ コンテンツ (ポータルのいずれかのページの **?** をクリックするだけです) に加えて、RemoteApp のすべてについて詳細に説明する次の資料を参照できます。
	- **概要:**
		- [Azure RemoteApp とは](remoteapp-whatis.md)
		- [What is in the RemoteApp template images?](remoteapp-images.md)
		- [How does licensing work in Azure RemoteApp?](remoteapp-licensing.md)
		- [Using Office 365 with Azure RemoteApp](remoteapp-o365.md)
		- [Using redirection in Azure RemoteApp](remoteapp-redirection.md)
	- **デプロイ:**
		- [RemoteApp のカスタム テンプレート イメージの作成方法](remoteapp-create-custom-image.md)
		- [RemoteApp のハイブリッド デプロイメントの作成方法](remoteapp-create-hybrid-deployment.md)
		- [RemoteApp のクラウド コレクションの作成方法](remoteapp-create-cloud-deployment.md)
		- [Configuring Active Directory for Azure RemoteApp](remoteapp-ad.md)
		- [How to publish an app in RemoteApp](remoteapp-publish.md)
	- **管理:**
		- [How to add a user in RemoteApp](remoteapp-user.md)
		- [Best practices for configuring and using Azure RemoteApp](remoteapp-bestpractices.md)	

	ビデオ。 RemoteApp についての多くのビデオがあります。これらの一部は概要 ([Azure RemoteApp の概要](http://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) について説明し、一部はデプロイメント ([Cloud Deployment Overview](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) (クラウドのデプロイメント概要) および [Hybrid Deployment](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be) (ハイブリッドのデプロイメント)) について説明します。確認してください。


<!--HONumber=54-->