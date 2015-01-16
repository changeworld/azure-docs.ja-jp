<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="DocumentDB を使用した Python Flask Web アプリケーションの構築 | Azure" description="DocumentDB を使用してデータを格納し、Azure にホストされた Python および Flask (MVC) Web アプリケーションからそのデータにアクセスする方法について説明します。"  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong" />

<a name="_Toc395888515"></a><a name="_Toc395809324">DocumentDB を使用した Python Flask (MVC) Web アプリケーションの構築</a>
===========================================================================================================================================

<a name="_Toc395809324">

<a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">概要</a>
========================================================================================================================

<a name="_Toc395888517"></a><a name="_Toc395809326">シナリオ</a>
----------------------------------------------------------------

このドキュメントでは、Azure DocumentDB を効果的に活用して、JSON ドキュメントの保存とクエリを行う方法を紹介します。チュートリアルとして、Azure DocumentDB を使用して投票の Web アプリケーションを構築する例を取り上げます。

チュートリアルでは、Azure に用意されている DocumentDB サービスを使用して、Azure にホストされている Python Web アプリケーションからデータを保存したりデータにアクセスしたりする方法を説明します。Python と Azure Websites の使用経験がある読者を想定しています。

学習内容:

1\. DocumentDB アカウントを作成してプロビジョニングする

2\. Python MVC アプリケーションを作成する

3\. Web アプリケーションから Azure DocumentDB に接続して使用する

4\. Web アプリケーションを Azure Websites にデプロイする

このチュートリアルの手順を実行すると、
アンケートに回答する単純な投票アプリケーションを作成できます。

![Alt text](./media/documentdb-python-application/image1.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">前提条件</a>


この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

Visual Studio 2013 (またはその無償版の [Visual Studio Express][])

Python Tools for Visual Studio ([ここ][]から入手できます)

Azure SDK for Visual Studio 2013, version 2.4 以降 ([ここ][1]から入手できます)

Azure クロスプラットフォーム コマンド ライン ツール ([Microsoft Web Platform Installer][] を通じて入手できます)

<a name="_Toc395888519"></a><a name="_Toc395809328">DocumentDB データベース アカウントの作成</a>
============================================================================================

DocumentDB データベース アカウントを Azure にプロビジョニングするには、Azure の管理ポータルを開いて、ホームページにある Azure ギャラリー タイルをクリックするか、または、画面の左下隅にある [+] をクリックします。

![Alt text](./media/documentdb-python-application/image2.png)


これで Azure のギャラリーが開き、Azure に用意されているさまざまなサービスを選択できます。ギャラリーのカテゴリ一覧から [データ、ストレージ、バックアップ] を選択します。

![Alt text](./media/documentdb-python-application/image3.png)

その中から、Azure DocumentDB に該当するオプションを選択します。

![Alt text](./media/documentdb-python-application/image4.png)

次に、画面の一番下にある [作成] を選択します。

![Alt text](./media/documentdb-python-application/image5.png)

[新しい DocumentDB] ブレードが開き、新しいアカウントの名前、リージョン、スケール、リソース グループなどの設定を指定できます。

![Alt text](./media/documentdb-python-application/image6.png)

アカウントの値を指定した後、[作成] をクリックすると、プロビジョニング プロセスが開始されて、データベース アカウントが作成されます。プロビジョニング プロセスが完了すると、ポータルの通知領域にその旨が表示され、スタート画面上のタイル (タイルを作成する設定になっている場合) が、処理の完了を示す表示に変化します。

![Alt text](./media/documentdb-python-application/image7.png)

プロビジョニングの完了後、スタート画面から DocumentDB タイルをクリックすると、新しく作成した DocumentDB アカウントのメイン ブレードが表示されます。

![Alt text](./media/documentdb-python-application/image8.png)
![Alt text](./media/documentdb-python-application/image9.png)



[キー] ボタンを使用してエンドポイントの URL とプライマリ キーを表示し、それらの値をクリップボードにコピーして、いつでも呼び出せる場所に保存してください。これから作成する Web アプリケーションの中でそれらの値を使用します。

</a>

<a name="_Toc395888520"></a><a name="_Toc395809329">新しい Python Flask Web アプリケーションの作成</a>
=================================================================================================

Visual Studio を開き、[ファイル]、[新しいプロジェクト]、[Python]、[Flask Web プロジェクト] の順に選択して、**tutorial** という名前を付けます。外部パッケージをインストールするかどうかをたずねるメッセージが表示されます。[仮想環境にインストールする] をクリックし、さらに [作成] をクリックします。これで、プロジェクトに必要な Python 仮想環境が設定されます。

Flask は、Python で Web アプリケーションをより迅速に作成するのに役立つ Web フレームワークです。[Flask のチュートリアルにアクセスするには、ここをクリックしてください。][]

![Alt text](./media/documentdb-python-application/image10.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">プロジェクトへの Flask パッケージの追加</a>
==================================

プロジェクトを設定した後は、プロジェクトに必要な特定の Flask パッケージ (たとえば、フォーム) を追加する必要があります。**[env]** を右クリックし、**次の Python パッケージをインストールします (この順序を守ることが重要です)**。

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text](./media/documentdb-python-application/image11.png)

**注: **まれに、出力ウィンドウにエラーが表示されることがあります。その場合は、エラーがクリーンアップに関連しているかどうか確認してください。クリーンアップには失敗しても、インストールは正常に行われる場合があります (出力ウィンドウを上方向にスクロールして確認してください)。
<a name="verify-the-virtual-environment"></a> その場合は続行しても問題ありません。

</h1>
<a name="_Toc395888522"></a><a name="_Toc395809331">仮想環境の確認</a>
======================================================================================


すべてが正しくインストールされているかどうかを確認してみましょう。**F5** キーをクリックして、Web サイトを起動します。Flask 開発サーバーが起動され、Web ブラウザーが開きます。次のページが表示されます。

![Alt text](./media/documentdb-python-application/image12.png)

<a name="_Toc395888523"></a><a name="_Toc395809332">プロジェクトへの DocumentDB の追加</a>
=========================================================================================
 DocumentDB Python SDK は、PyPi でホストされ、pip を使用してインストールできます。

ソリューション エクスプローラーで、[Python 環境] ノードを展開します。対象の環境を右クリックし、[Python パッケージのインストール] を選択します。

![Alt text](./media/documentdb-python-application/image13.png)

PyPi パッケージの名前である "--pre pydocumentdb" を入力します。オプションで既知のバージョンを入力することで、
インストールするバージョンを管理できます。バージョンを指定しない場合は、最新の安定バージョンがインストールされます。"--pre pydocumentdb" のように名前全体を入力する必要があることに注意してください。

![Alt text](./media/documentdb-python-application/image14.png)

pydocumentdb パッケージがダウンロードされ、環境にインストールされます。インストールが完了すると、環境の下にモジュールとして "pydocumentdb" が表示されます。

</h1>
<a name="_Toc395888524"></a><a name="_Toc395809333">データベース、コレクション、およびドキュメント定義の作成</a>
============================================================================================================

**forms.py** python ファイルを追加した後、このファイルをソリューション エクスプローラーの tutorial フォルダーに追加します。次のコードを forms.py に追加します。これから、投票アプリケーションを作成していきます。それには、ユーザー入力に基づいて切り替わる 3 つのブール型フィールドを作成します。

</h1>

    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

<a name="_Toc395888520"></a>
============================

### <a name="_Toc395809338">データベース、コレクション、およびドキュメントの作成</a>

\# 注: 認証資格情報はアプリ内よりも config ファイルに保持する方が安全と考えられます。ここでは、簡略化のために、この情報をソース コードに含めています。views.py 内に create という名前の新しい関数を作成します。次のコードを **views.py** に追加します。既存のコードを削除しないように
注意してください。

</h1>

    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

<a name="_Toc395888529"></a><a name="_Toc395809338">ユーザーによる投票の受け付けとドキュメントの更新</a>
=================================================================================================

### <a name="_Toc395809338">必要な import ステートメントの追加

<a name="_Toc395888529"></a>
============================

**views.py** の先頭に、次の import ステートメントを追加します。これらのステートメントにより、DocumentDB の PythonSDK および Flask パッケージがインポートされます。


    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">データベース、コレクション、およびドキュメントを読み取る</a>

次のコードを **views.py** に追加します。このコードは、フォームの設定と、データベース、コレクション、およびドキュメントの読み取りを行います。views.py 内の既存のコードを削除しないように注意してください。単にこのコードを末尾に追加してください。

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document
    
### <a name="_Toc395809338">Registering the vote and modifying the document</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">結果の表示</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')


### <a name="_Toc395809338">html ファイルの作成</a>

テンプレート フォルダーの下に、
create.html、results.html、vote.html の各 html ファイルを追加します。

次のコードを **create.html** に追加します。このコードは、新しいデータベース、コレクション、およびドキュメントを作成したことを示すメッセージを処理します。

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

次のコードを **results.html** に追加します。このコードは、投票の結果を表示します。
    
    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

次のコードを **vote.html** に追加します。このコードは、アンケートを表示し、投票を受け付けます。投票が登録されると、制御が views.py に渡されます。これにより、投票が行われたことを確認して、ドキュメントを適宜追加できます。

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}
    
<a name="_Toc395888529"></a>
============================

**layout.html** 内のコードを削除し、次のコードで置き換えます。このコードでは、ナビゲーション バーと、対応するルーティング用の URL を設定しています。

index.html 内のコードを削除し、次のコードで置き換えます。これが、アプリケーションのランディング ページとなります。

</h1>
<a name="_Toc395888532"></a><a name="_Toc395809341">構成ファイルを追加し、\_\_init\_\_.py を変更する</a>
----------------------------------------------------------------------------------------------------------------

プロジェクト [tutorial] を右クリックし、**config.py** ファイルを追加します。この config は、Flask のフォームで必要になります。これを使用して秘密キーを指定することもできます。次のコードを config.py に追加します。
    
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

同様に、**\_\_init\_\_.py file** ファイルを追加します。このファイルを tutorial フォルダーの下に配置します。元のコードを次のコードで置き換えます。このコードは、ビューと config.py ファイルの間の接続を処理します。

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

上記の手順を実行した後、ソリューション エクスプローラーの表示は次のようになります。

![Alt text](./media/documentdb-python-application/image15.png)

<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">ローカルでのアプリケーションの実行</a>
============================

Visual Studio で F5 キーを押すかまたは実行ボタンをクリックすると、次のような画面が表示されます。

![Alt text](./media/documentdb-python-application/image16.png)

[Vote] をクリックし、回答を選択します。

![Alt text](./media/documentdb-python-application/image17.png)

回答を投票するごとに、該当するカウンターの値が増加します。次回投票するときは結果が表示されます。

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Azure Websites へのアプリケーションのデプロイ</a>
========================================================================================================================

以上で、DocumentDB と連携するアプリケーションが完成しました。今度は、このアプリケーションを Azure Websites にデプロイします。ソリューション エクスプローラーでプロジェクトを右クリックし (ローカル実行したままになっていないことを確認してください)、[発行] を選択します。

![Alt text](./media/documentdb-python-application/image19.png)


資格情報を入力し、新しい Web サイトを作成するかまたは古い Web サイトを再利用して、Azure Websites を構成します。

![Alt text](./media/documentdb-python-application/image20.png)


数秒すると、Web アプリケーションの発行が完了し、ブラウザーが起動されます。作成したアプリケーションが Azure で実行されているようすが確認できます。

</h1>
<a name="_Toc395809338">まとめ</a>
==========

ご利用ありがとうございます。ここでは初めての方を対象に、Azure DocumentDB を使用して Python アプリケーションを作成し、Azure Websites に発行する方法を説明しました。

完全なソリューションは、[ここ][]から入手できます (注: この場合も、上に説明したように、仮想環境を追加し、Python ツールとパッケージをインストールする必要があります)。

</h1>

  [ここをクリックしてください。]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [Flask のチュートリアルにアクセスするには、ここをクリックしてください。]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [ここ]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft Web プラットフォーム インストーラー]: http://www.microsoft.com/web/downloads/platform.aspx
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

Similarly append the **\_\_init\_\_.py file**. Locate it under the
folder called tutorial. Replace the original code with the following. It
takes care of the connection between the views and the config.py file.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

After following the above mentioned steps, this is how your solution
explorer should look like

![Alt text](./media/documentdb-python-application/image15.png)

<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Run your application locally</a>
============================

Hit F5 or the run button in visual studio and you should see the
following on your screen.

![Alt text](./media/documentdb-python-application/image16.png)

Click on vote and select your option

![Alt text](./media/documentdb-python-application/image17.png)

For every vote you cast it will increment the appropriate counter. When
you vote the next time you will be able to see the results.

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Deploy application to Azure Websites</a>
========================================================================================================================

Now that you have the complete application working correctly against
DocumentDB we're going to deploy this to Azure Websites. Right Click on
the Project in Solution Explorer (make sure you're not still running it
locally) and select Publish.

![Alt text](./media/documentdb-python-application/image19.png)


Configure your Azure Website by providing your credentials and creating
a new website/reusing an old website.

![Alt text](./media/documentdb-python-application/image20.png)


In a few seconds, Visual Studio will finish publishing your web
application and launch a browser where you can see your handy work
running in Azure!

</h1>
<a name="_Toc395809338">Conclusion</a>
==========

Congratulations! You have just built your first Python Application using
Azure DocumentDB and published it to Azure Websites.

If you would like the complete solution, [click here][]. (Note: You will
still have to add the virtual environment, install the python tools and
packages as mentioned above)

</h1>

  [click here]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [Click here to access Flask tutorials]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [here]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
