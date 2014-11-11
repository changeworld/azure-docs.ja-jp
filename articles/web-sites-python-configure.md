<properties linkid="develop-python-tutorials-web-sites-configuration" urlDisplayName="Configuring Python with Azure Websites" pageTitle="Configuring Python with Azure Websites" metaKeywords="" description="This tutorial describes options for authoring and configuring a basic Web server Gateway Interface (WSGI) compliant Python application on Azure Websites." metaCanonical="" services="web-sites" documentationCenter="Python" title="Configuring Python with Azure Websites" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Azure Websites での Python の構成

このチュートリアルでは、Web Server Gateway Interface (WSGI) に準拠した基本的な Python アプリケーションを Azure Websites に作成して構成する方法について説明します。Azure Websites は簡単に使い始めることができ、Python アプリケーションを自由に他の Azure サービスに拡張することができます。Azure Websites プラットフォームには、Python (2.7.3 または 3.4.0 のどちらか適切な方) が用意されているほか、Python 向けの汎用 wfastcgi.py FastCGI ハンドラーが含まれています。Python ハンドラーを使用するように Web サイトを構成するだけでよく、それ以外の作業は必要ありません。

> [WACOM.NOTE] Azure Websites ポータルで使用する Python のバージョンを選択するには、Web サイトの [構成] タブを開き、**[Python バージョン]** の設定を変更します。

Azure Websites に Django フレームワークを構成する、より高度な例については、チュートリアル (
[][]<http://www.windowsazure.com/ja-jp/develop/python/tutorials/web-sites-with-django></a>) を参照してください。

## WSGI サポート

WSGI は、[PEP 3333][PEP 3333] で規定された Python の標準です。Web サーバーと Python 間のインターフェイスを定義します。各種の Web アプリケーションや Web フレームワークを Python を使って記述するためのインターフェイスが標準化されています。今日普及している Python Web フレームワークには WSGI が使用されています。Web フレームワークに必要な機能は Azure Websites に用意されています。また、カスタム ハンドラーを WSGI 仕様のガイドラインに準拠させれば、経験豊富なユーザーが Web フレームワークを独自に制作することも可能です。

## Web サイトの作成

このチュートリアルは、Azure サブスクリプションを既に所有しており、Azure 管理ポータルにアクセスできることを前提としています。Web サイトを作成するにあたっての詳しいガイダンスについては、[][1]<http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-create-websites></a> を参照してください。

簡単に言えば、既存の Web サイトがない場合、Azure 管理ポータルから自分で作成することができます。[Web サイト] 機能を選択し、目的の Web サイトの URL を指定して、[簡易作成] オプションを使用します。

![][0]

## Git 発行

Git 発行を構成するには、新たに作成した Web サイトの [クイック スタート] タブまたは [ダッシュボード] タブを使用します。このチュートリアルでは、Python Web サイトの作成と管理、Azure Websites への発行を Git を使用して行います。

![][2]

Git 発行の設定が完了すると、Git リポジトリが作成されて Web サイトに関連付けられます。このリポジトリの URL が表示され、以後、ローカル開発環境からクラウドにデータをプッシュする目的で使用できます。Git を介してアプリケーションを発行するには、Git クライアントを併せてインストールする必要があります。提供されるインストラクションに従って Web サイトのコンテンツを Azure Websites にプッシュしてください。

## Web サイトの内容

基本的な Python アプリケーションを例として取り上げます。Azure Websites で Python サポートを活かすために最低限必要な作業がわかりやすいように、このアプリケーションには基本的な WSGI ハンドラーが備わっています。このスケルトン Python アプリケーションを土台にして、さまざまなソリューションを作成することができます。次の例の難易度を超えた、本格的な Web フレームワークを作成することも可能です。

次に示しているのは、基本的な WSGI ハンドラーのコードです。WSGI に準拠したアプリケーションの開始部分として [PEP 3333][PEP 3333] が推奨している仕様に沿っています。Web サイト ルートの ConfigurePython フォルダーに ConfigurePython.py という名前のファイルを作成し、この内容を保存しました。

    def application(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        yield 'Hello from Azure Websites\n'

*application* は Python の呼び出し可能型です。WSGI 準拠サーバーから呼び出されるエントリ ポイントとして機能します。この呼び出し可能型オブジェクトは、次に示す 2 つの位置指定引数を受け取ります。

-   *environ*: 各種の環境変数を登録したディクショナリ
-   *start\_response*: HTTP ステータスと応答ヘッダーの転送を目的に Web サーバーから渡される呼び出し可能型。

このハンドラーは、要求を受け取るとその都度 "Hello from Azure Websites" というプレーンテキストを返します。

## 構成の方法

Azure Websites に対する Python アプリケーションの構成には 2 とおりの方法があります。

### 方法 1: ポータル

1.1. ポータルの [構成] タブで FastCGI ハンドラーを登録します。
この例では、Azure Websites に用意されている Python 用 FastCGI ハンドラーを使用しています。同じようにするには、スクリプト プロセッサと FastCGI ハンドラーの引数に次のパスを使用します。

-   Python スクリプト プロセッサのパス: D:\\python27\\python.exe
-   Python FastCGI ハンドラーのパス: D:\\python27\\scripts\\wfastcgi.py

![][3]

1.2. 同じくポータルの [構成] タブでアプリケーションの設定を構成します。
アプリケーションの設定は環境変数に変換されます。Python アプリケーションで必要となる構成値には、この機構を使用できます。ここでは、基本的なアプリケーションの例として、次のように構成しました。

-   PYTHONPATH は、モジュールの検索先ディレクトリに関する情報を Python に伝えます。Azure Websites では、便宜的に D:\\home\\site\\wwwroot が Web サイトのルートとして指定されます。
-   WSGI\_HANDLER は、モジュール (またはパッケージ) の名前と使用する属性を記録します。

![][4]

### 方法 2: web.config

もう 1 つは、Web サイトのルートにある web.config ファイルを使用して、この後説明する各種処理を実行する方法です。web.config を使用した方が、Web アプリケーションの移植性は高くなります。Web アプリケーションには、2 とおりのアプローチで要求をルーティングすることが可能です。1 つは、"\*" というパスを処理するようにハンドラーを設定する方法です。この場合、IIS は、受信したすべての要求を Python 経由でルーティングします。もう 1 つは、パスを具体的に設定する方法です。この場合、Python は、設定されたパスだけを処理対象とし、URL の書き換えによって、あらかじめ決められたパスに各種 URL をリダイレクトします。実際にお勧めするのは、後者のアプローチです。要求のターゲットとして機能する空のハンドラー ファイル (この例では handler.fcgi) を Web サイト ルート配下に置くことで、より高いパフォーマンスが得られます。前者のシナリオでは、静的なコンテンツ (画像ファイル、スタイル シートなど) への要求を含め、すべての要求が Python を経由しなければならず、Web サーバーが静的ファイル用に備えているアクセス最適化の効果が活かされません。後者のアプローチを採用することで、静的コンテンツを効率的に返すことができ、Python の呼び出しを必要最小限に抑えることができます。

2.1. PYTHONPATH 変数を指定します。

> これは、アプリケーション コードの検索先を Python に伝える変数です。この場合も D:\\home\\site\\wwwroot が Web サイトの絶対パスとして使用されます。

2.2. WSGI\_HANDLER 変数を設定します。

> この値は、WSGI ハンドラーの呼び出しを Python に命令するために Azure Websites が使用します。この変数は Python の式を値として持ち、実行時に、呼び出し可能型を WSGI ハンドラーとして返します。

2.3. Python のハンドラーを追加します。

> "handler.fcgi" というパスに対する要求が Python によって処理されることを、このハンドラーで Azure Websites に伝えます。独自の FastCGI ハンドラーや Python 開発スタックを採用する場合を除き、ハンドラーの構文は、以下の例に示した \<handlers\> タグ内の体裁を厳密に踏襲することが大切です。

2.4. handler.fcgi. の URL を書き換えます。

> 常時 handler.fcgi を要求することは、決して最善の方法とは言えません。Python ハンドラーによって処理されるファイルのパスを選ぶために、ここでは URL の書き換えを使用し、すべての URL が独自の Python ハンドラーによって処理されるようにしています。

    <configuration>
        <appSettings>
            <add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
            <add key="WSGI_HANDLER" value="ConfigurePython.application" />
        </appSettings>
        <system.webServer>
            <handlers>
                <add name="PythonHandler" 
                verb="*" path="handler.fcgi" 
                modules="FastCgiModule" 
                scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
                resourceType="Either" />
            </handlers>
            <rewrite>
                <rules>
                    <rule name="Configure Python" stopProcessing="true">
                        <match url="(.*)" ignoreCase="false" />
                        <conditions>
                            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                        </conditions>
                        <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration> 

この例で Web サイト ルートのフォルダー構造は、次のようになっています (Python のフォルダー名とファイル名は大文字と小文字が区別されるため、web.config にそのとおり反映されています)。

-   ConfigurePython\\ConfigurePython.py
-   web.config
-   handler.fcgi

handler.fcgi に対するすべての URL を書き換えて、そのパスを FastCGI 経由で Python に渡すため、同じ名前のプレースホルダー ファイルを作成する必要があります。そのようにしないと、IIS から HTTP 404 エラーが返されます。これは、IIS FastCGI モジュールの内部的な動作です。要求されたファイルは、実際に存在しない限り、指定されたスクリプト プロセッサ アプリケーションに渡せないようになっています。

Web サイトにアクセスして正しく構成されたかをテストしてみましょう。アクセスすると、"Hello from Azure Websites" というメッセージが表示されます。

![][5]

  [0]: http://www.windowsazure.com/ja-jp/develop/python/tutorials/web-sites-with-django
  [PEP 3333]: http://www.python.org/dev/peps/pep-3333/
  [1]: http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-create-websites
  [0]: ./media/web-sites-python-configure/configure-python-create-website.png
  [2]: ./media/web-sites-python-configure/configure-python-git.png
  [3]: ./media/web-sites-python-configure/configure-python-handler-mapping.png
  [4]: ./media/web-sites-python-configure/configure-python-app-settings.png
  [5]: ./media/web-sites-python-configure/configure-python-result.png
