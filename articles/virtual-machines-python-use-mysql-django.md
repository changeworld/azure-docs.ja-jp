<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Django Hello World - MySQL Windows エディション

このチュートリアルでは、1 つの Azure の仮想マシン上で MySQL を Django と組み合わせて使用する方法について説明します。このガイドは、Azure と Django を使用した経験がある読者を対象としています。Azure と Django の概要については、「[Django Hello World][Django Hello World]」を参照してください。このガイドは、読者が MySQL を使用した経験があることも前提としています。MySQL の概要については、[MySQL の Web サイト][MySQL の Web サイト]を参照してください。

このチュートリアルで学習する内容は次のとおりです。

-   MySQL と Django をホストするように Azure の仮想マシンを設定します。このチュートリアルでは Windows Server 2008 R2 でこの設定を行う方法について説明しますが、同じ操作を Azure でホストされている Linux VM で実行することもできます。
-   Python 用 [MySQL ドライバー][MySQL ドライバー]をインストールします。
-   MySQL データベースを使用するように既存の Django アプリケーションを構成します。
-   Python から直接 MySQL を使用します。
-   MySQL Django アプリケーションをホストし実行します。

MySQL データベースを利用し、Azure VM でホストすることによって [Django Hello World][Django Hello World] サンプルを拡張します。*"World"* の部分が別の文字列に置き換わることを確認してください。置き換わる文字列は、MySQL に基づいた Django *カウンター* アプリケーションによって判別され、順次異なる文字列が表示されます。Hello World サンプルの場合と同様、この Django アプリケーションも Azure の仮想マシンでホストされます。

このチュートリアルのプロジェクト ファイルは **C:\\django\\helloworld** に保存され、作成されるアプリケーションは次のようになります。

![][]

[WACOM.INCLUDE [create-account-and-vms-note][create-account-and-vms-note]]

## MySQL と Django をホストするように Windows Azure の仮想マシンを設定する

1.  [ここ][ここ]に記載されている手順に従って、*Windows Server 2008 R2* ディストリビューションの Azure の仮想マシンを作成します。

2.  仮想マシンで MySQL トランザクション用にポートを開きます。

 -   Azure ポータルで新しく作成した仮想マシンに移動し、*[エンドポイント]* タブをクリックします。
 -   画面の下部にある *[エンドポイントの追加]* ボタンをクリックします。
 -   NAME = **mysql**、PROTOCOL = **TCP**、PUBLIC PORT = **3306**、PRIVATE PORT = **3306** と指定して、エンドポイントを追加します。

1.  NAME = **web**、PROTOCOL = **TCP**、PUBLIC PORT = **80**、PRIVATE PORT = **80** と指定して、さらに別のエンドポイントを追加します。これにより、外部インターネット要求は Django が実行されているポート (ポート *80*) にリダイレクトされます。

2.  Windows リモート デスクトップを使用して、新しく作成した Azure の仮想マシンにリモートでログインします。

3.  仮想マシンで TCP ポート **80** を開きます。

 -   **[スタート]** メニューから、**[管理ツール]**、**[セキュリティが強化された Windows ファイアウォール]** の順に選択します。
 -   左側のウィンドウで、**[受信の規則]** を選択します。右側の **[操作]** ウィンドウで、**[新しい規則]** を選択します。
 -   **新規の受信の規則ウイザード**で、**[ポート]** を選択し、**[次へ]** をクリックします。
 -   **[TCP]**、**[特定のローカル ポート]** の順に選択します。ポートとして "80" (Django がリッスンするポート) を指定し、**[次へ]** をクリックします。
 -   **[接続を許可する]** を選択し、**[次へ]** をクリックします。
 -   もう一度 **[次へ]** をクリックします。
 -   規則の名前 ("DjangoPort" など) を指定し、[完了] をクリックします。

1.  仮想マシン上の Windows に対応した最新バージョンの [MySQL Community Server][MySQL Community Server] をインストールします。

    **メモ**:DB は、OS とは異なるデータ パーティションにインストールすることをお勧めします。

 -   *Windows (x86、64 ビット版) の MSI インストーラー*を実行します。[このリンク][MySQL Community Server]をクリックし、お近くのダウンロード ミラーから適切な MSI インストーラーをダウンロードしてください。次のヒントを参照してください。
    -   [Setup Type] として *[Complete]* を選択します。
    -   構成ウィザードで、*[Detailed Configuration]* を選択します。
    -   **ポート番号 3306 で TCP/IP ネットワークが有効になっていることを確認し、そのポートに対するファイアウォールの例外を追加します。**
    -   ルート パスワードを設定し、リモート コンピューターからのルート アクセスを有効にします。
 -   サンプルの ["world" データベース]["world" データベース] (MyISAM バージョン) をインストールします。
    -   Windows Azure の仮想マシンで[この][この] zip ファイルをダウンロードします。
    -   **このファイルを *C:\\Users\\Administrator\\Desktop\\world.sql* に解凍します。**

1.  MySQL をインストールした後で、Windows の *[スタート]* メニューをクリックし、先ほどインストールした *MySQL 5.5 Command Line Client* を実行します。次のコマンドを発行します。

        CREATE DATABASE world;
        USE world;
        SOURCE C:\Users\Administrator\Desktop\world.sql
        CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
        CREATE DATABASE djangoazure;
        GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
        GRANT ALL ON world.* TO 'testazureuser'@'%';
        SELECT name from country LIMIT 1;

    次のような応答画面が表示されます。

    ![][1]

1.  Django アプリケーションを開発する前に、仮想マシンに Python と Django をインストールする必要があります。そのためには、[Web Platform Installer][Web Platform Installer] を使用します。Web PI をインストール後に使用して、"Django" を検索し Django (Python) 製品をインストールします。

     **注:** このチュートリアルに従って Python と Django をインストールをするには、WebPI から Django 製品だけをインストールしてください。*Python Tools for Visual Studio* は必要がありません。また、Azure Python SDK もインストールする**必要はありません**。

1.  MySQL Python クライアント パッケージをインストールします。このパッケージは、[このリンク][このリンク]から直接インストールできます。パッケージのインストールが完了したら、次のコマンドを実行してインストールを確認します。

![][2]

## Django Hello World Web アプリケーションの拡張

1.  「[Django Hello World][Django Hello World]」チュートリアルに記載されている手順を実行して、Django で簡単な "Hello World" Web アプリケーションを作成します。

2.  普段使用しているテキスト エディターで **C:\\django\\helloworld\\helloworld\\settings.py** を開きます。**DATABASES** グローバル辞書を次のように変更します。

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'djangoazure',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                },
            'world': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'world',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                }
            }

    この例からわかるように、MySQL データベースを検索する場所に対して Django の命令を指定しています。

     **注:** Azure (MySQL) VM の**永続的**な IP アドレスに一致するように、*HOST* キーを**変更する必要があります**。ここでは、*HOST* には *ipconfig* Windows コマンドによって報告される情報をそのまま設定してください。

    MySQL VM の IP アドレスに一致するように *HOST* を変更した後は、このファイルを保存し、閉じてください。

1.  ここでは *djangoazure* データベースを参照しているので、このデータベースを使用してみましょう。この手順が終了すると、簡単な*カウンター* アプリケーションのモデルが作成されます。Django を使用してこのアプリケーションを作成するには、次のコマンドを実行します。

        cd C:\django\helloworld
        C:\Python27\python.exe manage.py startapp counter

    上に示した最後のコマンドで、Django から出力が何も報告されない場合は、処理が成功しています。

1.  次のテキストを **C:\\django\\helloworld\\counter\\models.py** に追加します。

        class Counter(models.Model):
            count = models.IntegerField()
            def __unicode__(self):
                return u'%s' % (self.count)

    この操作がすべて終わると、Django の *Model* クラスのサブクラス (*Counter*) が定義されます。このサブクラスには、1 つの整数フィールド (*count*) が含まれています。この簡単なカウンター モデルでは、Django アプリケーションへのアクセス数が記録されます。

1.  次に、Django に対して *Counter* の存在を知らせます。

 -   **C:\\django\\helloworld\\helloworld\\settings.py** をもう一度編集します。*INSTALLED\_APPS* タプルに *'counter'* を追加します。
 -   コマンド プロンプトで、次のコマンドを実行します。

            cd C:\django\helloworld
            C:\Python27\python manage.py sql counter
            C:\Python27\python manage.py syncdb

    これらのコマンドによって、*Counter* モデルが現在使用している Django データベースに格納されます。その結果、次のような出力が生成されます。

        C:\django\helloworld> C:\Python27\python manage.py sql counter
        BEGIN;
        CREATE TABLE `counter_counter` (
            `id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY,
            `count` integer NOT NULL
        )
        ;
        COMMIT;

        C:\django\helloworld> C:\Python27\python manage.py syncdb
        Creating tables ...
        Creating table auth_permission
        Creating table auth_group_permissions
        Creating table auth_group
        Creating table auth_user_user_permissions
        Creating table auth_user_groups
        Creating table auth_user
        Creating table django_content_type
        Creating table django_session
        Creating table django_site
        Creating table counter_counter

        You just installed Django's auth system, which means you don't have any superusers defined.
        Would you like to create one now? (yes/no): no
        Installing custom SQL ...
        Installing indexes ...
        Installed 0 object(s) from 0 fixture(s)

1.  **C:\\django\\helloworld\\helloworld\\views.py** の内容を置き換えます。次に示している *hello* 関数の新しい実装では、*Counter* モデルが、既にインストールされている個別のサンプル データベース (*world*) と組み合わせて使用され、"*World*" の文字列と置き換わる適切な文字列が生成されます。

        from django.http import HttpResponse
        import django.db
        from counter.models import Counter

        def getCountry(intId):
            #Connect to the MySQL sample database 'world'
            cur = django.db.connections['world'].cursor()
            #Execute a trivial SQL query which returns the name of 
            #all countries contained in 'world'
            cur.execute("SELECT name from country")
            tmp = cur.fetchall()
            #Clean-up after ourselves
            cur.close()
            if intId >= len(tmp):
                return "countries exhausted"
            return tmp[intId][0]

        def hello(request):
            if len(Counter.objects.all())==0:
                #when the database corresponding to 'helloworld.counter' is 
                #initially empty...
                c = Counter(count=0)
            else:
                c = Counter.objects.all()[0]
                c.count += 1
            c.save()       
            world = getCountry(int(c.count))
            return HttpResponse("<html><body>Hello <em>" + world + "</em></body></html>")

## Django Web サイトのデプロイと実行

注: テスト環境で Django を実行する方法を次に示します。運用環境で Django を実行するには、「Django Hello World チュートリアル」の「FastCGI での IIS の設定」のセクションに従ってください。Windows ファイアウォール クライアントを使用して、Windows Server 2K8 R2 仮想マシン上でインターネット トラフィックに対してポート 80 を開く操作は、FastCGI では必要ありません。

1.  Windows PowerShell ウィンドウに戻り、次のコマンドを入力し、Django Web サイトをデプロイして公開します。

        PS C:\django\helloworld> $ipPort = 
        PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
        PS C:\django\helloworld> $ipPort += ":80"
        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    **runserver** パラメーターによって、Django は TCP ポート *80* で *helloworld* Web サイトを実行します。このコマンドの結果は次のようになります。

        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
        Validating models...

        0 errors found
        Django version 1.4, using settings 'helloworld.settings'
        Development server is running at http://123.34.56.78:80
        Quit the server with CTRL-BREAK.

2.  ローカルの Web ブラウザーで、**http://*yourVmName*.cloudapp.net** を開きます (*yourVmName* は仮想マシンの作成手順で使用した名前です)。以下のスクリーンショットに示すように、"Hello ...!" が表示されます。これは、Django が仮想マシンで実行され、正常に動作していることを表しています。

    ![][]

    Web ブラウザーの表示を数回更新すると、メッセージが *"Hello **\<国名 abc\>**"* から *"Hello **\<別の国名\>**"* に変わります。

1.  Django による Web サイトのホストを停止するには、PowerShell に切り替えて、**CTRL-C** キーを押すだけです。

## Azure の仮想マシンのシャットダウン

このチュートリアルが終了したら、新しく作成した Azure の仮想マシンをシャットダウンまたは削除して、他のチュートリアル用にリソースを解放し、Azure に対する利用料金の発生を回避します。

  [Django Hello World]: http://windowsazure.com/ja-jp/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [MySQL の Web サイト]: http://dev.mysql.com/doc/
  [MySQL ドライバー]: http://pypi.python.org/pypi/MySQL-python/1.2.3
  []: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [ここ]: /ja-jp/manage/windows/tutorials/virtual-machine-from-gallery/
  [MySQL Community Server]: http://dev.mysql.com/downloads/mysql/
  ["world" データベース]: http://dev.mysql.com/doc/index-other.html
  [この]: http://downloads.mysql.com/docs/world.sql.zip
  [1]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png
  [Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [このリンク]: http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=
  [2]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png
  [http://yourVmName]: http://*yourVmName
