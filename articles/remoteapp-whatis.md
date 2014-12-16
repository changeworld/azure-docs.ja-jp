<properties title="What is RemoteApp?" pageTitle="RemoteApp とは" description="Learn about Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

#Azure RemoteApp とは

Azure RemoteApp は、リモート デスクトップ サービスを基盤とし、内部設置型の Microsoft RemoteApp プログラムの機能を Azure で使用できます。Azure RemoteApp では、多様なユーザー デバイスからアプリケーションに対して、セキュリティで保護されたリモート アクセスを提供します。

RemoteApp を Azure に移行する場合に、複雑な内部設置型の構成について心配することなく、Azure のストレージ、スケーラビリティ、およびグローバルなアクセスという利点を活用できます。Microsoft が Azure の保守を実施するため、Azure の信頼性が確保され、お客様はビジネスにとって最適なアプリケーションを作成するなど、より重要な問題に焦点を当てる時間を手に入れることができます。Azure RemoteApp の別の利点は、アクセスのしやすさです。ユーザーは、Windows、iOS、Mac OS X、および Android デバイスから RemoteApp プログラムにアクセスできます。ユーザーは、希望する環境でアプリケーションを使用できる一方、管理者は Azure の管理ポータルを使用して、それらのアプリケーションを管理できます。 

RemoteApp の詳細については、以下を参照してください。既に説明に納得いただいた場合は、[今すぐお試し](http://azure.microsoft.com/ja-jp/services/remoteapp/)になれます。

Azure RemoteApp は、[Microsoft の仮想デスクトップ インフラストラクチャ](http://www.microsoft.com/ja-jp/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)の一部です。

**新しい情報!** Azure RemoteApp について詳細を知りたいですか。または、実際に検証する準備ができていますか。毎週開催される[専門家にたずねるウェビナー](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)に参加してください。

##RemoteApp デプロイメント オプション
RemoteApp のデプロイメントには、次の 2 種類の方法があります。


- **クラウド デプロイメント**では、Azure クラウドがホストし、プログラムのすべてのデータがクラウドに格納されます。ユーザーは、Microsoft アカウントや Azure Active Directory と同期またはフェデレーションされた企業の資格情報でログインして、アプリケーションにアクセスできます。
- **ハイブリッド デプロイメント**では、Azure クラウドがホストし、Azure クラウドにデータが格納されるだけでなく、ユーザーはローカル ネットワークに格納されたデータとリソースにもアクセスできます。ユーザーは、Azure Active Directory と同期またはフェデレーションされた企業の資格情報でログインして、アプリケーションにアクセスできます。

###クラウド デプロイメント

[クラウドによる RemoteApp のデプロイメント](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-cloud-deployment/)では、スタンドアロン方式によりアプリケーションをクラウドでホストできます。クラウド デプロイメントは、ローカル ネットワークに接続するのではなく、Azure クラウド内にのみ存在します。

RemoteApp のプレビューの一部として、Office 2013 アプリをプレインストールし、ユーザーと共有できる状態で提供します。利用可能なソフトウェアを活用すると、サービスをすばやくプロビジョニングできます。

Office 2013 アプリを搭載したクラウド デプロイメントを使用するその他の利点として、アプリケーションと (サービスが基盤とする) オペレーティング システムが定期的な更新を通じて常に最新の状態で維持され、Microsoft のマルウェア対策のエンドポイント保護により、継続的な防御が提供されることが挙げられます。エンド ユーザーは、アプリへのアクセスに Microsoft アカウントまたは企業の資格情報を使用します。管理者が注意しなければならないのは、どのユーザーが、どのアプリケーションにアクセスが必要であるかを把握することだけです。

また、クラウド デプロイメントを作成して、ユーザー向けのカスタム アプリケーションまたは一連のアプリケーションを共有することもできます。このことは、デプロイメントを作成するときに、(RemoteApp へのアプリケーションの発行方法を定義する) [カスタム テンプレート イメージを作成](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-custom-image/)し、(Office 2013 のイメージではなく) 作成したイメージを選択するだけで可能です。 

###ハイブリッド デプロイメント
[ハイブリッドの RemoteApp デプロイメント](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-hybrid-deployment/)では、ローカル ネットワーク上でユーザーに一連のカスタム アプリケーションと、データやリソースへのアクセスを提供できます。クラウド デプロイメントで使用するカスタム イメージと違って、ハイブリッド デプロイメント用に作成するイメージは、ドメインに参加する環境でアプリケーションを実行し、ローカル ネットワークとデータへのフル アクセスを許可します。

Active Directory を Azure Active Directory に統合すると (ディレクトリ同期を使用)、ユーザーは企業の資格情報を使用して、アプリケーションおよびデータにアクセスできます。Active Directory の勤務先のアカウントを使用すると、企業のポリシーをクラウドに反映して、RemoteApp を通じて提供されるアプリを制御できます。

RD セッションのホスト役割サービスを使用した Windows Server 2012 R2 でテンプレート イメージを作成する限りは、ユーザーに発行するアプリケーションにほとんど制限がありません。アプリケーションがそのテンプレート イメージの環境で正しく機能する場合、エンド ユーザーはそれらのアプリケーションに RemoteApp を通じてアクセスできます。 

###デプロイメントの更新
ハイブリッド デプロイメントとクラウド デプロイメントの主な違いの 1 つに、ソフトウェアの更新の処理方法があります。クラウド デプロイメントでプレインストールされている Office 2013 のイメージを使用している場合は、更新について心配する必要はありません。このサービスは自律的に維持され、継続的にアプリケーションとオペレーティング システムの両方に更新を展開します。

ハイブリッド デプロイメントと、カスタム テンプレート イメージを使用するクラウド デプロイメントの場合、イメージとアプリケーションの維持は管理者が担当します。ドメインに参加しているイメージの場合、Windows Update、グループ ポリシー、System Center などのツールを使用して更新を制御できます。

カスタム テンプレート イメージを更新した後は、Azure のクラウドに新しいイメージをアップロードし、新しいイメージを使用するようデプロイメントを更新します (RemoteApp の [クイック スタート] ページまたはダッシュ ボードから実行できます)。

##サポートされている RemoteApp クライアント
Azure の RemoteApp のプレビュー リリースの一部として、Windows および Windows RT 用の新しい Microsoft RemoteApp クライアント アプリケーションと、iOS および Android 用の Microsoft リモート デスクトップ アプリケーションに更新プログラムをリリースしました。ユーザーは、モバイル デバイスまたはコンピューティング デバイスでこれらのアプリケーションを使用して、新しい RemoteApp プログラムにアクセスできます。

##次のステップ
まずは、実際に使ってみてください。次の記事が、RemoteApp の使用に役立ちます。

- [RemoteApp のカスタム テンプレート イメージの作成方法](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-custom-image/)
- [RemoteApp のクラウドのデプロイメントの作成方法](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-cloud-deployment/)
- [RemoteApp のハイブリッド デプロイメントの作成方法](http://azure.microsoft.com/ja-jp/documentation/articles/remoteapp-create-hybrid-deployment/)
