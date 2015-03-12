<properties 
	pageTitle="サイトの変換と拡張" 
	description="TBD" 
	authors="cephalin" 
	writer="cephalin" 
	editor="mollybos" 
	manager="wpickett" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# サイトの変換と拡張

[XML ドキュメント変換](http://msdn.microsoft.com/library/dd465326.aspx) (XDT) の宣言を使用して、Windows Azure Websites 内の [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) ファイルを変換できます。また、XDT 宣言を使用して、プライベート サイト拡張機能を追加し、カスタム サイト管理アクションを有効にすることもできます。この記事には、Web インターフェイスを使用して PHP 設定の管理を有効にする、サンプルの PHP Manager サイト拡張機能が含まれています。


<!-- MINI TOC -->

* [ApplicationHost.config 内でのサイト構成の変換](#transform)
* [サイトの拡張](#extend)
	* [プライベート サイト拡張機能の概要](#overview)
	* [サイト拡張機能の例: PHP Manager](#SiteSample)
		* [PHP Manager Web アプリケーション](#PHPwebapp)
		* [applicationHost.xdt ファイル](#XDT)
	* [サイト拡張機能の配置](#deploy)

<h2><a id="transform"></a>ApplicationHost.config 内でのサイト構成の変換</h2>
Azure Websites プラットフォームにより、サイト構成の柔軟性と制御を実現できます。IIS の標準的な ApplicationHost.config 構成ファイルは、Windows Azure Websites 内で直接編集することはできませんが、プラットフォームは XML ドキュメント変換 (XDT) に基づく宣言型の ApplicationHost.config 変換モデルをサポートします。

この変換機能を利用するには、XDT の内容を使用して ApplicationHost.xdt ファイルを作成し、サイトのルートの直下に配置します。次に、Windows Azure ポータルの **[構成]** ページで、 `WEBSITE_PRIVATE_EXTENSIONS` アプリケーション設定を 1 に設定します (サイトを再起動することが必要になる場合があります)。 

PHP 5.4 を使用するサイトに新しいカスタム環境変数を追加する方法を、次の applicationHost.xdt サンプルに示します。

	<?xml version="1.0"?> 
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"> 
  		<system.webServer> 
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />	
         				</environmentVariables>
      				</application>
    			</fastCgi> 
  		</system.webServer> 
	</configuration> 

 
変換ステータスと詳細を記録したログ ファイルは、FTP ルートの LogFiles\Transform で利用できます。

その他のサンプルについては、[https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) をご覧ください。

**注**<br />
 `system.webServer` の下にあるモジュールの一覧に所属している要素の削除や並べ替えは実行できませんが、この一覧への追加は可能です。 


<h2><a id="extend"></a>サイトの拡張</h2>
<h3><a id="overview"></a>プライベート サイト拡張機能の概要</h3>
Azure Websites では、サイト管理アクションの機能拡張ポイントとして、サイト拡張機能をサポートしています。実際、Azure Websites プラットフォームの一部の機能は、事前インストールされたサイト拡張機能として実装されています。事前インストールされたプラットフォーム拡張機能を変更することはできませんが、独自サイトでプライベート拡張機能を作成および構成できます。この機能も、XDT 宣言に依存します。プライベート サイト拡張機能を作成するための主要な手順を次に示します。

1. サイト拡張の**コンテンツ**: Azure Websites でサポートされている任意の Web アプリケーションを作成する
2. サイト拡張の**宣言**: ApplicationHost.xdt ファイルを作成する
3. サイト拡張の**展開**:  `root` の下にある SiteExtensions フォルダー内にコンテンツを配置する
4.  サイト拡張の**有効化**:  `WEBSITE_PRIVATE_EXTENSIONS` アプリケーション設定を 1 に設定する

Web アプリケーションに対する内部リンクは、ApplicationHost.xdt ファイル内で指定したアプリケーション パスを基準とする相対パスを指す必要があります。ApplicationHost.xdt ファイルに対してどのような変更を加えた場合でも、サイトのリサイクル、つまり停止と再起動が必要です。 

**注**: これらの主要な要素の詳細については、[https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) をご覧ください。プライベート サイト拡張機能を作成して有効にする手順を示す、詳細な例が掲載されています。その方針に従う PHP Manager のサンプルに対応するソース コードは、[https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager) からダウンロードできます。

<h3><a id="SiteSample"></a>サイト拡張機能の例: PHP Manager</h3>

PHP Manager は、PHP の .ini ファイルを直接変更する代わりに、Web インターフェイスを使用して PHP 設定を簡単に表示および構成するためにサイト管理者が使用できるサイト拡張機能です。PHP 用の一般的な構成ファイルに該当するのは、Program Files の下にある php.ini ファイルと、サイトのルート フォルダーにある user.ini ファイルです。Azure Websites プラットフォーム上で php.ini ファイルを直接編集することはできないため、PHP Manager 拡張機能は user.ini ファイルを使用して設定の変更を適用します。

<h4><a id="PHPwebapp"></a>PHP Manager Web アプリケーション</h4>
	
PHP Manager Web サイトのホーム ページを次に示します。

![TransformSitePHPUI][TransformSitePHPUI]

ここからわかるように、サイト拡張機能は通常の Web アプリケーションに似ていますが、サイトのルート フォルダーに追加の ApplicationHost.xdt ファイルが配置されています (この記事の次のセクションには、ApplicationHost.xdt ファイルに関する詳細が掲載されています)。

PHP Manager 拡張機能は、Visual Studio ASP.NET MVC 4 Web アプリケーション テンプレートを使用して作成されたものです。ソリューション エクスプローラーの次のビューに、PHP Manager サイト拡張機能の構造を示します。

![TransformSiteSolEx][TransformSiteSolEx]

ファイル I/O で必要とされる唯一の特殊なロジックは、サイトの wwwroot ディレクトリが配置されている場所を示すことです。次のコード例に示すように、環境変数 "HOME" はサイトのルート パスを示しており、"site\wwwroot" を追加すると wwwroot パスを作成できます。


	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
    		return userSettingsFile;
	} 


ディレクトリ パスを作成した後、ファイルを読み書きする通常のファイル I/O 操作を使用できます。

サイト拡張機能に関して注意する 1 つの点は、内部リンクの処理に関係しています。サイト上の内部リンクに対応する絶対パスを指定した HTML ファイル内で任意のリンクを使用している場合は、それらのリンクの先頭に、サイトのルートとして拡張機能名を追加する必要があります。拡張機能にとってサイトのルートは、"/" のみではなく、"/`[拡張機能名]`/"になったため、このような指定が必要です。したがって、すべての内部リンクも同様に更新する必要があります。たとえば、コード内に次のリンクが含まれているとします。 

`"<a href="/Home/Settings">PHP Settings</a>"`

このリンクがサイト拡張機能の一部である場合は、リンクを次の形式にする必要があります。

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"` 

Web サイト内で相対パスのみを使用するか、ASP.NET Web サイトの場合は適切なリンクを自動的に作成する `@Html.ActionLink` メソッドを使用する方法により、この要件を回避できます。

<h4><a id="XDT"></a>applicationHost.xdt ファイル</h4>

サイト拡張機能に対応するコードは、%HOME%\SiteExtensions\[拡張機能名] の下に配置されます。これを、拡張機能ルートと呼びます。  

開発したサイト拡張機能を applicationHost.config ファイルに登録するには、ApplicationHost.xdt という名前のファイルを拡張機能のルートに配置する必要があります。ApplicationHost.xdt ファイルの内容を次のようにする必要があります。

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

拡張機能名として選択する名前は、拡張機能のルート フォルダーと同じ名前にする必要があります。

この操作により、SCM サイトの下にある  `system.applicationHost` サイト一覧に対して、新しいアプリケーション パスを追加する効果が得られます。SCM サイトは、特定のアクセス資格情報を持つサイト管理エンドポイントです。それには、URL `https://[your-site-name].scm.azurewebsites.net` があります。  

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

<h3><a id="deploy"></a>サイト拡張機能の配置</h3>

サイト拡張機能をインストールするために、FTP を使用して、拡張機能のインストール先となるサイトの \SiteExtensions\[拡張機能名] フォルダーに Web アプリケーションのすべてのファイルをコピーできます。この場所に ApplicationHost.xdt ファイルもコピーしてください。

次に、Windows Azure Websites ポータルで、拡張機能のインストール先である Web サイトに対応する **[構成]** タブに移動します。**[アプリケーション設定]** セクションで、 `WEBSITE_PRIVATE_EXTENSIONS` キーを追加し、"1" という値を指定します。

![TransformSiteappSettings][TransformSiteappSettings]

最後に、Windows Azure ポータルで Web サイトを再起動し、拡張機能を有効にします。

![TransformSiteRestart][TransformSiteRestart]

次の場所で、サイト拡張機能が表示されます。


`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]` 

HTTPS が使用され、".scm" が含まれていることを除き、開発するサイトの URL に似た URL を使用することにご注意ください。 

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
[TransformSiteappSettings]: ./media/web-sites-transform-extend/TransformSiteappSettings.png
[TransformSiteRestart]: ./media/web-sites-transform-extend/TransformSiteRestart.png





<!--HONumber=42-->
