<properties 
	pageTitle="Cloud App Discovery のセキュリティとプライバシーの考慮事項" 
	description="このトピックでは、Cloud App Discovery に関連するセキュリティとプライバシーの考慮事項について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Cloud App Discovery のセキュリティとプライバシーの考慮事項

Microsoft は、組織のセキュリティの管理に役立つソフトウェアやサービスを提供すると同時に、お客様のプライバシーとデータの保護に努めています。<br>他者にデータを委託する場合、信頼には厳格なセキュリティ エンジニアリングへの投資とそれを裏付ける専門知識が必要であることを把握しております。Microsoft ではセキュリティで保護されたソフトウェア開発ライフサイクルの実践からサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。<br>Microsoft においてデータの保護は最優先事項になります。

このトピックでは、Azure Active Directory Cloud App Discovery でデータを収集、処理、およびセキュリティ保護する方法について説明します。




##概要

Cloud App Discovery は Azure AD の機能であり、Microsoft Azure でホストされます。<br>Cloud App Discovery Endpoint Agent は、IT で管理されているコンピューターからアプリケーション検出データを収集するために使用します。<br>収集したデータは、暗号化されたチャネル経由でセキュリティで保護されて Azure AD Cloud App Discovery サービスに送信されます。<br>その後、組織の Cloud App Discovery データは、Azure ポータルに表示されます。


<center>![How Cloud App Discovery Works](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


次のセクションでは、情報のフローに従い、情報が組織から Cloud App Discovery サービスに、最終的には Cloud App Discovery ポータルに移動する際にどのようにセキュリティ保護されるのかについて説明します。



## 組織からのデータの収集

Azure Active Directory の Cloud App Discovery 機能を使用して組織内の従業員が使用するアプリケーションに関する洞察を得るには、まず Azure AD Cloud App Discovery Endpoint Agent を組織のコンピューターにデプロイする必要があります。

Azure Active Directory テナントの管理者 (またはその代理人) は、Azure ポータルから、このエージェントのインストール パッケージをダウンロードできます。エージェントは、手動でインストールすることも、SCCM またはグループ ポリシーを使用して組織内の複数のマシンにインストールすることもできます。

デプロイ オプションの詳細については、[Cloud App Discovery のグループ ポリシーのデプロイ ガイドに関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)をご覧ください。


### エージェントによって収集されたデータ

Web アプリケーションに接続すると、以下の一覧に概要が説明されている情報が、エージェントによって収集されます。情報は、管理者がアプリケーション検出用に構成したアプリケーションについてのみ収集されます。<br>メタデータを収集するアプリケーションの一覧は、ポータルの [設定] タブで構成できます。



> [AZURE.NOTE]詳細については、[Cloud App Discovery の概要に関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)をご覧ください。
 
**情報カテゴリ**: ユーザー情報<br>**説明**:<br>ターゲットの Web アプリケーションに対する要求を作成したプロセスの Windows ユーザー名 (例: DOMAIN\\username) とそのユーザーの Windows セキュリティ識別子 (SID)


**情報カテゴリ**: プロセス情報<br>**説明**:<br>ターゲットの Web アプリケーションに対する要求を作成したプロセスの名前 (例: "iexplore.exe")

**情報カテゴリ**: コンピューター情報<br>**説明**:<br>エージェントがインストールされているコンピューターの NetBIOS の名前

**情報カテゴリ**: アプリのトラフィック情報<br>**説明**:<br>

接続情報:

- ソース (ローカル コンピューター) と宛先の IP アドレスとポート番号 

- 要求送信に使用する組織のパブリック IP アドレス

- 要求時刻

- 送受信されたトラフィックの量

- IP バージョン (4 または 6)

- TLS 接続のみ: Server Name Indication 拡張機能またはサーバー証明書のいずれかのターゲット ホスト名

HTTP 情報:

- メソッド (GET、POST など) 

- プロトコル (HTTP/1.1 など)

- ユーザー エージェント文字列

- ホスト名

- ターゲット URI (クエリ文字列を除く)

- コンテンツ タイプ情報

- 参照元 URL 情報 (クエリ文字列を除く)



> [AZURE.NOTE]上記の HTTP 情報は、暗号化されていないすべての接続について収集されます。TLS 接続では、ポータルで「詳細な検査」設定をオンにしているときにのみ、この情報がキャプチャされます。設定は、既定では「オン」です。詳細については、[Cloud App Discovery の概要に関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)をご覧ください。
 

 
### エージェントの動作

エージェントのインストールには、次の 2 つのコンポーネントが含まれています。

- ユーザーモード コンポーネント

- カーネルモード ドライバー コンポーネント (Windows フィルタリング プラットフォーム ドライバー)



エージェントは、最初にインストールされたときに、コンピューター上にコンピューター固有の信頼された証明書を格納し、その後、これを使用して Cloud App Discovery サービスとセキュリティで保護された接続を確立します。<br>エージェントは、このセキュリティで保護された接続経由で、定期的に Cloud App Discovery サービスからポリシーの構成を取得します。<br>ポリシーには、監視するクラウド アプリケーション、自動更新を有効にするかどうかなどについての情報が含まれています。

コンピューター上で Web トラフィックが送受信されると、Cloud App Discovery がトラフィックを分析し、関連するメタデータ (上記の表を参照) を抽出します。<br>エージェントは、1 分ごとに暗号化されたチャネル経由で、収集したメタデータを Cloud App Discovery サービスにアップロードします。

暗号化された接続では、収集したデータの精度を向上する目的で、追加の手順が実行されます。<br> これは、詳細な検査と呼ばれます。<br>ドライバー コンポーネントでは、暗号化されたトラフィックをインターセプトし、暗号化されたストリームに挿入します。これは、コンピューター上で自己署名ルート証明書を作成することで実行され、クライアント アプリケーションに Cloud App Discovery エージェントを信頼させます。この自己署名ルート証明書はエクスポートできないとマークされ、管理者の ACL となります。作成元のコンピューター以外の場所では使用されません。


### ユーザーのプライバシーの尊重

私たちの目的は、管理者にアプリケーション使用状況の詳細な把握とユーザーのプライバシーとの間のバランスを組織に合わせて設定できるツールを提供することです。そのために、ポータルの設定ページで次の項目を設定できるようにしました。

- **データ収集**: 管理者は検出データを取得するアプリケーションまたはアプリケーション カテゴリを指定できます。


- **詳細な検査**: 管理者はエージェントが SSL や TLS 接続の HTTP トラフィックを収集するかどうかを指定できます。



Cloud App Discovery Endpoint Agent は、上の表で説明した情報のみを収集します。



> [AZURE.NOTE]詳細については、[Cloud App Discovery の概要に関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)をご覧ください。
 



## Cloud App Discovery へのデータの送信

メタデータは、エージェントによって収集された後、最大 1 分間、またはキャッシュ データのサイズが 5 MB に達するまで、コンピューターでキャッシュされます。その後、圧縮され、セキュリティで保護された接続経由で Cloud App Discovery サービスに送信されます。

エージェントが何らかの理由で Cloud App Discovery サービスと通信できない場合は、収集されたメタデータはローカル ファイル キャッシュに格納されます。このキャッシュには、コンピューター上の特権を持つユーザー (Administrators グループなど) のみがアクセスできます。<br>エージェントは、キャッシュしたメタデータが Cloud App Discovery サービスによって正常に受信されるまで、自動的に再送信を試みます。



## サービス エンドでのデータの受信

エージェントは、Cloud App Discovery サービスに対して、上述のコンピューター固有のクライアント認証証明書を使用して、暗号化されたチャネル経由で転送されたデータを認証します。<br>Cloud App Discovery サービスの分析パイプラインは、分析パイプラインのすべての段階でメタデータを論理的に分割することで、メタデータを顧客ごとに個別に処理します。分析されたメタデータは、ポータルのさまざまなレポートで活用されます。

未処理のメタデータと分析されたメタデータは、最大 180 日間保存されます。さらに、お客様は、自分で選択した Azure BLOB ストレージ アカウントに、分析されたメタデータをキャプチャすることができます。これは、メタデータのオフライン分析や、データの保存期間の延長として役立ちます。

## Azure ポータルを使用するデータ アクセス

収集されたメタデータのセキュリティ保護を確保するために、既定では、テナントのグローバル管理者のみが Azure ポータルで Cloud App Discovery 機能にアクセスできます。<br>ただし管理者は、他のユーザーやグループにこのアクセスを委任できます。



> [AZURE.NOTE]詳細については、[Cloud App Discovery の概要に関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)をご覧ください。
 


ポータルで、データにアクセスするすべてのユーザーは、Azure AD Premium ライセンスをライセンスする必要があります。



**その他のリソース**


* [自分の組織内で使用される承認されていないクラウド アプリを検出する方法](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=July15_HO5-->