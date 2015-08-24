<properties 
	pageTitle="Azure RemoteApp とは" 
	description="Azure RemoteApp について説明します。" 
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
	ms.date="08/05/2015" 
	ms.author="elizapo"/>

# Azure RemoteApp とは

Azure RemoteApp は、Azure をサポートするリモート デスクトップ サービスによってオンプレミスの Microsoft の RemoteApp プログラムの機能を提供します。Azure RemoteApp は、多くのさまざまなユーザーのデバイスのアプリケーションにセキュリティで保護されたリモート アクセスを提供します。

RemoteApp を Azure に移動すると、複雑なオンプレミスの構成に煩わされることなく、ストレージ、スケーラビリティ、および Azure へのグローバルなアクセスが利用できます。Microsoft が Azure の保守を実施するため、Azure の信頼性が確保され、お客様はビジネスにとって最適なアプリケーションを作成するなど、より重要な問題に焦点を当てる時間を手に入れることができます。Azure RemoteApp の別の利点は、アクセスのしやすさです。ユーザーは、Windows、iOS、Mac OS X、および Android デバイスから RemoteApp プログラムにアクセスできます。ユーザーは、希望する環境でアプリケーションを使用できる一方、管理者は Azure 管理ポータルを使用して、それらのアプリケーションを管理できます。

RemoteApp の詳細については、以下を参照してください。既に説明に納得いただいた場合は、[今すぐお試しになれます](http://azure.microsoft.com/services/remoteapp/)。

Azure の RemoteApp について質問はありますか。 「[FAQ (よく寄せられる質問)](remoteapp-faq.md)」を参照してください。

Azure RemoteApp は、[Microsoft の仮想デスクトップ インフラストラクチャ](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)の一部です。

**新しい情報!** Azure RemoteApp について詳細を知りたいですか。 または、実際に検証する準備ができていますか。 毎週開催される[専門家にたずねるウェビナー](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website)に参加してください。

## RemoteApp コレクション
RemoteApp のコレクションには、次の 2 種類があります:


- **クラウド コレクション**は、Azure クラウドにホストされ、プログラムのすべてのデータがそのクラウドに格納されます。ユーザーは、Microsoft アカウントまたは Azure Active Directory と同期また連携した企業資格情報でログインしてアプリにアクセスできます。
- **ハイブリッド コレクション**では、Azure クラウドにホストされ、データはクラウドに格納されますが、ユーザーはローカル ネットワークに格納されたデータとリソースにアクセスすることもできます。ユーザーは、Azure Active Directory と同期また連携した企業資格情報でログインしてアプリにアクセスできます。

### クラウド コレクション

[クラウド RemoteApp コレクション](remoteapp-create-cloud-deployment.md)は、クラウドのアプリケーションをホストするスタンドアロンの方法を提供します。クラウド コレクションは、ローカル ネットワークに接続するのではなく、Azure のクラウドにのみ存在します。

RemoteApp 評価版の一部として、Office 365 ProPlus または Office 2013 アプリをプレインストールしていますので、すぐにユーザーと共有できます。利用可能なソフトウェアを活用する場合は、サービスをすばやくプロビジョニングできます。

クラウド コレクションを Office アプリと使用する他の利点として、アプリとオペレーティング システム (サービスが組み込まれているシステム) は定期的な更新を通じて常に最新の状態に保たれ、Microsoft Anti-Malware Endpoint Protection は継続的な防衛を提供します。エンド ユーザーは、アプリへのアクセスに Microsoft アカウントまたは企業の資格情報を使用します。管理者として注意することは、誰がどのアプリケーションにアクセスしているのかを確認することだけです。

またクラウド コレクションを作成してカスタム アプリケーションまたはユーザー用のアプリケーション セットを共有することができます。そのためには、[カスタム イメージを作成](remoteapp-imageoptions.md)し (これでアプリケーションを RemoteApp に公開する)、コレクションを作成するときにそのイメージ (Office 2013 のイメージではない) を選択するだけで済みます。

#### クラウドを選択する状況

クラウド コレクションを選択するのは、会社のプライベート ネットワークで、共有するアプリケーションに任意のリソースへの接続が必要ない場合 (たとえば、VPN デバイスを使用する場合) です。アプリケーションがインターネット、OneDrive、または Azure 上のリソースを必要とする場合は、クラウド コレクションが適しています。また、作成するのも、最も簡単です。


### ハイブリッド コレクション
[ハイブリッド RemoteApp コレクション](remoteapp-create-hybrid-deployment.md)では、ユーザーにカスタム アプリケーションのセットとローカル ネットワーク内のデータとリソースへのアクセスを提供することができます。クラウド コレクションと使用されるカスタム イメージとは異なり、ハイブリッド コレクションに作成するイメージは、ドメインに参加した環境でアプリを実行し、ローカル ネットワークとデータへのフル アクセスを付与します。

Active Directory を Azure Active Directory と統合すると (DirSync を使用)、ユーザーは企業の資格情報を使用してアプリとデータにアクセスできるようになります。Active Directory で仕事用アカウントを使用する場合は、企業のポリシーをクラウドで使用して RemoteApp を通じて提供するアプリを管理することができます。

RD セッション ホストの役割サービスで Windows Server 2012 R2 にテンプレート イメージを構築する場合は、ユーザーに公開するアプリにいくつかの制限があります。アプリケーションがそのテンプレート イメージの環境で正しく機能する場合、エンド ユーザーはそれらのアプリケーションに RemoteApp を通じてアクセスできます。

#### ハイブリッドを選択する状況

ハイブリッド コレクションを選択するのは、会社のプライベート ネットワーク上のリソースへの接続が必要な場合です。たとえば、アプリケーションに、次のいずれかへのアクセスが必要な場合です。

- イントラネット上に配置されたファイル サーバー
- Quicken
- ファイアウォールの内側にあるデータベース

これは一般に、クラウドに移動することができない多数のリソースをプライベート ネットワーク上に持つ大企業にとって役立ちます。

### コレクションの更新
ハイブリッド コレクションとクラウドのコレクション間の主な違いの 1 つは、ソフトウェア更新の処理方法です。プレインストールの Office 365 ProPlus または Office 2013 のイメージを使用するクラウド コレクションでは、更新プログラムについて心配する必要はありません。サービスはそれ自体が継続的に保守され、更新プログラムはアプリとオペレーティング システムの両方に適用されます。

カスタム テンプレート イメージを使用するクラウド コレクションとハイブリッド コレクションでは、イメージとアプリを保守する必要があります。ドメインに参加しているイメージでは、Windows Update、グループ ポリシー、または System Center などのツールを使用して更新プログラムを制御できます。

カスタム テンプレート イメージの更新後に、Azure クラウドに新しいイメージをアップロードし、新しいイメージを使用するコレクションを更新します。(RemoteApp の **[クイック スタート]** ページまたはダッシュボードから実行できます)。

詳細については、[コレクションの更新](remoteapp-update.md)に関するページを参照してください。

## サポートされている RemoteApp クライアント
Azure RemoteApp は、Windows および Windows RT の RemoteApp クライアント アプリケーションと Mac、iOS および Android 用の Microsoft のリモート デスクトップ アプリでサポートされています。ユーザーは、モバイル デバイスまたはコンピューティング デバイスでこれらのアプリを使用して、新しい RemoteApp プログラムにアクセスできます。

クライアントの詳細については、[Azure RemoteApp でのアプリへのアクセス](remoteapp-clients.md)に関するページを参照してください。

## 次のステップ
始めましょう。 実際に使ってみてください。 これらの記事は RemoteApp を開始するために役立ちます:

- [RemoteApp イメージの作成](remoteapp-imageoptions.md)
- [RemoteApp のクラウド コレクションの作成方法](remoteapp-create-cloud-deployment.md)
- [RemoteApp のハイブリッド コレクションの作成方法](remoteapp-create-hybrid-deployment.md)
- [RemoteApp のライセンスはどのような仕組みになっていますか?](remoteapp-licensing.md)
- [Azure RemoteApp を構成し、使用するためのベスト プラクティス](remoteapp-bestpractices.md)
- [Azure RemoteApp よく寄せられる質問](remoteapp-faq.md)
 

<!---HONumber=August15_HO7-->