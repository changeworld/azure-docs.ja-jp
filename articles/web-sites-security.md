<properties title="Securing an Azure Website" pageTitle="Azure の Web サイトのセキュリティ保護" description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


#Azure Web サイト内の Web アプリケーションのセキュリティ保護

Web アプリケーションの開発における課題の 1 つが、セキュリティで保護された安全なサービスを顧客に提供する方法です。この記事では、Web アプリケーションをセキュリティで保護できる Azure Websites の機能について説明します。

> [WACOM.NOTE] Web ベースのアプリケーションにおけるセキュリティ上の考慮事項に関する詳細な説明は、このドキュメントの対象範囲外です。Web アプリケーションをセキュリティで保護するためのさらなるガイダンスを得るための出発点として、「[Open Web Application Security Project (OWASP) ](https://www.owasp.org/index.php/Main_Page)」、特に [top 10 プロジェクト](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)のページを参照してください。OWASP メンバーによって判別された、Web アプリケーションにおける重要なセキュリティ リスクの現在のトップ 10 が記載されています。

###目次

* [保護された通信](#https)
* [保護された開発](#develop)
* [次のステップ](#next)
 
##<a name="https"></a>保護された通信

Web サイト用に作成された ***.azurewebsites.net** ドメイン名を使用している場合は、SSL 証明書がすべての **.azurewebsites.net** ドメイン名に提供されているため、すぐに HTTPS を使用できます。サイトで[カスタム ドメイン名](http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-custom-domain-name/)を使用している場合は、SSL 証明書をアップロードし、カスタム ドメインに対して HTTPS を有効にすることができます。

##<a name="develop"></a>保護された開発 

###プロファイルの発行と発行の設定

Visual Studio**、**Web Matrix**、**Azure PowerShell**、Azure **クロスプラットフォーム コマンドライン インターフェイス**などのユーティリティを使用して、アプリケーションの開発、管理タスクの実行、またはタスクの自動化を行っている場合は、***発行設定*ファイルまたは*発行プロファイル*を使用できます。どちらも Azure に対する認証に使用されるため、許可されていないアクセスを防ぐためにセキュリティで保護される必要があります。

* **発行設定**ファイルには以下のものが含まれます。

	* Azure サブスクリプション ID

	* *アカウント名やパスワードを提供する必要なく*サブスクリプションの管理タスクを実行できるようにする管理証明書。

* **発行プロファイル**ファイルには以下のものが含まれます。

	* Azure Web サイトへ発行するための情報

発行設定または発行プロファイルを使用するユーティリティを使用している場合は、発行設定または発行プロファイルを含むファイルをユーティリティにインポートし、その後、そのファイルを**削除**します。ファイルを保持する必要がある場合、たとえば、プロジェクトに携わる他のユーザーと共有するには、アクセス許可が限定された**暗号化**されたディレクトリなど、セキュリティで保護された場所にファイルを保存します。

さらに、インポートされた資格情報が保護されていることを確認する必要があります。たとえば、**Azure PowerShell** と **Azure クロスプラットフォーム コマンドライン インターフェイス**はどちらも、インポートされた情報を**ホーム ディレクトリ** (Linux または OS X システムでは *~*、Windows システムでは */users/yourusername*) に保存します。希望する場合は、さらにセキュリティを強化するために、オペレーティング システムに対応した暗号化ツールを使用してこれらの場所を**暗号化**できます。

###構成設定と接続文字列
接続文字列、認証資格情報、およびその他の機密情報は構成ファイルに保存するのが一般的な方法です。残念ながら、これらのファイルは Web サイト上で公開される場合やパブリック リポジトリにチェックインされる場合があり、結果としてこの情報が危険にさらされる可能性があります。

Azure Websites では、構成情報を Web サイト ランタイム環境の一部である**アプリケーション設定**および**接続文字列**として保存できます。大半のプログラミング言語では、値は、*環境変数*を介して実行時にアプリケーションに公開されます。.NET アプリケーションの場合、これらの値は実行時に .NET 構成に挿入されます。

**アプリケーション設定**と**接続文字列**は、Azure の管理ポータル、または PowerShell や Azure クロスプラットフォーム コマンドライン インターフェイスなどのユーティリティを使用して構成可能です。

アプリケーション設定と接続文字列の詳細については、「[Web サイトの構成方法](/ja-jp/documentation/articles/web-sites-configure/)」を参照してください。

###FTPS

Azure は、**FTPS** を介して Web サイトのファイル システムへの保護された FTP アクセスを提供します。これによって、Web サイト上のアプリケーション コードや診断ログに安全にアクセスできます。Web サイトの FTPS リンクは、[Azure の管理ポータル](https://manage.windowsazure.com)内の**ダッシュボード**のサイトに掲載されています。

FTPS の詳細については、「[File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol)」を参照してください。

##次のステップ

Azure プラットフォームのセキュリティ、**セキュリティ インシデントまたは迷惑行為**の報告方法、またはサイトの**侵入テスト**の実施予定を Microsoft に通知する方法の詳細については、[Microsoft Azure のトラスト センター](/ja-jp/support/trust-center/security/)のセキュリティのセクションを参照してください。

Azure Websites の **web.config** または **applicationhost.config** ファイルの詳細については、「[Configuration options unlocked in Azure Web Sites (Azure Web サイトでロック解除される構成オプション)](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)」を参照してください。

攻撃の検出に役立つことのある Azure Websites のログ情報の詳細については、「[Azure Websites の診断ログの有効化](/ja-jp/documentation/articles/web-sites-enable-diagnostic-log/)」を参照してください。
