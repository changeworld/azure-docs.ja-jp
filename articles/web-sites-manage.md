<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="How to manage" pageTitle="How to manage websites - Microsoft Azure service management" metaKeywords="Azure portal website management" description="A reference for the Portal website management pages in Microsoft Azure. Details are provided for each website management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Websites" authors="cephalin"  solutions="" writer="mwasson" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson"></tags>

# <a name="howtomanage"></a>Azure 管理ポータルでの Web サイトの管理

Azure ポータルでの Web サイトの管理は、一連のページまたは "タブ" で行います。Web サイト管理ページそれぞれについて以下で説明します。

## クイック スタート

**[クイック スタート]** 管理ページには、次のセクションが含まれています。

-   **[ツールの入手]** – [WebMatrix のインストール][WebMatrix のインストール]および [Microsoft Azure SDK のインストール][Microsoft Azure SDK のインストール]を行うためのリンクが表示されます。
-   **[アプリケーションの発行]** - Web サイトの発行プロファイルをダウンロードするためのリンク、Web サイトの展開資格情報をリセットするためのリンク、ステージングされていないサイトでステージングされた発行 (展開) スロットを追加するためのリンク、およびステージングされた発行について知るためのリンクが表示されます。
-   **[ソース管理の統合]** – TFS、CodePlex、GitHub、Dropbox、Bitbucket、Local Git などのソース管理ツールまたは Web サイトからのデプロイを設定して管理します。

## Dashboard

**[ダッシュボード]** 管理ページには、次のセクションが含まれています。

以下のメトリックを使用して、Web サイトの使用状況を集計したグラフ。

-   **[CPU 時間]** – Web サイトの CPU 使用率の測定値。
-   **[受信データ]** – Web サイトがクライアントから受信したデータの量。
-   **[送信データ]** – Web サイトからクライアントに送信されたデータの量。
-   **[HTTP サーバー エラー]** - 送信された HTTP "5xx サーバー エラー" メッセージの数。
-   **[要求]** – Web サイトに対するすべてのクライアント要求の数。

**注:** **[監視]** 管理ページの下部にある **[メトリックの追加]** を選択すると、パフォーマンス メトリックを追加できます。詳細については、「[Web サイトの監視方法][Web サイトの監視方法]」を参照してください。

**[Web エンドポイントの状態]** - 監視が構成された Web エンドポイントの一覧。どのエンドポイントも構成されていない場合、**[Web エンドポイント監視の構成]** をクリックし、**[構成]** 管理ページの **[監視]** セクションに移動します。詳細については、「[Web サイトの監視方法][Web サイトの監視方法]」を参照してください。

**[自動スケールの状態]** - 標準モードでは、リソースを自動的にスケールして、必要なだけの金額を支払うようにすることができます。自動スケールを有効にするには、**[自動スケールの構成]** を選択します。**[スケール]** ページが表示されます。Web サイトが Free モードまたは Shared モードである場合、自動スケールを構成する前に、Standard モードに変更する必要があります (これは **[スケール]** ページで実行できます)。**[自動スケール操作のログ]** から **[管理サービス]** ポータルを開き、Web サイトの自動スケールの履歴を確認することができます。既定のクエリの範囲は過去 24 時間ですが、既定のクエリは変更することができます。

**[使用状況の概要]** - Web サイトの CPU、ファイル システム、メモリ使用量の統計が表示されます。

**[リンク済みリソース]** - Web サイトに接続されている、SQL データベース、MySQL データベース、Microsoft Azure のストレージ アカウントなどのリソースの一覧が表示されます。リソースを管理するにはリソースの名前をクリックします。MySQL データベースがある場合、その名前をクリックすると、ClearDB 管理ページに移動します。そこでパフォーマンス メトリックを確認することも、ClearDB ダッシュボードに移動して必要に応じて MySQL データベースをアップグレードすることもできます。一覧にリソースが 1 つもない場合は、**[リンク済みリソースの管理]** をクリックして **[リンク済みリソース]** ページに移動し、Web サイトのリソースに対するリンクを追加します。

**[概要]** セクションに次の概要情報とリンクが表示されます (設定によっては、一部のオプションが表示されない場合もあります)。

-   **[適用可能なアドオンの表示]** - **[ストアから購入]** ダイアログ ボックスを開き、Web サイトに追加機能を提供するアドオンを選択して購入することができます。リージョンや環境によっては、一部のアドオンが利用できない場合があります。
-   **[接続文字列の表示]** - Web サイトのデータベース接続文字列を表示します。
-   **[発行プロファイルのダウンロード]** - Web サイトの発行プロファイルをダウンロードするときに、このリンクをクリックします。発行プロファイルには、FTP や Git を使用してコンテンツを Web サイトにアップロードするために必要な資格情報 (ユーザー名とパスワード) および URL が含まれます。発行プロファイル ファイルは XML 形式であり、テキスト エディターで表示できます。
-   **[展開資格情報を設定する]** - FTP や Git を使用してコンテンツを Web サイトにアップロードするために必要なユーザー名とパスワードを作成する場合にクリックします。これらの資格情報を使用して、サブスクリプション内の任意の Web サイトにコンテンツをプッシュできます (「[FTP 資格情報][FTP 資格情報]」を参照してください)。**メモ**: Microsoft アカウント (Live ID) の資格情報を使用した FTP ホストや Git リポジトリに対する認証はサポートされていません。
-   **[発行プロファイルの資格情報のリセット]** - Web サイトの発行プロファイルをリセットします。以前にダウンロードした発行プロファイルは無効になります。
-   **[ソース管理からのデプロイの設定]** - Team Foundation Service、CodePlex、GitHub、Dropbox、Bitbucket、ローカル Git からの継続的発行を設定できるダイアログ ボックスが表示されます。
-   **[新しい展開スロットの追加]** - 標準モードのサイトで、サイトをステージング スロットに作成する場合、この機能を使用します。ステージング スロット (ステージング サイト) を使用してサイトのコンテンツと構成を検証した後で、それを運用環境にスワップすることができます。サイトのステージングを利用してコンテンツ更新を段階的に追加し、ステージング スロットでの更新が完了したら、サイトを運用環境にスワップすることもできます (既にステージング環境に存在するサイトにスロットを追加することはできません)。
-   **[Visual Studio Online での編集]** - Microsoft Azure ポータルから Visual Studio Online を使用して Web サイトを直接編集するときに、このリンクをクリックします。このオプションは、**[構成]** ページで有効にしておかないと表示されません。
-   **[Dropbox から切断]** - デプロイ目的で Dropbox への接続を設定している場合、このリンクを使って接続を切断することができます。
-   **[Git リポジトリの削除]** - Git リポジトリを設定している場合、このリンクを使って削除することができます。
-   **[状態]** – Web サイトが実行中かどうかを示します。
-   **[管理サービス]** - **[操作ログ]** リンクをクリックすると、Microsoft Azure 管理サービス ポータルで Web サイトの操作ログを表示できます。
-   **[仮想 IP アドレス]** - **[構成]** タブの **[SSL バインド]** セクションで IP ベースの SSL バインドを構成していた場合に、Web サイトの仮想 IP アドレスを表示します。
-   **[サイトの URL]** – インターネット上の Web サイトについて、一般アクセス可能なアドレスを指定します。
-   **[コンピューティング モード]** – Web サイトが Free、Shared、Basic、Standard のどのモードで実行されているかを示します。Web スケール グループ モードの詳細については、「[Web サイトの規模変更方法][Web サイトの規模変更方法]」を参照してください。
-   **[FTP ホスト名]** – FTP 経由での Web サイトへの発行に使用する URL を指定します (「[FTP の資格情報][FTP 資格情報]」を参照)。
-   **[FTPS ホスト名]** – FTPS 経由での Web サイトへの発行に使用する URL を指定します (「[FTP の資格情報][FTP 資格情報]」を参照)。
-   **[展開ユーザー/FTP ユーザー]** - FTP または Git 経由で Web サイトを Microsoft Azure にデプロイする際に使用されるアカウントを指定します (「[FTP の資格情報][FTP 資格情報]」を参照)。
-   **[FTP 診断ログ]** – **[構成]** 管理ページで診断ログ記録が有効になっている場合に Web サイトの診断ログの FTP 場所を指定します。
-   **[FTPS 診断ログ]** – **[構成]** 管理ページで診断ログ記録が有効になっている場合に Web サイトの診断ログの FTPS 場所を指定します。
-   **[場所]** – Web サイトをホストするデータセンターのリージョンを指定します。
-   **[サブスクリプション名]** – Web サイトに関連付けられているサブスクリプション名を指定します。
-   **[サブスクリプション ID]** – Web サイトが関連付けられているサブスクリプションの一意なサブスクリプション ID (GUID) を指定します。

## 展開

このタブは、ソース管理からの展開を設定した場合にのみ表示されます。**[デプロイ]** 管理ページには、好みの発行方法を使用して Web サイトに行われたすべてのデプロイの概要が示されます。Web サイトに対する Git 発行が構成されていて、まだデプロイが行われていない場合、**[デプロイ]** 管理ページには、Git を使用して Web アプリケーションを Web サイトにデプロイする方法が表示されます。

## 監視

**[監視]** 管理ページには、Web サイトの使用状況を示すグラフが表示されます。このグラフには、「ダッシュボード」で説明した **[ダッシュボード]** ページのグラフと同じメトリックが既定で表示されます。グラフは、メトリック [HTTP 成功]、[HTTP リダイレクト]、[HTTP 401 エラー]、[HTTP 403 エラー]、[HTTP 404 エラー]、および [HTTP 406 エラー] が表示されるように構成することもできます。これらのメトリックの詳細については、「[Web サイトの監視方法][Web サイトの監視方法]」を参照してください。

## Web ジョブ

[Web ジョブ] 管理ページでは、Web サイトに対してオンデマンドで実行するタスク、スケジュールに従って実行するタスク、または連続的に実行するタスクを作成できます。詳細については、「[How to Use the WebJobs feature in Microsoft Azure Web Sites (Microsoft Azure の Web サイトで Web ジョブ機能を使用する方法)][How to Use the WebJobs feature in Microsoft Azure Web Sites (Microsoft Azure の Web サイトで Web ジョブ機能を使用する方法)]」を参照してください。

## 構成

**[構成]** 管理ページでは、アプリケーション固有の設定を行います。

詳細については、「[Web サイトの構成方法][Web サイトの構成方法]」を参照してください。

## スケール

**[スケール]** 管理ページで、Web スケール グループ モード (**[無料]**、**[共有]**、**[基本]**、または **[標準]**) を指定できます。**[共有]**、**[基本]**、および **[標準]** の各モードでは、スループットとパフォーマンスが向上します。**[Shared]**、**[Basic]**、**[Standard]** の各モードでは、Web サイトおよび同じ Web スケール グループ内の他の Web サイトで使用する仮想マシンの数である **[インスタンス数]** を増やすことができます。

**[標準]** モードでは、**[インスタンス サイズ]** を変更して、各インスタンスのコア数とメモリ容量を増やすこともできます。費用対効果を高めるために、**[自動スケール]** を選択して、Microsoft Azure が Web サイトのリソースを動的に配分するよう設定することもできます。

Web サイトのスケール オプションを構成する方法の詳細については、「[Web サイトの規模の設定方法][Web サイトの規模変更方法]」を参照してください。

## リンク済みリソース

**[リンク済みリソース]** 管理ページでは、SQL データベース、MySQL データベース、Azure ストレージ アカウントなど、Web サイトで使用している Microsoft Azure リソースが一覧表示されます。リソースを管理するにはリソースの名前をクリックします。

## バックアップ

**[バックアップ]** 管理ページでは、Web サイトの自動バックアップや手動バックアップの作成、以前の状態への Web サイトの復元、またはいずれかのバックアップに基づいた新しい Web サイトの作成が可能です。詳細については、[Microsoft Azure の Web サイトのバックアップに関するページ][Microsoft Azure の Web サイトのバックアップに関するページ]および「[Microsoft Azure の Web サイトの復元][Microsoft Azure の Web サイトの復元]」を参照してください。

## 管理ページのアイコン

アイコンは、Web サイトの管理ページそれぞれの下部に表示されます。これらのアイコンのうち一部は複数のページに表示され、いくつかのアイコンは特定の 1 ページだけに表示されます。以下のアイコンは、**[ダッシュボード]** 管理ページの下部に表示されます。

-   **[参照]** - Web サイトの既定ページを開きます。
-   **[停止]** - Web サイトを停止します。
-   **[再起動]** - Web サイトを再起動します。
-   **[ドメインの管理]** - ドメインをこの Web サイトにマップします。**[無料]** スケール モードのサイトでは使用できません。
-   **[削除]** - Web サイトを削除します。
-   **[WebMatrix]** - サポートされている Web サイトを WebMatrix で開きます。これにより、Web サイトへの変更を行い、その変更を Microsoft Azure 上にある元の Web サイトに発行できます。

以下のアイコンは、特定のタスクを実行するために、**[ダッシュボード]** 以外の管理ページの下部に表示されます。

-   **[メトリックの追加]** - **[監視]** 管理ページの下部に表示されます。これにより、[監視] 管理ページに表示されるグラフにメトリックを追加することができます。
-   **[リンク]** - **[リンク済みリソース]** 管理ページの下部に表示され、他の Microsoft Azure リソースに対する管理リンクを作成することができます。
-   たとえば、Web サイトで SQL データベースにアクセスする場合、**[リンク]** をクリックして、データベース リソースに対する管理リンクを作成できます。

## FTP の資格情報

使用できる FTP 資格情報のセットには、*デプロイ*資格情報と*発行プロファイル*資格情報の 2 つがあります。主な違いは次のとおりです。

**デプロイ資格情報**

-   Microsoft アカウントと関連付けられています。
-   任意の Web サイトの、アカウントと関連付けられているすべてのサブスクリプションへのデプロイに使用できます。
-   ユーザーがユーザー名/パスワードを選択します。
-   通常は git または FTP デプロイに使用されます。

**発行プロファイル資格情報**

-   単一の Web サイトと関連付けられています。
-   ユーザーはユーザー名またはパスワードを選択しません。
-   通常は Web デプロイに使用されますが、FTP に使用することもできます。

どちらの資格情報セットでも使用できます。FTP および FTPS のホスト名は、ダッシュボードの **[概要]** に一覧表示されます。

### デプロイ資格情報の使用

デプロイ資格情報を設定するには:

1.  管理ポータルで、目的の Web サイトの **[ダッシュボード]** ページを開きます。
2.  **[デプロイ資格情報を設定する]** をクリックします。
3.  ダイアログで、ユーザー名とパスワードを入力します。

注: 手順 2 で、デプロイ資格情報が既にある場合は、**[デプロイ資格情報のリセット]** と表示されます。新しいパスワードを設定したり、ユーザー名を変更するには、これをクリックします。

デプロイ資格情報は、Microsoft アカウントと関連付けられています。ユーザー名またはパスワードの変更は、アカウントと関連付けられているすべての Web サイトに適用されます。Azure サブスクリプションに複数の管理者がいる場合、管理者ごとに個別の資格情報があります。
FTP の完全なユーザー名は、“Web サイト\\ユーザー名” です。これは、ポータルの **[概要]** に **[デプロイ / FTP ユーザー]** として一覧表示されます。

### 発行プロファイル資格情報の使用

各 Web サイトには専用の発行プロファイル資格情報があります。発行プロファイル資格情報を表示するには:

1.  管理ポータルで、目的の Web サイトの **[ダッシュボード]** ページを開きます。
2.  **[発行プロファイルのダウンロード]** をクリックします。

発行プロファイル ファイルは XML ファイルです。Web デプロイ用と FTP 用に 2 つのプロファイルが含まれます。

<pre>
&lt;publishData&gt;
  &lt;publishProfile
    profileName="contoso - Web Deploy"
    publishMethod="MSDeploy"
    publishUrl="contoso.scm.azurewebsites.net:443"
    msdeploySite="contoso"
    userName="$contoso"
    userPWD="abc1234..."
    destinationAppUrl="http://contoso.azurewebsites.net"
    SQLServerDBConnectionString=""
    mySQLDBConnectionString=""
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
  &lt;publishProfile 
    profileName="contoso - FTP" 
    <mark>publishMethod="FTP"</mark> 
    publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.windows.net/site/wwwroot" 
    ftpPassiveMode="True" 
    <mark>userName="contoso\$contoso"</mark> 
    <mark>userPWD=" abc1234..."</mark>  
    destinationAppUrl="http://contoso.azurewebsites.net" 
    SQLServerDBConnectionString="" 
    mySQLDBConnectionString="" 
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
&lt;/publishData&gt;
</pre>

`publishMethod="FTP"` のプロファイルを探します。ユーザー名は `userName` の下に一覧表示され、パスワードは `userPWD` の下に一覧表示されます。

パスワードをリセットするには、**[発行プロファイルの資格情報のリセット]** をクリックします。新しい資格情報を取得するには、発行プロファイルを再びダウンロードします。発行プロファイルの資格情報は Web サイトと関連付けられています。各 Web サイトには専用の発行プロファイルがあります。

<!-- LINKS --> 
<!-- Anchors. -->

  [WebMatrix のインストール]: http://go.microsoft.com/fwlink/?LinkID=226244
  [Microsoft Azure SDK のインストール]: http://go.microsoft.com/fwlink/?LinkId=246928
  [Web サイトの監視方法]: http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-monitor-websites/
  [FTP 資格情報]: #ftp-credentials
  [Web サイトの規模変更方法]: http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-scale-websites
  [How to Use the WebJobs feature in Microsoft Azure Web Sites (Microsoft Azure の Web サイトで Web ジョブ機能を使用する方法)]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-create-web-jobs/
  [Web サイトの構成方法]: http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-configure-websites
  [Microsoft Azure の Web サイトのバックアップに関するページ]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-backup/
  [Microsoft Azure の Web サイトの復元]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-restore/
