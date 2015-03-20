<properties 
    pageTitle="DocumentDB を使用した Python Flask Web アプリケーションの構築 | Azure" 
    description="DocumentDB を使用してデータを格納し、Azure にホストされた Python および Flask (MVC) Web アプリケーションからそのデータにアクセスする方法について説明します。" 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

# DocumentDB を使用した Python Flask (MVC) Web アプリケーションの構築
------------------------------------------------------------------------

このドキュメントでは、Azure DocumentDB を効果的に活用して、JSON ドキュメントの保存とクエリを行う方法を紹介します。チュートリアルとして、Azure DocumentDB を使用して投票の Web アプリケーションを構築する例を取り上げます。

チュートリアルでは、Azure に用意されている DocumentDB サービスを使用して、Azure にホストされている Python Web アプリケーションからデータを保存したりデータにアクセスしたりする方法を説明します。Python と Azure Websites の使用経験がある読者を想定しています。

このチュートリアルでは、次の項目について説明します。

1. DocumentDB アカウントを作成してプロビジョニングする

2. Python MVC アプリケーションを作成する

3. Web アプリケーションから Azure DocumentDB に接続して使用する

4. Web アプリケーションを Azure Websites にデプロイする

このチュートリアルの手順を実行すると、
アンケートに回答する単純な投票アプリケーションを作成できます。

![Alt text](./media/documentdb-python-application/image1.png)


## 前提条件

この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

- Visual Studio 2013 (またはその無償版の [Visual Studio Express][])

- Python Tools for Visual Studio ([ここ][]から入手できます)

- Azure SDK for Visual Studio 2013, version 2.4 以降 ([ここ][1]から入手できます)

- Python 2.7 ([ここ][2]から入手できます)

- Python 2.7 用の Microsoft Visual C++ コンパイラ ([ここ][3]から入手できます)

## 手順 1:DocumentDB データベース アカウントの作成

DocumentDB データベース アカウントを Azure にプロビジョニングするには、[Azure の管理ポータル][]を開いて、ホームページにある Azure ギャラリー タイルをクリックするか、または、画面の左下隅にある [+] をクリックします。

![Alt text](./media/documentdb-python-application/image2.png)


これで Azure Marketplace が開き、Azure に用意されているさまざまなサービスを選択できます。Marketplace のカテゴリ一覧から [データ & 分析] を選択します。

![Alt text](./media/documentdb-python-application/image3.png)

そこで「documentdb」を検索し、DocumentDB に該当するオプションを選択します。

![Alt text](./media/documentdb-python-application/image4.png)

次に、画面の一番下にある [作成] を選択します。

![Alt text](./media/documentdb-python-application/image5.png)

[新しい DocumentDB] ブレードが開き、新しいアカウントの名前、リージョン、スケール、リソース グループなどの設定を指定できます。

![Alt text](./media/documentdb-python-application/image6.png)

アカウントの値を指定した後、[作成] をクリックすると、プロビジョニング プロセスが開始されて、データベース アカウントが作成されます。
プロビジョニング プロセスが完了すると、ポータルの通知領域にその旨が表示され、スタート画面上のタイル (タイルを作成する設定になっている場合) が、処理の完了を示す表示に変更されます。

![Alt text](./media/documentdb-python-application/image7.png)

プロビジョニングの完了後、スタート画面から DocumentDB タイルをクリックすると、新しく作成した DocumentDB アカウントのメイン ブレードが表示されます。

![Alt text](./media/documentdb-python-application/image8.png)

[キー] ボタンを使用してエンドポイントの URL とプライマリ キーを表示し、それらの値をクリップボードにコピーして、いつでも呼び出せる場所に保存してください。これから作成する Web アプリケーションの中でそれらの値を使用します。


## 手順 2:新しい Python Flask Web アプリケーションの作成

Visual Studio を開き、[ファイル]、[新しいプロジェクト]、[Python]、[Flask Web プロジェクト] の順に選択して、**tutorial** という名前を付けます。 

Flask は、Python で Web アプリケーションをより迅速に作成するのに役立つ Web フレームワークです。[Flask のチュートリアルにアクセスするには、ここをクリックしてください][]。

![Alt text](./media/documentdb-python-application/image9.png)

外部パッケージをインストールするかどうかをたずねるメッセージが表示されます。**[仮想環境にインストールする]** をクリックします。現時点では PyDocumentDB は Python 3.x をサポートしていないため、必ず Python 2.7 をベースの環境として使用してください。これで、プロジェクトに必要な Python 仮想環境が設定されます。

![Alt text](./media/documentdb-python-application/image10.png)


## 手順 3:Python Flask Web アプリケーションの変更


### プロジェクトへの Flask パッケージの追加

プロジェクトを設定した後は、プロジェクトに必要な特定の Flask パッケージ (DocumentDB 用の Python パッケージ pydocumentdb など) を追加する必要があります。**requirements.txt** という名前のファイルを開き、既存のコードを次のコードに置き換えます。

    flask==0.9
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup
    pydocumentdb>=0.9.4-preview

**[env]** を右クリックし、**[requirements.txt からインストール]** をクリックします。

![Alt text](./media/documentdb-python-application/image11.png)

**注:** まれに、出力ウィンドウにエラーが表示されることがあります。その場合は、エラーがクリーンアップに関連しているかどうかを確認してください。クリーンアップには失敗しても、インストールは正常に行われる場合があります (出力ウィンドウを上方向へスクロールして確認してください)。
<a name="verify-the-virtual-environment"></a>その場合は続行しても問題ありません。


### 仮想環境の確認

すべてが正しくインストールされているかどうかを確認してみましょう。**F5** キーを押して、Web サイトを起動します。Flask 開発サーバーが起動され、Web ブラウザーが開きます。次のページが表示されます。

![Alt text](./media/documentdb-python-application/image12.png)

### データベース、コレクション、およびドキュメント定義の作成

ソリューション エクスプローラーの **tutorial** という名前のフォルダーを右クリックして、Python ファイルを追加します。このファイルに **forms.py** という名前を付けます。これから、投票アプリケーションを作成していきます。

    from flask.ext.wtf import Form
    from wtforms import RadioField

    class VoteForm(Form):
        deploy_preference  = RadioField('Deployment Preference', choices=[
            ('Web Site', 'Web Site'),
            ('Cloud Service', 'Cloud Service'),
            ('Virtual Machine', 'Virtual Machine')], default='Web Site')

### views.py への必要な import ステートメントの追加

**views.py** の先頭に、次の import ステートメントを追加します。これらのステートメントにより、DocumentDB の PythonSDK および Flask パッケージがインポートされます。


    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    

### データベース、コレクション、およびドキュメントの作成

次のコードを **views.py** に追加します。これにより、フォームで使用されるデータベースの作成が処理されます。**views.py** 内の既存のコードを削除しないように注意してください。単にこのコードを末尾に追加してください。

    @app.route('/create')
    def create():
        """Renders the contact page."""
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
    
        # Attempt to delete the database.  This allows this to be used to recreate as well as create
        try:
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            client.DeleteDatabase(db['_self'])
        except:
            pass
    
        # Create database
        db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        # Create collection
        collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })
        # Create document
        document = client.CreateDocument(collection['_self'],
            { 'id': config.DOCUMENTDB_DOCUMENT,
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': config.DOCUMENTDB_DOCUMENT })
    
        return render_template(
            'create.html', 
            title='Create Page', 
            year=datetime.now().year,
            message='You just created a new database, collection, and document.  Your old votes have been deleted')


### データベース、コレクション、およびドキュメントの読み取りと、フォームの送信

次のコードを **views.py** に追加します。このコードは、フォームの設定と、データベース、コレクション、およびドキュメントの読み取りを行います。**views.py** 内の既存のコードを削除しないように注意してください。単にこのコードを末尾に追加してください。
    
    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = VoteForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            # Read databases and take first since id should not be duplicated.
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            # Read collections and take first since id should not be duplicated.
            coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            # Read documents and take first since id should not be duplicated.
            doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
            # Take the data from the deploy_preference and increment our database
            doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            replaced_document = client.ReplaceDocument(doc['_self'], doc)
    
            # Create a model to pass to results.html
            class VoteObject:
                choices = dict()
                total_votes = 0
    
            vote_object = VoteObject()
            vote_object.choices = {
                "Web Site" : doc['Web Site'],
                "Cloud Service" : doc['Cloud Service'],
                "Virtual Machine" : doc['Virtual Machine']
            }
            vote_object.total_votes = sum(vote_object.choices.values())
    
            return render_template(
                'results.html', 
                year=datetime.now().year, 
                vote_object = vote_object)
    
        else :
            return render_template(
                'vote.html', 
                title = 'Vote',
                year=datetime.now().year,
                form = form)


### html ファイルの作成

templates フォルダーの下に、create.html、results.html、vote.html の各 html ファイルを追加します。

次のコードを **create.html** に追加します。このコードは、新しいデータベース、コレクション、およびドキュメントを作成したことを示すメッセージを処理します。

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}

次のコードを **results.html** に追加します。このコードは、投票の結果を表示します。

    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
    <br />

    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
        <div class="col-sm-5">
            <div class="progress">
                <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    {{vote_object.choices[choice]}}
                </div>
            </div>
        </div>
    </div>
    {% endfor %}

    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}

次のコードを **vote.html** に追加します。このコードは、アンケートを表示し、投票を受け付けます。投票が登録されると、制御が views.py に渡されます。これにより、投票が行われたことを確認して、ドキュメントを適宜追加できます。

    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
        {{form.deploy_preference}}
        <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}

**index.html** の内容を次の内容に置き換えます。これが、アプリケーションのランディング ページとなります。

    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}


### 構成ファイルの追加と、\_\_init\_\_.py の変更

プロジェクト名の [tutorial] を右クリックし、**config.py** ファイルを追加します。
この config は、Flask のフォームで必要になります。これを使用して秘密キーを指定することもできます。このチュートリアルでは必要ありません。次のコードを config.py に追加します。   **DOCUMENTDB\_HOST** および **DOCUMENTDB\_KEY** の値を変更します。

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'


同様に、**\_\_init\_\_.py** の内容を次の内容に置き換えます。

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

上記の手順を実行した後、ソリューション エクスプローラーの表示は
次のようになります。

![Alt text](./media/documentdb-python-application/image15.png)


## 手順 4:ローカルでのアプリケーションの実行

Visual Studio で F5 キーを押すかまたは実行ボタンをクリックすると、次のような画面が表示されます。

![Alt text](./media/documentdb-python-application/image16.png)

[投票データベースの作成/クリア] をクリックして、データベースを生成します。

![Alt text](./media/documentdb-python-application/image17.png)

次に、[投票] をクリックし、回答を選択します。

![Alt text](./media/documentdb-python-application/image18.png)

回答を投票するごとに、該当するカウンターの値が増加します。

![Alt text](./media/documentdb-python-application/image19.png)


## 手順 5:Azure Websites へのアプリケーションのデプロイ

以上で、DocumentDB と連携するアプリケーションが完成しました。今度は、このアプリケーションを Azure Websites にデプロイします。ソリューション エクスプローラーでプロジェクトを右クリックし (ローカル実行したままになっていないことを確認してください)、[発行] を選択します。次に、[Microsoft Azure Websites] を選択します。

![Alt text](./media/documentdb-python-application/image20.png)


資格情報を入力し、[発行] をクリックして、Azure Web サイトを構成します。

![Alt text](./media/documentdb-python-application/image21.png)

数秒すると、Web アプリケーションの発行が完了し、ブラウザーが起動されます。作成したアプリケーションが Azure で実行されているようすが確認できます。


## 次のステップ

ご利用ありがとうございます。ここでは初めての方を対象に、Azure DocumentDB を使用して Python アプリケーションを作成し、Azure Websites に発行する方法を説明しました。


  [Flask のチュートリアルにアクセスするには、ここをクリックしてください]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [こちら]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft Web プラットフォーム インストーラー]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure の管理ポータル]: http://portal.azure.com
<!--HONumber=47-->
