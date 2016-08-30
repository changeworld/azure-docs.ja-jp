<properties 
    pageTitle="Azure RemoteApp にはどのような種類のコレクションが必要ですか。 | Microsoft Azure" 
    description="Azure RemoteApp で使用可能なコレクションの種類について理解する。" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# Azure RemoteApp にはどのような種類のコレクションが必要ですか。

> [AZURE.IMPORTANT]
Azure RemoteApp の提供は終了しました。詳細については、[お知らせ](https://go.microsoft.com/fwlink/?linkid=821148)をご覧ください。

Azure RemoteApp では、任意のデバイスでユーザーとアプリおよびリソースを共有することができます。これを行うには、アプリおよびリソースを保持するためのコレクションを作成して、そのコレクションを他のユーザーと共有します。ネットワークと認証のオプションが異なる 2 種類のコレクション オプションがあります。どちらが適しているでしょうか。

Azure RemoteApp のコレクションを最大限に活用するために必要な、さまざまな考慮事項と選択肢を見てみましょう。


## コレクションの種類の簡単な相違点

| | クラウド | ハイブリッド |
|-----------|-------|--------|
|既存の VNET の使用| はい| はい|
|AD に接続しているアカウント (DirSync) が必要| なし| はい|
|ドメインへの参加が必要| なし| はい|
|ドメイン コント ローラーが VNET にアクセスできる必要| なし| はい|

## クラウドのコレクション
- 作成が迅速 - コレクションが迅速にプロビジョニングされれば、アプリがユーザーに早く届くことになります。
- 独自のアプリを取り込むか、マイクロソフトのアプリを共有します。カスタム イメージ (Azure VM から作成) を使用するか、またはサブスクリプションに含まれているイメージの 1 つを選択します。
- コレクションと、オンプレミスのドメインの間の接続を構成する必要はありません。
- ただし、独自の Azure VNET を使用して、データ共有のためにオンプレミスの環境へのアクセスを提供したり、SQL Server のようなリソースに (データベース認証を使用して) Windows 以外の認証を使用したりできます。


では、どのようにしてコレクションを作成しますか?

- クラウドのみ? ポータルの **[簡易作成]** オプションで作成します。
- クラウド + VNET? **[VNET で作成]** オプションで作成しますが、ドメインへの参加を選択しないでください。

## ハイブリッドのコレクション
- オンプレミスのネットワークと Azure VNET へのフル アクセスを提供します。
- アプリとデータのドメイン参加アクセスが含まれています。リモート アプリケーションは、オンプレミスの Active Directory に対する認証ができます。これで、ドメイン内のリソースにアクセスできます。
- System Center ソリューションおよび Windows グループ ポリシーにより、(Windows Server 2012 R2 上に構築されたカスタムのイメージを使用して) 高度な監視および管理を有効にします。
- Azure VNET をローカルの VNET に接続するための [ExpressRoute](https://azure.microsoft.com/services/expressroute/) をサポートしています。

**[VNET で作成]** オプションで作成し、ドメインへの参加を選択してください。

## 認証オプション
Azure RemoteApp は、Microsoft アカウントと Azure Active Directory アカウントの両方をサポートしていますが、すべてのコレクションがすべてのメソッドをサポートしているわけではありません。

| アカウントの種類 | | クラウド | クラウド + VNET | ハイブリッド |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Microsoft アカウント | | はい | はい | なし |
| Azure Active Directory (Azure AD) | | | | |
| | Azure AD のみ | はい | はい | なし |
| | パスワード同期がある AD Connect | はい | はい | はい |
| | パスワード同期がない AD Connect | はい | はい | なし |
| | AD FS がある AD Connect | はい | はい | はい |
| | サード パーティの Azure サポート ID プロバイダー (Ping など) | はい | はい | はい |
| Multi-Factor Authentication | | はい | はい | はい |



### クラウドとクラウド + VNET 間 
クラウドのコレクションでは、Microsoft アカウント、Azure AD アカウント、または 2 つの組み合わせを使用できます。ユーザーに対して最も効果的なアカウントを使用します。

Microsoft アカウントを使用するための特定の要件はありません。

Azure AD アカウントを使用する場合は、Azure AD テナントがサブスクリプションに関連付けられているテナントと一致するかどうかを確認する必要があります。Azure RemoteApp サブスクリプションを作成したときに、使用していた Azure AD テナントがサブスクリプションに自動的に関連付けられていました。権限を与えるすべての Azure AD ユーザーが、その同じテナントになる必要があります。必要に応じて、サブスクリプションに関連付けられた [Azure AD テナントを変更](remoteapp-changetenant.md)することができます。
 
### ハイブリッド (またはクラウド + Azure AD + AD)

ハイブリッドのコレクションには、Azure AD とオンプレミスの Active Directory を使用する必要があります。AD Connect を使用して、2 つのディレクトリを統合する必要があります。ただし、AD Connect を構成する方法に関してはいくつかの選択肢があります。

AD Connect には、パスワード同期を使用する場合と、AD フェデレーションを使用する場合の 2 つのシナリオがあります。どちらが最適かについては、[AD Connect の情報](../active-directory/active-directory-aadconnect.md)を確認してください。

クラウドのコレクションに Azure AD と AD を使用することもできます。同じ設定手順に従ってください。

Azure AD および Active Directory の構成に必要な手順については、「[Azure RemoteApp の Azure AD と Active Directory の要件](remoteapp-ad.md)」を参照してください。

## コレクションを作成しよう
これですべて説明したと思いますが、1 つだけやるべきことが残っていました。最初の Azure RemoteApp コレクションを作成することです。

「[クラウドのコレクションを作成する](remoteapp-create-cloud-deployment.md)」または「[ハイブリッドのコレクションを作成する](remoteapp-create-hybrid-deployment.md)」で作成してください。

<!---HONumber=AcomDC_0817_2016-->