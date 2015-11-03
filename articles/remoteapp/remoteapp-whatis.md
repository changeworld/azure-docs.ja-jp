<properties 
	pageTitle="Azure RemoteApp とは | Microsoft Azure" 
	description="Azure RemoteApp を使用してアプリとリソースを任意のデバイスと共有する方法について説明します。" 
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
	ms.date="10/23/2015" 
	ms.author="elizapo"/>

# Azure RemoteApp とは

Azure RemoteApp は、Azure をサポートするリモート デスクトップ サービスによってオンプレミスの Microsoft の RemoteApp プログラムの機能を提供します。Azure RemoteApp は、多くのさまざまなユーザーのデバイスのアプリケーションにセキュリティで保護されたリモート アクセスを提供します。

Azure RemoteApp では、ほぼすべてのデバイスでアプリとリソースを共有できます。アプリはクラウドでホストされます。つまり、管理者は、ユーザーの需要を満たすためにハードウェアとスケーリングに対処する必要はありません。管理者が実行する必要があるのは共有するアプリのアップロードだけであり、後はユーザーにそれらのアプリを使用してもらうだけです。[ユーザーはデバイスを持ち続け](remoteapp-clients.md)、管理者が Azure ポータルを通してすべてを管理します。さらに、会社の資格情報を使用することを選択して、アプリとデータのセキュリティを確保することもできます。

Azure RemoteApp の詳細については、以下を参照してください。既に説明に納得いただいた場合は、[今すぐお試しになれます](http://azure.microsoft.com/services/remoteapp/)。

Azure の RemoteApp について質問はありますか。 「[FAQ (よく寄せられる質問)](remoteapp-faq.md)」を参照してください。

Azure RemoteApp は、[Microsoft の仮想デスクトップ インフラストラクチャ](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)の一部です。

**新しい情報!** Azure RemoteApp について詳細を知りたいですか。 または、実際に検証する準備ができていますか。 毎週開催される[専門家にたずねるウェビナー](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website)に参加してください。

## Azure RemoteApp コレクション
[Azure RemoteApp のコレクション](remoteapp-collections.md)には、次の 2 種類があります。


- **クラウド コレクション**は、クラウドでホストされ、プログラムのデータはそのクラウドに格納されます。ユーザーは、Microsoft アカウントまたは Azure Active Directory と同期また連携した企業資格情報でログインしてアプリにアクセスできます。

	クラウド コレクションを選択するのは、会社のプライベート ネットワークで、共有するアプリケーションに任意のリソースへの接続が必要ない場合 (たとえば、VPN デバイスを使用する場合) です。アプリケーションがインターネット、OneDrive、または Azure 上のリソースを必要とする場合は、クラウド コレクションが適しています。また、作成するのも、最も簡単です。

- **ハイブリッド コレクション**では、Azure クラウドにホストされ、データはクラウドに格納されますが、ユーザーはローカル ネットワークに格納されたデータとリソースにアクセスすることもできます。ユーザーは、Azure Active Directory と同期また連携した企業資格情報でログインしてアプリにアクセスできます。

	ハイブリッド コレクションを選択するのは、会社のプライベート ネットワーク上のリソースへの接続が必要な場合です。たとえば、アプリケーションに、次のいずれかへのアクセスが必要な場合です。

	- イントラネット上に配置されたファイル サーバー
	- Quicken
	- ファイアウォールの内側にあるデータベース

	これは一般に、クラウドに移動することができない多数のリソースをプライベート ネットワーク上に持つ大企業にとって役立ちます。

各コレクションには、ネットワークを含めて異なるオプションがあるため、[どのコレクションが最適か](remoteapp-collections.md)を見つけてください。


### コレクションの更新
ハイブリッド コレクションとクラウドのコレクション間の主な違いの 1 つは、ソフトウェア更新の処理方法です。プレインストールの Office 365 ProPlus または Office 2013 のイメージを使用するクラウド コレクションでは、更新プログラムについて心配する必要はありません。サービスはそれ自体が継続的に保守され、更新プログラムはアプリとオペレーティング システムの両方に適用されます。

カスタム テンプレート イメージを使用するクラウド コレクションとハイブリッド コレクションでは、イメージとアプリを保守する必要があります。ドメインに参加しているイメージでは、Windows Update、グループ ポリシー、または System Center などのツールを使用して更新プログラムを制御できます。

カスタム テンプレート イメージの更新後に、Azure クラウドに新しいイメージをアップロードし、新しいイメージを使用するコレクションを更新します。(Azure RemoteApp の **[クイック スタート]** ページまたはダッシュボードから実行できます)。

詳細については、[コレクションの更新](remoteapp-update.md)に関するページを参照してください。

## サポートされている RemoteApp クライアント
Azure RemoteApp は、Windows および Windows RT の RemoteApp クライアント アプリケーションと Mac、iOS および Android 用の Microsoft のリモート デスクトップ アプリでサポートされています。ユーザーは、モバイル デバイスまたはコンピューティング デバイスでこれらのアプリを使用して、新しい Azure RemoteApp プログラムにアクセスできます。

クライアントの詳細については、[Azure RemoteApp でのアプリへのアクセス](remoteapp-clients.md)に関するページを参照してください。

## 次のステップ
始めましょう。 実際に使ってみてください。 以下の記事が Azure RemoteApp を開始するのに役立ちます。

- [Azure RemoteApp にはどのような種類のコレクションが必要ですか。](remoteapp-collections.md)
- [Azure RemoteApp イメージの作成](remoteapp-imageoptions.md)
- [Azure RemoteApp のクラウド コレクションの作成方法](remoteapp-create-cloud-deployment.md)
- [Azure RemoteApp のハイブリッド コレクションの作成方法](remoteapp-create-hybrid-deployment.md)
- [RemoteApp のライセンスはどのような仕組みになっていますか。](remoteapp-licensing.md)
- [Azure RemoteApp を構成し、使用するためのベスト プラクティス](remoteapp-bestpractices.md)
- [Azure RemoteApp よく寄せられる質問](remoteapp-faq.md)
 

### サポートのお願い 
記事を評価したり、下欄にコメントを投稿したりするだけでなく、記事そのものに変更を加えられることをご存じですか。 何かが不足している場合 誤りがある場合、 わかりにくいことが書いてある場合は、 上へスクロールし、**[GitHub で編集]** をクリックして、変更を加えることができます。変更はこちらで確認し、承認されると、変更点や改善点がこのページに反映されます。

<!---HONumber=Nov15_HO1-->