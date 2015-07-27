<properties 
	pageTitle="Azure App Service Web Apps による Python の構成" 
	description="このチュートリアルでは、Web Server Gateway Interface (WSGI) に準拠した基本的な Python アプリケーションを Azure App Service Web Apps に作成して構成する方法について説明します。" 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>




# Azure App Service Web Apps による Python の構成

このチュートリアルでは、Web Server Gateway Interface (WSGI) に準拠した基本的な Python アプリケーションを [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) に作成して構成する方法について説明します。

仮想環境や、requirements.txt を使用したパッケージ インストールなどの Git デプロイの追加の機能を説明します。


## Bottle、Django、Flask

Azure Marketplace には、Bottle、Django、Flask フレームワーク用のテンプレートが含まれます。 Azure App Service に最初の Web アプリを開発している場合、または Git に習熟していない場合は、これらのチュートリアルのいずれかに従うことをお勧めします。これらのチュートリアルでは、Windows または Mac から Git デプロイを使用して、ギャラリーから作業アプリケーションを構築するためのステップ バイ ステップの手順を紹介しています。

- [Bottle を使用した Web アプリの作成](web-sites-python-create-deploy-bottle-app.md)
- [Django を使用した Web アプリの作成](web-sites-python-create-deploy-django-app.md)
- [Flask を使用した Web アプリの作成](web-sites-python-create-deploy-flask-app.md)


## Azure プレビュー ポータルでの Web アプリの作成

このチュートリアルは、Azure サブスクリプションを既に所有しており、Azure プレビュー ポータルにアクセスできることを前提としています。

既存の Web アプリがない場合、[Azure プレビュー ポータル](https://portal.azure.com)から自分で作成できます。画面左下にある [新規] ボタンをクリックし、次に **[Web + モバイル]**、**[Web アプリ]** の順にクリックします。

## Git 発行

「[Azure App Service での GIT を使用した継続的なデプロイ](web-sites-publish-source-control.md)」の指示に従い、新しく作成した Web アプリで Git 発行を構成します。このチュートリアルでは、Python Web アプリの作成と管理、Azure App Service への発行を Git を使用して行います。

Git 発行の設定が完了すると、Git リポジトリが作成されて Web アプリに関連付けられます。このリポジトリの URL が表示され、以後、ローカル開発環境からクラウドにデータをプッシュする目的で使用できます。Git を介してアプリケーションを発行するには、Git クライアントを併せてインストールする必要があります。提供されるインストラクションに従って Web アプリのコンテンツを Azure App Service にプッシュしてください。


## アプリケーションの概要

次のセクションでは、次のファイルが作成されます。これらは、Git リポジトリのルートに配置する必要があります。

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## WSGI ハンドラー

WSGI は、[PEP 3333](http://www.python.org/dev/peps/pep-3333/) で規定された Python の標準です。Web サーバーと Python 間のインターフェイスを定義します。各種の Web アプリケーションや Web フレームワークを Python を使って記述するためのインターフェイスが標準化されています。今日普及している Python Web フレームワークには WSGI が使用されています。Web フレームワークに必要な機能は Azure App Service Web Apps に用意されています。また、カスタム ハンドラーを WSGI 仕様のガイドラインに準拠させれば、経験豊富なユーザーが Web フレームワークを独自に制作することも可能です。

次は、カスタム ハンドラーを定義する `app.py` の例です。

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

`python app.py` を使用してこのアプリケーションをローカルで実行し、その後 Web ブラウザーで `http://localhost:5555` を参照できます。


## 仮想環境

上記の例のアプリは外部パッケージを必要としませんが、アプリケーションによっては外部パッケージが必要になる場合があります。

外部のパッケージとの依存関係を管理するために、Azure の Git デプロイでは、仮想環境の作成がサポートされています。

Azure がリポジトリのルート ディレクトリに requirements.txt を検出すると、`env` という名前の仮想環境が自動的に作成されます。これは、最初のデプロイ、または選択した Python ランタイムを変更した後のデプロイ時に発生します。

仮想開発環境をローカルで作成する場合、Git リポジトリには含めないでください。


## パッケージの管理

requirements.txt 内のリストにあるパッケージが、pip を使用して仮想環境に自動的にインストールされます。これはデプロイごとに発生しますが、パッケージが既にインストールされている場合は、インストールがスキップされます。

例 `requirements.txt`

    azure==0.8.4


## Python バージョン

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

例 `runtime.txt`

    python-2.7


## web.config

サーバーによる要求の処理方法を指定するには、web.config ファイルを作成する必要があります。

リポジトリに web.x.y.config ファイルがある場合は (ここでは x.y は選択した Python ランタイム)、Azure が適切なファイルを web.config として自動的にコピーします。

次の例の web.config は、次のセクションで説明する仮想環境プロキシ スクリプトに依存します。これらの例は、上記の `app.py` の例で使用した WSGI ハンドラーによって動作します。

Python 2.7 用の`web.config` の例:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Python 3.4 用の`web.config` の例:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


パフォーマンスを向上させるために、静的ファイルは、Python コードを介さずに、Web サーバーによって直接処理されます。

上記の例では、静的ファイルのディスク上の場所は URL の場所と一致する必要があります。つまり、`http://pythonapp.azurewebsites.net/static/site.css` への要求は、ディスクの `\static\site.css` にあるファイルを使用します。

URL の場所とは別のディスク上の場所にあるファイルを使用するように、ルール `Static Files` を構成することもできます。次のルール定義では、`http://pythonapp.azurewebsites.net/static/site.css` への要求は、`\static\site.css` ではなく、ディスクの `\FlaskWebProject\static\site.css` にあるファイルを使用します。

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` は WSGI ハンドラーを指定する場所です。上記の例では、ハンドラーはルート フォルダーの `app.py` の `wsgi_app` という名前の関数であるため、`app.wsgi_app` になります。

`PYTHONPATH` はカスタマイズ可能ですが、requirements.txt で指定することで、仮想環境にすべての依存関係をインストールする場合は、変更する必要はありません。


## 仮想環境のプロキシ

次のスクリプトは、WSGI ハンドラーの取得、仮想環境のアクティブ化、エラーの記録に使用されます。修正することなく、汎用的に使用できます。

`ptvs_virtualenv_proxy.py` の内容:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_HANDLER env var must be set')
        
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None

        if handler is None:
            raise ValueError('"%s" could not be imported' % handler_name)

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
        
        site.main()
        
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## Git デプロイのカスタマイズ

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## トラブルシューティング - パッケージのインストール

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## トラブルシューティング - 仮想環境

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)をご覧ください。





 

<!---HONumber=July15_HO3-->