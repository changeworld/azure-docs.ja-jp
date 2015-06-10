<properties 
   pageTitle="Microsoft Azure AD Connect - Windows Azure AD 同期ツール (DirSync) からのアップグレード" 
   description="ディレクトリ同期から Azure AD Connect にアップグレードする方法について説明します。この記事では、現在の Windows Azure AD 同期ツール (DirSync) から Azure AD Connect にアップグレードするための手順について説明します。" 
   services="active-directory" 
   documentationCenter="" 
   authors="shoatman" 
   manager="terrylanfear" 
   editor="billmath"/>

<tags 
   ms.service="active-directory" 
   ms.workload="identity" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="05/26/2015" 
   ms.author="shoatman"/>

# Azure Active Directory Connect に Windows Azure Active Directory の同期 (DirSync) をアップグレードします。

次のドキュメントは、既存の DirSync インストールの Azure AD Connect へのアップグレードに役立ちます。

## Azure AD Connect のダウンロード

Azure AD Connect の使用を開始するには、次を使用して、最新のバージョンをダウンロードすることができます。[Azure AD Connect パブリック プレビューのダウンロード](http://connect.microsoft.com/site1164/program8612)

## Azure AD Connect をインストールする前に
Azure AD Connect をインストール、および DirSync からアップグレードする前に、いくつか必要な項目があります。

- Azure AD のインスタンス既存のグローバル管理者アカウント (どのアカウントかはインストール時にリマインドされます) のパスワード
- ローカル Active Directory のエンタープライズ管理者アカウント
- 省略可能: DirSync を構成して SQL Server の完全バージョンを使用するには、データベースインスタンスの情報。

### 並列展開

現在50 K 個以上同期している場合、並列展開を実行するオプションを実行できます。並列展開 (SQL Server の別のサーバーが必要な場合) には、別のサーバーまたはサーバーのセットが必要です。並列展開の利点は、同期のダウンタイムを回避することです。Azure AD Connect  のインストールは、予想ダウンタイムを予測しますが、過去に DirSync をアップグレードしていた場合は、今までの経験がベストなガイドとなります。

## Azure AD Connect のインストール

Azure AD Connect を既存の DirSync サーバーにダウンロードします。

1. AzureADConnect.msi に移動し、ダブルクリックします。
2. ウィザードの手順を開始します。

インプレース アップグレードでは、次の高レベルの手順が行われます。

1. Azure AD Connect へようこそ
2. 現在の DirSync 構成の分析
3. Azure AD のグローバル管理者のパスワードの収集
4. 企業管理者アカウントの資格情報の収集 (Azure AD Connect のインストール時に使用)
5. AAD Connect のインストール
    * DirSync のアンインストール
	* AAD Connect のインストール
	* 必要に応じて、同期を開始します

追加の手順と情報が、以下の場合に必要です。

* 現在ローカルまたはリモートで完全な SQL Server を使用しています
* 同期のスコープ内で、50 以上のオブジェクトがある必要があります。

## インプレース アップグレードが 50 未満のオブジェクトの SQL Express (チュートリアル)

0. Azure AD Connect インストーラー (MSI) を起動します。

1. ライセンス条項とプライバシーに関する声明を確認し、同意します。

![Azure AD へようこそ](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)

2. 既存の DirSync インストールの分析の横をクリックします。

![既存のディレクトリ同期のインストールを分析します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)

3. 分析が完了すると続行方法の推奨を行います。このシナリオでは、(SQL Express を使用する 50K 以下のオブジェクト) 次の画面が表示されます。

![DirSync からアップグレードする準備の分析が完了しました](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)

4. 現在の Azure AD への接続に使用するアカウントのパスワードを入力します。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)

5. Active Directory の企業管理者アカウントを指定します。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)

6. 構成する準備が整いました。DirSync の横をクリックすると、アンインストールが行われ、Azure AD Connect が構成されて同期が始まります。  

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## インプレース アップグレード - 50K オブジェクト以上
手順 3 で、スコープ内に 50K 以上オブジェクトがある場合は別のメッセージが表示されます。次のような画面が表示されます。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

ここでは、別のサーバーでの並列アップグレードを検討することをお勧めします。このことを推奨する理由は、組織のサイズによっては、インプレース アップグレードがサービスレベル契約に影響を及ぼす可能性があるからです。Azure AD Connect を使用して最初の同期にかかる時間の予測を試みています。前述のように、DirSync  のインストールまたは DirSync へのアップグレードを行った経験は最善の指標となり得ます。

並列展開 (Azure AD Connect から別のサーバーを実行する必要がある場合) には、別のサーバーまたはサーバーのセットが必要です。そのような理由で、組織内への影響を回避するためにスケジュールを設定できる場合、インプレース アップグレードを検討するのは完全に妥当な理由です。

インプレース アップグレードを進めるには、次のメッセージの横にあるチェック ボックスをオンにします。"このコンピューター上の DirSync のアップグレードを続行する"。

## インプレース アップグレード - 完全 SQL Server

手順 3 で、DirSync インストールがローカルまたはリモート SQL Server の完全バージョンが使用されている場合、別のメッセージが表示されます。次のような画面が表示されます。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)

ディレクトリ同期で使用されている既存の SQL Server データベースのサーバーに関する情報が表示されます。必要に応じて適宜調整を行います。[次へ] をクリックすると、インストールを続行します。

## 並列展開 - 50 K 以上のオブジェクト

手順 3 で複数の 50 K 以上のオブジェクトがある場合、Azure AD Connect のインストールは、並列デプロイメントを推奨します。Azure AD Connect のインプレースまたは並行デプロイメントのどちらかを選択するかについての情報は、上記の「インプレース アップグレード - 50K オブジェクト以上」を参照してください。次のような画面が表示されます。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

並列デプロイメントを続行する場合は、次の手順を実行する必要があります。

- [設定のエクスポート] をクリックします。別のサーバーにAzure AD Connect をインストールすると、現在のディレクトリを同期からのすべての設定を新しくインストールされた AAD の接続に移行するこれらの設定がインポートされます。

設定が正常にエクスポートされると、DirSyncサーバーで、Azure AD Connect ウィザードを終了できます。

### 別のサーバーでの Azure AD Connect のインストール

Azure AD Connect を新しいサーバーにインストールする場合、DirSync は検出されず、、Azure AD Connect. のクリーン インストールを実行する必要があります。ここで、いくつかの特別な手順があります。

1. Azure AD Connect インストーラー (MSI) を実行します。
2. [Azure AD Connect] 画面が表示されます。右上にある [X] をクリックして、ウィザードを終了します。
3. コマンド プロンプトを開きます。
4. Azure AD Connect のインストール場所から (既定: C:\Program files \microsoft Azure Active Directory の接続)、次のコマンドを実行します。
    * AzureADConnect.exe /migrate

Azure AD Connects と、次の UI が表示されます。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)

5. DirSync のインストールからエクスポートされた設定ファイルを選択します。
6. 以下を含む高度なオプションを構成します。
    * Azure AD Connect のカスタム インストール場所
	* SQL Server の既存のインスタンス (既定: Azure AD Connect は、SQL Server 2012 Express をインストールします)。
	* SQL Server に使用するサービスアカウント(SQL Server database がリモートの場合、このアカウントはドメインサービスアカウントである必要があります)

次の UI でこれらのオプションを参照してください。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)

7. [次へ] をクリックします。 
8. 「構成に準備完了」のページで、[構成が完了すると、すぐに同期プロセスを開始] をオフにします
8. [インストール] をクリックします。

[AZURE.NOTE]インストールされていて、実行中の DirSync と AD Connect が AAD の書き込みを同時に試みていないことを確認するために、[同期の開始] チェックボックスをオフにします。

### Azure AD Connect が同期を開始する準備ができていることを確認します。

Azure AD Connect を DirSync から継承する準備できているかを判定するには、 Azure AD Connect Synchronization Service Manager を開きます。Windows の [スタート] メニューで「同期」と入力して検索すると、このアプリケーションが表示されます。

アプリケーション内で、「操作」タブを表示する必要があります。このタブで、次の操作が完了したことを確認します。

- AD Management Agent でのインポート
-  Azure AD Management Agent でのインポート
- AD Management Agent での完全同期
- Azure AD Management Agent での完全同期

これら 4 つの操作が完了すると、DirSync のアンインストールと Azure AD Connect 同期の有効化の準備が完了します。

### DirSync のアンインストール (古いサーバー)

- [プログラムの追加と削除] 内で、「Windows Azure Active Directory 同期ツール」を探します
- [Windows Azure Active Directory 同期ツール] のアンインストール

### Azure AD Connect を開きます (新しいサーバー)
インストール後に、Azure AD connect を再度開くと設定経験が提供されます。Azure AD Connect を開きます。

次のように表示されます。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* [ステージング モードの構成] を選択します。
    * エクスポートされた設定を使用して DirSync のアップグレードを行うと自動で Azure AD Connect がステージングモードに移行します。ステージング モードは、基本的には Azure AD connect 内で同期が発生することを意味しますが、変更は Azure AD または AD にエクスポートされません。
* 「有効なステージング モード」のチェック ボックスをオフにして、ステージングをオフにします。

![Azure ADの資格情報を入力します。](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* [インストール] ボタンをクリックします。

これは並列デプロイメントを使用した Azure AD Connect への移行が正常に完了しました。

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