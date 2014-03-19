<properties linkid="develop-python-web-app-with-django-and-mysql" UrlDisplayName="Django と MySQL を使用した Web" pageTitle="Django と MySQL を使用した Python Web アプリケーション - Windows Azure チュートリアル" MetaKeywords="Azure Django Web アプリケーション, Azure Django MySQL, Azure Django Python" description="1 つの Windows Azure の仮想マシン上で MySQL を Django と組み合わせて使用する方法について説明するチュートリアルです。コード サンプルは Python で記述されています。" metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows エディション" authors=""  solutions="" writer="" manager="" editor=""  />







# Django Hello World - MySQL Windows エディション#
 
このチュートリアルでは、1 つの Windows Azure の仮想マシン上で MySQL を Django と組み合わせて使用する方法について説明します。このガイドは、Windows Azure と Django を使用した経験がある読者を対象としています。Windows Azure と Django の概要については、「[Django Hello World] [djangohelloworld]」を参照してください。このガイドは、読者が MySQL を使用した経験があることも前提としています。MySQL の概要については、[MySQL の Web サイト][mysqldoc]を参照してください。

このチュートリアルで学習する内容は次のとおりです。

* MySQL と Django をホストするように Windows Azure の仮想マシンを設定します。このチュートリアルでは Windows Server 2008 R2 でこの設定を行う方法について説明しますが、同じ操作を Windows Azure でホストされている Linux VM で実行することもできます。
* Python 用 [MySQL ドライバー] [mysqlpy]をインストールします。
* MySQL データベースを使用するように既存の Django アプリケーションを構成します。
* Python から直接 MySQL を使用します。
* MySQL Django アプリケーションをホストし実行します。

MySQL データベースを利用し、Windows Azure VM でホストすることによって [Django Hello World] [djangohelloworld] サンプルを拡張します。*World* の部分が別の文字列に置き換わることを確認してください。置き換わる文字列は、MySQL に基づいた Django カウンター** アプリケーションによって判別され、順次異なる文字列が表示されます。Hello World サンプルの場合と同様、この Django アプリケーションも Windows Azure の仮想マシンでホストされます。

このチュートリアルのプロジェクト ファイルは **C:\django\helloworld** に保存され、作成されるアプリケーションは次のようになります。

![][0]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## MySQL と Django をホストするように Windows Azure の仮想マシンを設定する
1.[ここ][preview-portal-vm]に記載されている手順に従って、*Windows Server 2008 R2* ディストリビューションの Windows Azure の仮想マシンを作成します。

1.仮想マシンで MySQL トランザクション用にポートを開きます。
 * Windows Azure ポータルで新しく作成した仮想マシンに移動し、*[エンドポイント]* タブをクリックします。
 * 画面の下部にある *[エンドポイントの追加]* ボタンをクリックします。
 * NAME = **mysql**、PROTOCOL = **TCP**、PUBLIC PORT = **3306**、PRIVATE PORT = **3306** で、エンドポイントを追加します。

1.NAME = **web**、PROTOCOL = **TCP**、PUBLIC PORT = **80**、PRIVATE PORT = **80** で、別のエンドポイントを追加します。これにより、外部インターネット要求は Django が実行されているポート (ポート *80*) にリダイレクトされます。

1.Windows *リモート デスクトップ*を使用して、新しく作成した Windows Azure の仮想マシンにリモートでログインします。

1.仮想マシンで TCP ポート **80** を開きます。
 * **[スタート]** メニューから、**[管理ツール]**、**[セキュリティが強化された Windows ファイアウォール]** の順に選択します。
 * 左側のウィンドウで **[受信の規則]** を選択します。右側の **[操作]** ウィンドウで **[新しい規則]** を選択します。
 * **新規の受信の規則ウイザード**で **[ポート]** を選択し、**[次へ]** をクリックします。
 * **[TCP]**、**[特定のローカル ポート]** の順に選択します。ポートとして "80" (Django がリッスンするポート) を指定し、**[次へ]** をクリックします。
 * **[接続を許可する]** を選択し、**[次へ]** をクリックします。
 * もう一度 **[次へ]** をクリックします。
 * 規則の名前 ("DjangoPort" など) を指定し、[完了] をクリックします。

1.仮想マシン上の Windows に対応した最新バージョンの [MySQL Community Server] [mysqlcommunity] をインストールします。

	**注:** DB は、OS とは異なるデータ パーティションにインストールすることをお勧めします。

 * *Windows (x86、64 ビット版) の MSI インストーラー*を実行します。[このリンク] [mysqlcommunity]をクリックし、お近くのダウンロード ミラーから適切な MSI インストーラーをダウンロードしてください。次のヒントを参照してください。
     * [Setup Type] として *[Complete]* を選択します。
     * 構成ウィザードで、*[Detailed Configuration]* を選択します。
     * **ポート番号 3306 で TCP/IP ネットワークが有効になっていることを確認し、そのポートに対するファイアウォールの例外を追加します。**
     * ルート パスワードを設定し、リモート コンピューターからのルート アクセスを有効にします。
 * サンプルの ["world" データベース] [mysqlworld] (MyISAM バージョン) をインストールします。
     * Windows Azure の仮想マシンで[この] [mysqlworlddl] zip ファイルをダウンロードします。
     * **このファイルを *C:\Users\Administrator\Desktop\world.sql* に解凍します。**
1.MySQL をインストールした後で、Windows の *[スタート]* メニューをクリックし、先ほどインストールした *MySQL 5.5 Command Line Client* を実行します。次のコマンドを発行します。

		CREATE DATABASE world;
		USE world;
		SOURCE C:\Users\Administrator\Desktop\world.sql
		CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
		CREATE DATABASE djangoazure;
		GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
		GRANT ALL ON world.* TO 'testazureuser'@'%';
		SELECT name from country LIMIT 1;

  次のような応答画面が表示されます。

![][2]

1.Django アプリケーションを開発する前に、仮想マシンに Python と Django をインストールする必要があります。[Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) を使用してインストールすることもできます。Web PI をインストールしたら、Web PI で "Django" を検索し、Django (Python) 製品をインストールしてください。

  **注:** WebPI から *Django* 製品をインストールするだけで、このチュートリアルを利用できます。*Python Tools for Visual Studio* は**不要です**。また、Windows Azure Python SDK もインストールする必要はありません。

1.MySQL Python クライアント パッケージをインストールします。[このリンク] [mysqlpydl].から直接インストールできます。パッケージのインストールが完了したら、次のコマンドを実行してインストールを確認します。

![][1]


## Django Hello World Web アプリケーションの拡張
1.「[Django Hello World (Django Hello World)] [djangohelloworld]」チュートリアルに記載されている手順を実行して、Django で簡単な "Hello World" Web アプリケーションを作成します。

1.普段使用しているテキスト エディターで、**C:\django\helloworld\helloworld\settings.py** を開きます。**DATABASES** グローバル辞書を次のように変更します。

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
 
  **注:** Windows Azure (MySQL) VM の**永続的な** IP アドレスに一致するように、*HOST* キーを**変更する必要があります**。ここでは、*HOST* には *ipconfig* Windows コマンドによって報告される情報をそのまま設定してください。

  MySQL VM の IP アドレスに一致するように *HOST* を変更した後は、このファイルを保存し、閉じてください。

1.ここでは *djangoazure* データベースを参照しているので、このデータベースを使用してみましょう。この手順が終了すると、簡単なカウンター アプリケーションのモデルが作成されます**。Django を使用してこのアプリケーションを作成するには、次のコマンドを実行します。

		cd C:\django\helloworld
		C:\Python27\python.exe manage.py startapp counter

  上に示した最後のコマンドで、Django から出力が何も報告されない場合は、処理が成功しています。

1.次のコードを **C:\django\helloworld\counter\models.py** に追加します。

		class Counter(models.Model):
		    count = models.IntegerField()
		    def __unicode__(self):
		        return u'%s' % (self.count)

  この操作がすべて終わると、Django の *Model* クラスのサブクラス (*Counter*) が定義されます。このサブクラスには、1 つの整数フィールド (*count*) が含まれています。この簡単なカウンター モデルでは、Django アプリケーションへのアクセス数が記録されます。

1.次に、Django に対して *Counter* の存在を知らせます。
 * もう一度 **C:\django\helloworld\helloworld\settings.py** を編集します。*INSTALLED_APPS* タプルに *'counter'* を追加します。
 * コマンド プロンプトで、次のコマンドを実行します。

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

1.**C:\django\helloworld\helloworld\views.py** の内容を置き換えます。以下の *hello* 関数の新しい実装では、*Counter* モデルが、既にインストールされている個別のサンプル データベース (*world*) と組み合わせて使用され、"*World*" の文字列と置き換わる適切な文字列が生成されます。

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



1.Windows PowerShell ウィンドウに戻り、次のコマンドを入力し、Django Web サイトをデプロイして公開します。

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

1.ローカルの Web ブラウザーで、**http://*yourVmName*.cloudapp.net** を開きます (*yourVmName* は仮想マシンの作成手順で使用した名前です)。次のスクリーンショットに示すように、"Hello ... !" と表示されます。これは、Django が仮想マシンで実行され、正常に動作していることを表しています。

![][5]

  Web ブラウザーの表示を数回更新すると、メッセージが *"Hello **&lt;country abc&gt;**"* から *"Hello **&lt;some other country&gt;**"* に変わることがわかります。

1.Django による Web サイトのホストを停止するには、PowerShell に切り替えて、**CTRL-C** キーを押すだけです。


## Windows Azure の仮想マシンのシャットダウン

このチュートリアルが終了したら、新しく作成した Windows Azure の仮想マシンをシャットダウンまたは削除して、他のチュートリアル用にリソースを解放し、Windows Azure に対する利用料金の発生を回避します。

[0]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png
[1]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png
[2]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png
[5]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png

[djangohelloworld]: http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server

[mysqldoc]: http://dev.mysql.com/doc/
[mysqlpy]: http://pypi.python.org/pypi/MySQL-python/1.2.3

[mysqlpydl]: http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=
[mysqlcommunity]:http://dev.mysql.com/downloads/mysql/

[mysqlworld]:http://dev.mysql.com/doc/index-other.html
[mysqlworlddl]:http://downloads.mysql.com/docs/world.sql.zip


[preview-portal-vm]: /en-us/manage/windows/tutorials/virtual-machine-from-gallery/

[Stop-AzureService コマンドの状態]: ../Media/django-helloworld-ps-stop.png
[Remove-AzureService コマンドの状態]: ../Media/django-helloworld-ps-remove.png

[インストール ガイド]: http://windowsazure.com/en-us/documentation/articles/python-how-to-install
 

