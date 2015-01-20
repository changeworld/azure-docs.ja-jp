<properties title="What is RemoteApp?" pageTitle="RemoteApp とは何ですか。" description="Azure RemoteApp について説明します。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Azure RemoteApp とは
 
Azure RemoteApp は、Azure をサポートするリモート デスクトップ サービスによって内部設置型の Microsoft の RemoteApp プログラムの機能を提供します。Azure RemoteApp は、多くのさまざまなユーザーのデバイスのアプリケーションにセキュリティで保護されたリモート アクセスを提供します。

RemoteApp を Azure に移動すると、複雑な内部設置型の構成に煩わされることなく、ストレージ、スケーラビリティ、および Azure へのグローバルなアクセスが利用できます。Microsoft が Azure に提供する信頼性の保証されたメンテナンスにより、お客様はビジネスの使用に最適なアプリを作成するといった、より重要な問題に集中することができます。Azure RemoteApp のもう 1 つの利点は、アクセスのしやすさにあります - ユーザーは、Windows、iOS、Mac OS X、および Android デバイスから RemoteApp プログラムにアクセスできます。ユーザーは任意の環境でアプリを使用することができ、お客様は Azure 管理ポータルを使用してそれらのアプリを管理することができます。 

RemoteApp の詳細についてお読みになるか、または既に説明した「[今すぐ試す](http://azure.microsoft.com/ja-jp/services/remoteapp/)」を参照してください。

Azure の RemoteApp について質問はありますか。「[FAQ (よく寄せられる質問)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-faq/)」を参照してください。

Azure RemoteApp は [Microsoft 仮想デスクトップ インフラストラクチャ](http://www.microsoft.com/ja-jp/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)の一部です。

**新規 !** Azure RemoteApp の詳細または RemoteApp の大規模な検証については、週単位の「[ask the experts webinar (エキスパートに質問するウェビナー)](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)」を参照してください。

##RemoteApp の展開オプション
RemoteApp のコレクションには、次の 2 種類があります:


- **クラウド コレクション**は、Azure クラウドにホストされ、プログラムのすべてのデータがクラウドに格納されます。ユーザーは、Microsoft アカウントまたは Azure Active Directory と同期また連携した企業資格情報でログインしてアプリにアクセスできます。
- **ハイブリッド コレクション** では、Azure クラウドにホストされ、データはクラウドに格納されますが、ユーザーはローカル ネットワークに格納されたデータとリソースにアクセスすることもできます。ユーザーは、Azure Active Directory と同期また連携した企業資格情報でログインしてアプリにアクセスできます。

###クラウド コレクション

[クラウド RemoteApp コレクション](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-cloud-deployment/)はクラウドのアプリケーションをホストするスタンドアロンの方法を提供します。クラウド コレクションは、ローカル ネットワークに接続するのではなく、Azure のクラウドにのみ存在します。

RemoteApp 評価版の一部として、Office 365 ProPlus または Office 2013 アプリをプレインストールしていますので、すぐにユーザーと共有できます。利用可能なソフトウェアを活用する場合は、サービスをすばやくプロビジョニングできます。

クラウド コレクションを Office アプリと使用する他の利点として、アプリとオペレーティング システム (サービスが組み込まれているシステム) は定期的な更新を通じて常に最新の状態に保たれ、Microsoft Anti-Malware Endpoint Protection は継続的な防衛を提供します。エンドユーザーは、Microsoft アカウントまたは企業の資格情報を使用してアプリにアクセスします。管理者として注意することは、誰がどのアプリケーションにアクセスしているのかを確認することだけです。

またクラウド コレクションを作成してカスタム アプリケーションまたはユーザー用のアプリケーション セットを共有することができます。そのためには、コレクションを作成するときに、[カスタム テンプレート イメージの作成し](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-custom-image/) (アプリを RemoteApp に公開する方法)、そのイメージ (Office 2013 のイメージではなく) を選択する必要があります。 

###ハイブリッド コレクション
[ハイブリッド RemoteApp コレクション](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-hybrid-deployment/)では、ユーザーにカスタム アプリケーション のセットとローカル ネットワーク内のデータとリソースへのアクセスを提供することができます。クラウド コレクションと使用されるカスタム イメージとは異なり、ハイブリッド コレクションに作成するイメージは、ドメインに参加した環境でアプリを実行し、ローカル ネットワークとデータへのフル アクセスを付与します。

Active Directory を Azure Active Directory と統合すると (DirSync を使用)、ユーザーは企業の資格情報を使用してアプリとデータにアクセスできるようになります。Active Directory で仕事用アカウントを使用する場合は、企業のポリシーをクラウドで使用して RemoteApp を通じて提供するアプリを管理することができます。

RD セッション ホストの役割サービスで Windows Server 2012 R2 にテンプレート イメージを構築する場合は、ユーザーに公開するアプリにいくつかの制限があります。アプリはが、そのテンプレート イメージの環境で正しく機能する場合は、エンドユーザーはそれらに RemoteApp を介してアクセスできます。 

###コレクションの更新
ハイブリッド コレクションとクラウドのコレクション間の主な違いの 1 つは、ソフトウェア更新の処理方法です。プレインストールの Office 365 ProPlus または Office 2013 のイメージを使用するクラウド コレクションでは、更新プログラムについて心配する必要はありません。サービスはそれ自体が継続的に保守され、更新プログラムはアプリとオペレーティング システムの両方に適用されます。

カスタム テンプレート イメージを使用するクラウド コレクションとハイブリッド コレクションでは、イメージとアプリを保守する必要があります。ドメインに参加しているイメージでは、Windows Update、グループ ポリシー、または System Center などのツールを使用して更新プログラムを制御できます。

カスタム テンプレート イメージの更新後に、Azure クラウドに新しいイメージをアップロードし、新しいイメージを使用するコレクションを更新します。([RemoteApp のクイック スタート] ページまたはダッシュ ボードから実行できます。)

##サポートされている RemoteApp クライアント
Azure RemoteApp は、Windows および Windows RT の RemoteApp クライアント アプリケーションと Mac、iOS および Android 用の Microsoft のリモート デスクトップ アプリでサポートされています。ユーザーは、モバイル デバイスまたはコンピュータ デバイスでこれらのアプリを使用して、新しい RemoteApp プログラムにアクセスできます。

##次のステップ
始めましょう。実際に使ってみるこれらの記事は RemoteApp を開始するために役立ちます:

- [RemoteApp のカスタム テンプレート イメージの作成方法](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-custom-image/)
- [RemoteApp のクラウド コレクションの作成方法](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-cloud-deployment/)
- [RemoteApp のクラウド ハイブリッド コレクションの作成方法](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-hybrid-deployment/)
- [How does licensing work in RemoteApp? (RemoteApp のライセンスはどのような仕組みになっていますか。)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-licensing/)
- [Best practices for using Azure RemoteApp (Azure RemoteApp を使用するためのベスト プラクティス)](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-bestpractices/)
- [Azure RemoteApp よく寄せられる質問](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-faq/)

<!--HONumber=35.2-->
