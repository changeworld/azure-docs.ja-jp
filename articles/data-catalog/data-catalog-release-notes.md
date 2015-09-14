<properties
   pageTitle="Azure Data Catalog リリース ノート"
	description="Azure Data Catalog の 2015 年 8 月 28 日パブリック プレビュー リリースのリリース ノート"
	services="data-catalog"
	documentationCenter=""
	authors="steelanddata"
	manager="NA"
	editor=""
	tags=""/>
<tags
   ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="08/28/2015"
	ms.author="maroche"/>

# Azure Data Catalog リリース ノート

## Azure Data Catalog の 2015 年 8 月 28 日リリースのリリース ノート

### 登録されたデータセットの一部でデータ プロファイルが不足する

データ ソース登録ツールでデータ プロファイル オプションを使用してデータ ソースを登録すると、次に該当する場合は、データ プロファイル情報が登録されないことがあります。

* Azure SQL Database テーブル
* 同じ名前の複数のオブジェクトが複数のスキーマ内に存在する SQL Server のテーブルとビュー
* 列名が 118 文字を上回る SQL Server のテーブルとビュー
* 列名が 20 文字を上回る Oracle のテーブルとビュー
* 列名にスペースまたはマルチバイト文字が含まれる Oracle のテーブルとビュー

これらの制限は 8 月 28 日付けのリリースでの既知の問題であり、Azure Data Catalog の今後の更新で対応される予定です。

## Azure Data Catalog の 2015 年 7 月 13 日リリースのノート

### Oracle Database への登録と接続

Oracle Database データ ソースに接続する場合、ユーザーは、使用しているソフトウェアのビット (32 ビットか 64 ビット) に一致する正しい Oracle ドライバーをインストールしている必要があります。

-	32 ビット Windows を実行するコンピューター上の Oracle データ ソースを登録する場合は、32 ビット Oracle ドライバーを使用します。
-	64 ビット Windows を実行するコンピューター上の Oracle データ ソースを登録する場合は、64 ビット Oracle ドライバーを使用します。
-	64 ビット Windows に含まれる 32 ビット バージョンの Microsoft Office を実行するコンピューター上の Excel を使用して、Oracle データ ソースに接続する場合、32 ビット Oracle ドライバーを使用します。
-	64 ビット バージョンの Microsoft Office を実行するコンピューター上の Excel を使用して、Oracle データ ソースに接続する場合、64 ビット Oracle ドライバーを使用します。

### SQL Server Reporting Services への登録と接続

Azure Data Catalog の初期プレビュー リリースでの SQL Server Reporting Services (SSRS) データ ソースのサポートは、ネイティブ モード サーバーのみに制限されます。SSRS の SharePoint モードでのサポートは、今後のリリースで追加される予定です。

### Excel でのデータ資産のオープン

Azure Data Catalog ポータルから Microsoft Excel でデータ資産を開くと、[**Microsoft Excel のセキュリティに関する通知**] ダイアログ ボックスにメッセージが表示されることがあります。これは標準の予想される動作であり、ユーザーは、[**有効にする**] を選択して続行できます。

詳細については、「[不審な Web サイトへのリンクやそうしたサイトからのファイルについてのセキュリティの警告を有効または無効にする](https://support.office.com/ja-JP/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE)」を参照してください。

### BLOB および UDT 列がプレビューに含まれない

バイナリ ラージ オブジェクト (BLOB) 列とユーザー定義データ型 (UDT) 列を含むテーブルとビューを登録し、データ資産のプレビューを含めるように選択しても、これらの列はプレビューに含まれません。

### プロキシおよびポリシーの構成とデータ ソースの登録

ユーザーは、Azure Data Catalog ポータルにログオンできる状況が発生することがありますが、データ ソース登録ツールにログオンしようとすると、ログオンを妨げるエラー メッセージが表示されます。

この問題の動作には 2 つの可能性のある原因があります。

**原因 1: Active Directory フェデレーション サービス構成** データ ソース登録ツールは、フォーム認証を使用して、Active Directory に対してユーザー ログオンを検証します。ログオンを成功させるには、Active Directory 管理者によって、グローバル認証ポリシーでフォーム認証が有効にされている必要があります。

特定の状況で、ユーザーが企業ネットワーク上にいる場合のみ、またはユーザーが企業ネットワークの外部から接続している場合のみ、このエラー動作が発生することがあります。グローバル認証ポリシーでは、イントラネット接続とエクストラネット接続で個別に認証方法を有効にすることができます。ログオン エラーは、ユーザーの接続元のネットワークで、フォーム認証が有効にされていない場合に発生する可能性があります。

詳細については、「[WIA をサポートしないデバイスのイントラネット フォーム ベースの認証の構成](https://technet.microsoft.com/library/dn727110.aspx)」を参照してください。

**原因 2: ネットワーク プロキシ構成** 企業ネットワークでプロキシ サーバーを使用している場合、登録ツールが、プロキシ経由で Azure Active Directory に接続できないことがあります。ユーザーは、ツールの構成ファイルを編集し、このセクションをファイルに追加して、登録ツールが確実に接続できるようにします。


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


RegistrationTool.exe.config ファイルを見つけるには、登録ツールを起動し、Windows タスク マネージャー ユーティリティを開きます。タスク マネージャーの [詳細] タブで、[RegistrationTool.exe] を右クリックし、ポップアップ メニューから [ファイルの場所を開く] を選択します。

<!---HONumber=September15_HO1-->