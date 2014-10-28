<properties title="Securing an Azure Website" pageTitle="Securing an Azure Website." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

## Azure Web サイト内の Web アプリケーションのセキュリティ保護

Web アプリケーションの開発における課題の 1 つが、セキュリティで保護された安全なサービスを顧客に提供する方法です。この記事では、Web アプリケーションをセキュリティで保護できる Azure Websites の機能について説明します。

> [WACOM.NOTE] Web ベースのアプリケーションにおけるセキュリティ上の考慮事項に関する詳細な説明は、このドキュメントの対象範囲外です。Web アプリケーションをセキュリティで保護するためのさらなるガイダンスを得るための出発点として、「[Open Web Application Security Project (OWASP)][Open Web Application Security Project (OWASP)]」、特に [top 10 プロジェクト][top 10 プロジェクト]のページを参照してください。OWASP メンバーによって判別された、Web アプリケーションにおける重要なセキュリティ リスクの現在のトップ 10 が記載されています。

### 目次

-   [保護された通信][保護された通信]
-   [保護された開発][保護された開発]
-   [次のステップ][次のステップ]

## <a name="https"></a>保護された通信

Web サイト用に作成された **.azurewebsites.net** ドメイン名を使用している場合は、SSL 証明書がすべての **.azurewebsites.net** ドメイン名に提供されているため、すぐに HTTPS を使用できます。サイトで[カスタム ドメイン名][カスタム ドメイン名]を使用している場合は、SSL 証明書をアップロードし、カスタム ドメインに対して HTTPS を有効にすることができます。

詳細については、「[Azure の Web サイトでの HTTPS の有効化][Azure の Web サイトでの HTTPS の有効化]」を参照してください。

### HTTPS を強制的に使用する

Azure Websites では、HTTPS は強制的に使用*されません*。訪問者は、引き続き HTTP を使用してサイトにアクセスできますが、機密情報が危険にさらされる可能性があります。HTTPS を強制的に使用するようにするには、**URL 書き換え**モジュールを使用します。URL 書き換えモジュールは Azure Websites に組み込まれており、それを使用して、受信した要求をアプリケーションに渡す前に要求に適用されるルールを定義できます。このモジュールは、Azure Websites がサポートするプログラミング言語で記述されたアプリケーションで使用できます。

> [WACOM.NOTE] .NET MVC アプリケーションでは、URL 書き換えの代わりに [RequireHttps][RequireHttps] フィルターを使用する必要があります。RequireHttps の使用方法の詳細については、[安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトにデプロイする][安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトにデプロイする]方法に関するページを参照してください。
>
> 他のプログラミング言語とフレームワークを使用して要求をプログラム的にリダイレクトする方法の詳細については、該当するテクノロジのドキュメントを参照してください。

URL 書き換えルールは、アプリケーションのルートに格納されている **web.config** ファイルに定義されます。次の例には、すべての受信トラフィックに HTTPS の使用を強制する基本的な URL 書き換えルールが含まれています。

<a name="example"></a>**Web.Config ファイルの URL 書き換えの例**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

このルールは、ユーザーが HTTP を使用してページを要求したときに HTTP ステータス コード 301 (永続的なリダイレクト) を返すことで動作します。301 は、訪問者が要求した URL と同じ URL へ要求をリダイレクトしますが、要求の HTTP 部分は HTTPS で置き換えられます。たとえば、<HTTP://contoso.com> は <HTTPS://contoso.com> にリダイレクトされます。

> [WACOM.NOTE] アプリケーションが **Node.js**、**PHP**、**Python Django**、または **Java** で記述されている場合は、web.config ファイルが含まれていない可能性があります。ただし、**Node.js**、**Python Django**、**Java** はすべて、実際には、Azure Web サイト上でホストされた場合に web.config を使用します。Azure はデプロイメント時に自動的にファイルを作成するため、それを目にすることはありません。アプリケーションの一部としてこのファイルを含めると、Azure が自動的に生成したファイルは上書きされます。

### .NET

.NET アプリケーションの場合は、アプリケーションの web.config ファイルを変更し、[例][例]の **\<rewrite\>** セクションを **\<system.WebServer\>** セクションに追加します。

web.config ファイルに **\<rewrite\>** セクションが既に含まれる場合は、[例][例]の **\<rule\>** を **\<rules\>** セクションの最初のエントリとして追加します。

### PHP

PHP アプリケーションの場合は、[例][例]を web.config ファイルとしてアプリケーションのルートに保存するだけです。その後、アプリケーションを Azure Web サイトに再度デプロイします。

### Node.js、Python Django、および Java

Node.js、Python Django、および Java アプリケーションの場合、web.config ファイルがまだ提供されていない場合は自動的に作成されますが、デプロイメント時に作成されるため、サーバー上にのみ存在します。自動生成されたファイルには、Azure にアプリケーションのホスト方法を伝える設定が含まれます。

自動生成されたファイルを Web サイトから取得して変更するには、次の手順を使用します。

1.  FTP を使用してファイルをダウンロードします (「[Uploading/downloading files over FTP and collecting diagnostics logs (FTP を介したファイルのアップロード/ダウンロードおよび診断ログの収集)][Uploading/downloading files over FTP and collecting diagnostics logs (FTP を介したファイルのアップロード/ダウンロードおよび診断ログの収集)]」を参照)。

2.  それをアプリケーションのルートに追加します。

3.  次の情報を使用して書き換えルールを追加します。

    -   **Node.js および Python Django**

        Node.js および Python Django アプリケーション用に生成された web.config ファイルには、既に **\<rewrite\>** セクションがあり、サイトが適切に機能するために必要な **\<rule\>** エントリが含まれています。サイトに HTTPS の使用を強制するには、例の **\<rule\>** を **\<rules\>** セクションの最初のエントリとして追加します。これにより、その他のルールは未変更のままで、HTTPS が強制的に使用されます。

    -   **Java**

        Apache Tomcat を使用している Java アプリケーションの web.config ファイルには **\<rewrite\>** セクションが含まれていないため、例の **\<rewrite\>** セクションを **\<system.webServer\>** セクションに追加する必要があります。

4.  プロジェクト (更新された web.config を含む) を Azure に再度デプロイします。

HTTPS を強制的に使用する書き換えルールを伴う web.config をデプロイすると、すぐに有効となり、すべての要求が HTTPS にリダイレクトされます。

IIS URL 書き換えモジュールの詳細については、[URL 書き換え][URL 書き換え]のドキュメントを参照してください。

## <a name="develop"></a>保護された開発

### プロファイルの発行と発行の設定

**Visual Studio**、**Web Matrix**、**Azure PowerShell**、Azure **クロスプラットフォーム コマンドライン インターフェイス**などのユーティリティを使用して、アプリケーションの開発、管理タスクの実行、またはタスクの自動化を行っている場合は、*発行設定*ファイルまたは*発行プロファイル*を使用できます。どちらも Azure に対する認証に使用されるため、許可されていないアクセスを防ぐためにセキュリティで保護される必要があります。

-   **発行設定**ファイルには以下のものが含まれます。

    -   Azure サブスクリプション ID

    -   *アカウント名やパスワードを提供する必要なく*サブスクリプションの管理タスクを実行できるようにする管理証明書。

-   **発行プロファイル**ファイルには以下のものが含まれます。

    -   Azure Web サイトへ発行するための情報

発行設定または発行プロファイルを使用するユーティリティを使用している場合は、発行設定または発行プロファイルを含むファイルをユーティリティにインポートし、その後、そのファイルを**削除**します。ファイルを保持する必要がある場合、たとえば、プロジェクトに携わる他のユーザーと共有するには、アクセス許可が限定された**暗号化**されたディレクトリなど、セキュリティで保護された場所にファイルを保存します。

さらに、インポートされた資格情報が保護されていることを確認する必要があります。たとえば、**Azure PowerShell** と **Azure クロスプラットフォーム コマンドライン インターフェイス**はどちらも、インポートされた情報を**ホーム ディレクトリ** (Linux または OS X システムでは *~*、Windows システムでは */users/yourusername*) に保存します。希望する場合は、さらにセキュリティを強化するために、オペレーティング システムに対応した暗号化ツールを使用してこれらの場所を**暗号化**できます。

### 構成設定と接続文字列

接続文字列、認証資格情報、およびその他の機密情報は構成ファイルに保存するのが一般的な方法です。残念ながら、これらのファイルは Web サイト上で公開される場合やパブリック リポジトリにチェックインされる場合があり、結果としてこの情報が危険にさらされる可能性があります。

Azure Websites では、構成情報を Web サイト ランタイム環境の一部である**アプリケーション設定**および**接続文字列**として保存できます。大半のプログラミング言語では、値は、*環境変数*を介して実行時にアプリケーションに公開されます。.NET アプリケーションの場合、これらの値は実行時に .NET 構成に挿入されます。

**アプリケーション設定**と**接続文字列**は、Azure の管理ポータル、または PowerShell や Azure クロスプラットフォーム コマンドライン インターフェイスなどのユーティリティを使用して構成可能です。

アプリケーション設定と接続文字列の詳細については、「[Web サイトの構成方法][Web サイトの構成方法]」を参照してください。

### FTPS

Azure は、**FTPS** を介して Web サイトのファイル システムへの保護された FTP アクセスを提供します。これによって、Web サイト上のアプリケーション コードや診断ログに安全にアクセスできます。Web サイトの FTPS リンクは、[Azure の管理ポータル][Azure の管理ポータル]内の**ダッシュボード**のサイトに掲載されています。

FTPS の詳細については、「[File Transfer Protocol][File Transfer Protocol]」を参照してください。

## 次のステップ

Azure プラットフォームのセキュリティ、**セキュリティ インシデントまたは迷惑行為**の報告方法、またはサイトの**侵入テスト**の実施予定を Microsoft に通知する方法の詳細については、[Microsoft Azure のトラスト センター][Microsoft Azure のトラスト センター]のセキュリティのセクションを参照してください。

Azure Web サイトの **web.config** または **applicationhost.config** ファイルの詳細については、「[Configuration options unlocked in Azure Web Sites (Azure Web サイトでロック解除される構成オプション)][Configuration options unlocked in Azure Web Sites (Azure Web サイトでロック解除される構成オプション)]」を参照してください。

攻撃の検出に役立つことのある Azure Websites のログ情報の詳細については、「[Azure の Web サイトの診断ログを有効にする][Azure の Web サイトの診断ログを有効にする]」を参照してください。

  [Open Web Application Security Project (OWASP)]: https://www.owasp.org/index.php/Main_Page
  [top 10 プロジェクト]: https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
  [保護された通信]: #https
  [保護された開発]: #develop
  [次のステップ]: #next
  [カスタム ドメイン名]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-custom-domain-name/
  [Azure の Web サイトでの HTTPS の有効化]: /ja-jp/documentation/articles/web-sites-configure-ssl-certificate/
  [RequireHttps]: http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx
  [安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトにデプロイする]: /ja-jp/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [例]: #example
  [Uploading/downloading files over FTP and collecting diagnostics logs (FTP を介したファイルのアップロード/ダウンロードおよび診断ログの収集)]: http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx
  [URL 書き換え]: http://www.iis.net/downloads/microsoft/url-rewrite
  [Web サイトの構成方法]: /ja-jp/documentation/articles/web-sites-configure/
  [Azure の管理ポータル]: https://manage.windowsazure.com
  [File Transfer Protocol]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [Microsoft Azure のトラスト センター]: /ja-jp/support/trust-center/security/
  [Configuration options unlocked in Azure Web Sites (Azure Web サイトでロック解除される構成オプション)]: http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/
  [Azure の Web サイトの診断ログを有効にする]: /ja-jp/documentation/articles/web-sites-enable-diagnostic-log/
