<properties 
	pageTitle="Azure AD Connect の使用" 
	description="Azure AD Connect のセットアップ ウィザードをダウンロード、インストール、および実行する方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="azure-active-directory-connect" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Azure AD Connect の使用


<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/active-directory-aadconnect/" title="概要" class="current">概要</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-how-it-works/" title="機能">機能</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-get-started/" title="使用">使用</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-whats-next/" title="次のトピック">次のトピック</a> <a href="/ja-jp/documentation/articles/active-directory-aadconnect-learn-more/" title="詳細">詳細</a>
</div>

次のドキュメントは、Azure Active Directory Connect の使用を開始する際に役立ちます。

## Azure AD Connect のダウンロード



Azure AD Connect の使用を開始するには、次を使用して、最新のバージョンをダウンロードすることができます。[Azure AD Connect パブリック プレビューのダウンロード](http://connect.microsoft.com/site1164/program8612)

## Azure AD Connect をインストールする前に
Azure AD Connect をインストールする前に、いくつか必要な項目があります。

- Azure サブスクリプションまたは [Azure 試用サブスクリプション](http://azure.microsoft.com/pricing/free-trial/)
- Azure AD Premium または [Azure AD Premium 評価版](http://aka.ms/aadptrial)
- 統合する Azure AD テナントの Azure AD 全体管理者アカウント
- Windows Server 2008 以降を使用している AD ドメイン コントローラーまたはメンバー サーバー
- ローカル Active Directory のエンタープライズ管理者アカウント
- 省略可能: 同期を検証するテスト ユーザー アカウント。 

AD FS オプションで SSO を使用する場合は、以下も必要です。

- 目的のフェデレーション サーバー上のローカル管理者の資格情報。
- Web アプリケーション プロキシ ロールをデプロイするワークグループ (ドメインに参加していない) サーバー上のローカル管理者の資格情報。
- フェデレーション サーバー用の Windows Server 2012 R2 サーバー。
- Web アプリケーション プロキシ用の Windows Server 2012 R2 サーバー。
-  fs.contoso.com などの目的のフェデレーション サービス名に対する SSL 証明書を 1 つ含む .pfx ファイル。
- ウィザードを実行するコンピューターは、Windows リモート管理を使用して AD FS または Web アプリケーション プロキシをインストールする他のコンピューターに接続できる必要があります。


## Azure AD Connect のインストール

Azure AD Connect をダウンロードしたら、次を使用して Azure AD Connect をインストールします。

1. Azure AD Connect をインストールするサーバーにエンタープライズ管理者としてログインします。
2. AzureADConnect.msi に移動し、ダブルクリックします。
3. 高速またはカスタムのいずれかの設定を使用して、ウィザードの手順に従います。
4. 省略可能: テスト ユーザー アカウントを使用して、Office 365 などのクラウド サービスにサインインしてテストします。

<center>![Azure AD Connect へようこそ](./media/active-directory-aadconnect-get-started/aadConnect_Welcome.png)</center>

## 同期サービスのオプションの構成
同期サービスをインストールするとき、オプションの構成セクションをオフのままにすると、Azure AD Connect によってすべて自動的に設定されます。これには、SQL Server 2012 Express のインスタンスの設定、適切なグループの作成、アクセス許可の割り当てが含まれます。既定値を変更する場合、次の表を使用すると、使用できるオプションの構成を理解できます。

オプションの構成 | 説明 
------------- | ------------- |
SQL Server 名 |SQL Server 名とインスタンス名を指定することができます。使用する AD データベース サーバーが既にある場合は、このオプションを選択します。
サービス アカウント |既定では Azure AD Connect では、使用する同期サービスのサービス アカウントを作成します。ここで発生する問題は、パスワードが自動的に生成され、Azure AD Connect をインストールしているユーザーには不明であるということです。ほとんどのシナリオでは、このことは問題ありません。ただし、同期する組織単位のスコープなどの高度な構成を行う場合は、アカウントを作成し、ユーザー自身のパスワードを選択します。 |
アクセス許可 | 同期サービスがインストールされている場合、既定では、Azure AD Connect によって 4 つのグループが作成されます。これらのグループは。管理者グループ、オペレーター グループ、参照グループ、およびパスワード再設定グループです。独自のグループを指定する場合は、ここから行うことができます。
設定のインポート |Azure AD Sync の DirSync から構成情報をインポートする場合は、このオプションを使用します。|



## 高速インストール
[簡単設定] の選択は、既定のオプションであり、最も一般的なシナリオの 1 つです。このとき、Azure AD Connect により、パスワード ハッシュ同期オプションと共に同期をデプロイします。これは、単一のフォレスト用のみであり、ユーザーはオンプレミスのパスワードを使用して、クラウドにサインインできます。高速インストールを使用した場合、インストールが完了すると、自動的に同期が開始されます。このオプションでは、6 回クリックするだけで、オンプレミスのディレクトリをクラウドに拡張できます。

<center>![高速インストール](./media/active-directory-aadconnect-get-started/express.png)</center>

## カスタム インストール

カスタム インストールでは、いくつかの異なるオプションを選択できます。次の表では、カスタム インストール オプションを選択するときに使用できるウィザードのページについて説明します。

ページ名 | 説明
-------------------    | ------------- | 
ユーザーのサインイン|このページでは、パスワード同期、または AD FS とのフェデレーションを使用するか、またはそのどちらも使わないことを選択できます。
ディレクトリへの接続|このページでは、同期する 1 つまたは複数のディレクトリを追加できます。
フィルターの同期| ここでは、すべてのユーザーとグループを同期するかどうか、またはディレクトリあたり 1 つのグループを指定し、そのグループだけを同期するかどうかを決定できます。
オンプレミスの ID|ここでは、[ディレクトリへの接続] ページに追加したすべてのディレクトリに、ユーザーが 1 回だけ存在するか、または複数のディレクトリに存在するかを指定することができます。ユーザーが複数のディレクトリに存在する場合は、ディレクトリ内でこれらのユーザーを一意に識別する属性を選択する必要があります。たとえば、メールの属性や ObjectSID、SAMAccountName はすべて、ユーザーを一意に識別するために使用される一般的な属性です。
Azure の ID|このページでは、ID フェデレーションに使用するソース アンカーを指定します。
オプションの機能|次の表で、選択できるオプションの機能を簡単に説明します。

<center>![高速インストール](./media/active-directory-aadconnect-get-started/of.png)</center>


オプションの機能 | 説明
-------------------    | ------------- | 
Exchange ハイブリッド展開 |Exchange ハイブリッド展開機能によって、オンプレミスと Azure の両方で、Exchange メールボックスの共存が可能となります。そのためには、Azure AD から特定の属性のセットを、オンプレミスのディレクトリと同期します。
Azure AD アプリと属性フィルター|Azure AD アプリと属性フィルターを有効にして、ウィザードの後続のページで、同期する属性のセットを特定のセットに合わせることができます。このことを行うと、ウィザードで 2 つの追加の構成ページが開きます。  
パスワードの書き戻し|パスワードの書き戻しを有効にすると、Azure AD で発信されるパスワードの変更が、オンプレミスのディレクトリに書き戻されます。
ユーザーの書き戻し|ユーザーの書き戻しを有効にすると、Azure AD に作成されたユーザーが、オンプレミスのディレクトリに書き戻されます。このことを行うと、ウィザードで追加の構成ページが開きます。  
デバイスの同期|デバイスの同期を有効にすると、Azure AD にデバイスの構成を書き込むことができます。
ディレクトリ拡張属性の同期|ディレクトリ拡張属性の同期を有効にすると、指定した属性が Azure AD に同期されます。このことを行うと、ウィザードで追加の構成ページが開きます。  

既定の構成の変更、同期規則エディターと宣言型のプロビジョニングなど追加の構成オプションについては、「[Azure AD Connect の管理](active-directory-aadconnect-whats-next.md)」を参照してください。

## Azure AD Connect のサポート コンポーネント

次に示すのは、前提条件と Azure AD Connect を設定するサーバーに、Azure AD Connect がインストールするサポート コンポーネントの一覧です。この一覧は、基本的な高速インストール用です。[同期サービスのインストール］ ページで異なる SQL Server の使用を選択した場合は、次に示す SQL Server 2012 のコンポーネントはインストールされません。

- Forefront Identity Manager の Azure Active Directory コネクタ
- Microsoft SQL Server 2012 のコマンド ライン ユーティリティ
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Windows PowerShell 用 の Azure Active Directory モジュール
- IT プロフェッショナル向け Microsoft Online Services サインイン アシスタント
- Microsoft Visual C++ 2013 再配布パッケージ


**その他のリソース**

* [クラウド内のオンプレミスの ID インフラストラクチャの使用](active-directory-aadconnect.md)
* [Azure AD Connect の動作](active-directory-aadconnect-how-it-works.md)
* [Azure AD Connect の次のトピック](active-directory-aadconnect-whats-next.md)
* [詳細情報](active-directory-aadconnect-learn-more.md)
* [MSDN の Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->