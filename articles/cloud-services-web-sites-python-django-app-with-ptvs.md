<properties linkid="develop-python-tutorials-django-with-python-tools-for-visual-studio" urlDisplayName="Django with Python Tools for Visual Studio 2.0" pageTitle="Creating Django applications with Python Tools for Visual Studio 2.0" metaKeywords="" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL Database or MySQL database instance and can be deployed to a website or cloud service." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools 2.0 for Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Python Tools 2.0 for Visual Studio を使用して Django アプリケーションを作成する

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Python Tools 2.0 for Visual Studio を使用して簡単な Django アプリケーションを作成します。ここで作成するのは、アンケート調査に投票してもらうためのアプリケーションです。まず、sqlite3 データベースをローカルに作成した後、Azure 上の SQL Server または MySQL データベースへ移行します。Django 管理インターフェイスを有効にし、このインターフェイスを使ってデータベースにアンケート項目を追加する方法を紹介します。また、Visual Studio に組み込まれている Djangoシェルも使用します。最後に、作成したアプリケーションを Azure Websites と Azure Cloud Services にデプロイします。</p>
<p>右側のクリップを見ると、このチュートリアルと同じ手順をビデオで確認できます。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">33:08</span></div>

</div>

このチュートリアルでは、Python Tools for Visual Studio と Azure を使用する場合の手順を説明します。ここで作成する投票アプリケーションおよび Django の詳細については、[][]<https://www.djangoproject.com/></a> をご覧ください。

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## 要件

このチュートリアルを実行するには、次のコンポーネントが必要です。

-   [Python Tools 2.0 for Visual Studio][Python Tools 2.0 for Visual Studio]
-   [Python 2.7 (32 ビット)][Python 2.7 (32 ビット)]
-   Visual Studio および Azure SDK:
  -   VS 2010 Pro 以上と Azure SDK 2.1
  -   VS 2012 Pro 以上と Azure SDK 2.1、2.2 またはそれ以降
  -   VS 2013 Pro 以上と Azure SDK 2.2 以降
  -   無料の VS 2013 統合シェル。**Azure SDK では、統合シェルをサポートしていません**。Django アプリケーションは、無料の統合シェルを使用してローカルで開発、デバッグ、および実行できますが、Visual Studio を使用して Azure Websites または Cloud Services に発行することはできません。

Web Platform Installer を使用して Azure SDK をインストールします。これにより、SDK、エミュレーター、および Azure Tools for Visual Studio がインストールされます。Web Platform Installer で、**Azure SDK for .NET** を探し、お使いの Visual Studio のバージョンでサポートされている SDK のバージョンを 1 つ選択します。

**注:** Azure Websites または Cloud Services を使用してこのアプリケーションを正常に実行するには、[python.org][Python 2.7 (32 ビット)] から入手した公式な CPython 2.7 ディストリビューションを使用する必要があります。他のディストリビューションは動作する場合がありますが、公式にサポートされていません。

## 既存のプロジェクトをダウンロードする

このチュートリアルで先にスキップするには、[このプロジェクトのソース コードをダウンロードする][このプロジェクトのソース コードをダウンロードする]ことができます。

sqlite3 データベースが既に作成されていて、次のスーパー ユーザー資格情報が設定されています。

    Username: tutorial
    Password: azure

このダウンロードには、仮想環境は含まれていません。「[仮想環境を作成する][仮想環境を作成する]」セクションの手順に従って、仮想環境を作成する必要があります。仮想環境を作成した時点で、「[デバッグ][デバッグ]」セクションに進むためのプロジェクトの準備ができます。

## プロジェクトを作成する

Python Tools for Visual Studio は Python 仮想環境をサポートしています。ここでは django プロジェクトを作成し、仮想環境を使用して依存モジュールをインストールします。Azure Websites または Cloud Services に発行するプロジェクトを設定するときは、この方法をお勧めします。

1.  Visual Studio を開き、[ファイル]、[新しいプロジェクト]、[Django アプリケーション] の順に選択して、「**tutorial**」という名前を付けます。

    ![[新しいプロジェクト]](./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png)

**注:** ソリューション エクスプローラーで、[参照設定] の下に Django 1.4 のノードが表示されます。これは Azure Cloud Services のデプロイメントで、ターゲット コンピューターに Python と Django をインストールするときに使用されます。Django 1.4 への参照を [参照設定] ノードから削除しないでください。ここでは、仮想環境を使って Django パッケージをインストールするので、仮想環境にインストールした Django パッケージが使用されます。

## <a name="creating-a-virtual-environment"></a>仮想環境を作成する

ここでは仮想環境を使用して依存モジュールをインストールします。これは、Python のアプリケーションで推奨される方法であり、Azure に対して発行する場合は必須です。

1.  新しい仮想環境を作成します。ソリューション エクスプローラーで **[Python 環境]** を右クリックし、**[仮想環境の追加]** を選択します。

    ![仮想環境の追加][仮想環境の追加]

2.  基になる Python インタープリターとして Python 2.7 を選択し、既定の名前 (**env**) をそのまま使用します。pip または virtualenv がまだインストールされていない場合は、PTVS によってこれらがインストールされます。

3.  **[env]** を右クリックし、**[Python パッケージのインストール]** を選択します。**django**

    ![Django のインストール][Django のインストール]

4.  Django はファイル数が多いので、インストールに少し時間がかかります。その間、進捗状況が出力ウィンドウに表示されます。

    ![Django のインストールに関する出力][Django のインストールに関する出力]

    **注:** まれに、出力ウィンドウにエラーが表示されることがあります。その場合は、エラーがクリーンアップに関連しているかどうか確認してください。クリーンアップには失敗しても、インストールは正常に行われる場合があります（出力ウィンドウを上方向にスクロールして確認してください)。これは、新しく作成した一時ファイルやフォルダーが PTVS によってロックされ、pip クリーンアップ処理で削除できなくなるためです。

5.  **[env]** を右クリックし、**[Python パッケージのインストール]** を選択します。**pytz** をインストールします (必須ではありませんが、このパッケージをインストールすることをお勧めします。django でタイムゾーンをサポートするときに使用されます)。

## 仮想環境の確認

1.  すべてが正しくインストールされているかどうかを確認してみましょう。**F5** キーまたは **Ctrl + F5** キーを押して、Web サイトを開きます。django 開発用サーバーが起動し、Web ブラウザが表示されます。次のページが表示されます。

    ![Django を示す Web ブラウザー][Django を示す Web ブラウザー]

## 投票アプリケーションを作成する

このセクションでは、アンケートへの投票を処理するアプリケーションを追加します。

Django プロジェクトには複数のアプリケーションを追加できます。このチュートリアルで作成したプロジェクトの名前は「tutorial」で、Visual Studio プロジェクトと同じです。これから追加するアプリケーションには **polls** という名前を付けます。このアプリケーションのフォルダーはプロジェクト ノードの下に作成されます。

1.  該当する**プロジェクト ノード**、**[追加]**、**[Django アプリケーション]** の順に選択し、「**polls**」という名前を付けます。このアプリケーション用のフォルダーが作成されます。ここには、頻繁に使用するアプリケーション ファイルの定型コードが保存されます。

    ![Django アプリケーションの追加][Django アプリケーションの追加]

2.  **tutorial/settings.py** で、**INSTALLED\_APPS** に次のエントリを追加します。

        'polls',

3.  **INSTALLED\_APPS** で以下をコメント解除します。

        'django.contrib.admin',

4.  **tutorial/urls.py** を次のコードに書き換えます。

        from django.conf.urls import patterns, include, url

        from django.contrib import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            url(r'^', include('polls.urls', namespace="polls")),
            url(r'^admin/', include(admin.site.urls)),
        )

5.  **polls/models.py** を次のコードに書き換えます。

        from django.db import models

        class Poll(models.Model):
            question = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')

            def __unicode__(self):
                return self.question

        class Choice(models.Model):
            poll = models.ForeignKey(Poll)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)

            def __unicode__(self):
                return self.choice_text

6.  **polls/views.py** を次のコードに書き換えます。

        from django.shortcuts import get_object_or_404, render
        from django.http import HttpResponseRedirect
        from django.core.urlresolvers import reverse
        from polls.models import Choice, Poll

        def vote(request, poll_id):
            p = get_object_or_404(Poll, pk=poll_id)
            try:
                selected_choice = p.choice_set.get(pk=request.POST['choice'])
            except (KeyError, Choice.DoesNotExist):
                # Redisplay the poll voting form.
                return render(request, 'polls/detail.html', {
                    'poll': p,
                    'error_message': "You didn't select a choice.",
                })
            else:
                selected_choice.votes += 1
                selected_choice.save()
                # Always return an HttpResponseRedirect after successfully dealing
                # with POST data. This prevents data from being posted twice if a
                # user hits the Back button.
                return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))

7.  次のコードを含む新しい Python ファイル **polls/urls.py** を追加します。

        from django.conf.urls import patterns, url
        from django.views.generic import DetailView, ListView
        from polls.models import Poll

        urlpatterns = patterns('',
            url(r'^$',
                ListView.as_view(
                    queryset=Poll.objects.order_by('-pub_date')[:5],
                    context_object_name='latest_poll_list',
                    template_name='polls/index.html'),
                name='index'),
            url(r'^(?P<pk>\d+)/$',
                DetailView.as_view(
                    model=Poll,
                    template_name='polls/detail.html'),
                name='detail'),
            url(r'^(?P<pk>\d+)/results/$',
                DetailView.as_view(
                    model=Poll,
                    template_name='polls/results.html'),
                name='results'),
            url(r'^(?P<poll_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
        )

8.  次のコードを含む新しい項目 **polls/admin.py** を作成します。

        from django.contrib import admin
        from polls.models import Choice, Poll

        class ChoiceInline(admin.TabularInline):
            model = Choice
            extra = 3

        class PollAdmin(admin.ModelAdmin):
            fieldsets = [
                (None,               {'fields': ['question']}),
                ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
            ]
            inlines = [ChoiceInline]
            list_display = ('question', 'pub_date')
            list_filter = ['pub_date']
            search_fields = ['question']
            date_hierarchy = 'pub_date'

        admin.site.register(Poll, PollAdmin)

9.  **polls/templates** フォルダーの下に、**polls** という名前の新しいフォルダーを作成します。

10. ドラッグ アンド ドロップ操作または切り取りと貼り付けを使用して、**polls/templates/index.html** ファイルを **polls/templates/polls** フォルダーへ移動します。

11. **polls/templates/polls/index.html** を次のマークアップに書き換えます。

        <html>
        <head></head>
        <body>
        {% if latest_poll_list %}
            <ul>
            {% for poll in latest_poll_list %}
                <li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
            {% endfor %}
            </ul>
        {% else %}
            <p>No polls are available.</p>
        {% endif %}
        </body>
        </html>

12. 次のような新しい Django HTML テンプレート **polls/templates/polls/detail.html** を作成します。

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
        <form action="{% url 'polls:vote' poll.id %}" method="post">
        {% csrf_token %}
        {% for choice in poll.choice_set.all %}
            <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
            <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
        {% endfor %}
        <input type="submit" value="Vote" />
        </form>
        </body>
        </html>

13. 次のような新しい Django HTML テンプレート **polls/templates/polls/results.html** を作成します。

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        <ul>
        {% for choice in poll.choice_set.all %}
            <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
        {% endfor %}
        </ul>
        <a href="{% url 'polls:detail' poll.id %}">Vote again?</a>
        </body>
        </html>

14. このプロジェクトのファイル構成は次のようになります。

    ![Solution Explorer][Solution Explorer]

## sqlite3 データベースをローカルに作成する

これで、新しい Web アプリケーションの準備が整いました。ただし、使用する前にデータベースを構成する必要があります。Web サイトをローカルでテストするため、sqlite3 データベースを作成しましょう。非常に軽量のデータベースなので、他に何もインストールする必要はありません。データベース ファイルはプロジェクト フォルダー内に作成します。

1.  **tutorial/settings.py** で、次の import をファイルの先頭に追加します。

        from os import path

2.  同じファイルの最上部、import の後に、次の定義を追加します。

        PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

3.  DATABASES セクションを次のコードに書き換えます。

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.sqlite3',
                'NAME': path.join(PROJECT_ROOT, 'db.sqlite3'),
                'USER': '',
                'PASSWORD': '',
                'HOST': '',
                'PORT': '',
            }
        }

4.  プロジェクト ノードを右クリックして、**[Django]**、**[Django Sync DB]** の順に選択します。対話型の Django 管理ウィンドウが表示されます。データベースがまだ存在していないので、管理者の資格情報を作成するよう求められます。ユーザー名とパスワードを入力します。メール アドレスは省略可能です。

    ![Django 同期データベース][Django 同期データベース]

5.  F5 キーまたは Ctrl + F5 キーを押して、Web サイトを開きます。django 開発用サーバーが起動し、Web ブラウザが表示されます。Web サイトのルート URL にアンケート項目が一覧表示されます。ただし、データベースにはまだ何もありません。

    ![Web ブラウザー][Web ブラウザー]

6.  **http://localhost:{port}/admin** へ移動します。ポート番号は、開発用サーバーのコンソール ウィンドウから取得できます。前の手順で作成した資格情報を使用して、ログインします。

    ![アンケート項目の追加][アンケート項目の追加]

7.  管理インターフェイスを使用して、アンケート項目を 1 ～ 2 件追加します。ローカル データベースにアンケート項目を追加する作業にあまり時間をかけないでください。この後、クラウド データベースに切り替えますが、その時点でもう一度データベースにアンケート項目を追加します。

    ![アンケート項目][アンケート項目]

8.  **http://localhost:{port}/** へ移動します。追加したアンケート項目が一覧表示されます。

    ![][]

9.  いずれかのアンケート項目をクリックして、投票ページへ進みます。

    ![アンケート項目の詳細][アンケート項目の詳細]

10. どちらかに投票すると、結果ページが表示されます。投票数が "1" 増えていることがわかります。

    ![アンケート項目の結果][アンケート項目の結果]

## スタイルシートとその他の静的ファイルを使用する

このセクションでは、スタイルシートを使ってサイトの外観を変更します。スタイルシートなどの静的ファイルはそれぞれ扱いが異なるので、適切な場所に保存することが重要です。

1.  **tutorial/settings.py** で、**STATIC\_ROOT** の代入を見つけ、次のように変更します。

        STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\','/')

2.  **polls** フォルダーの下に、**static** という名前の新しいフォルダーを作成します。

3.  **polls/static** フォルダーの下に、**polls** という名前の新しいフォルダーを作成します。

4.  **polls/static/polls** フォルダーの下に、**images** という名前の新しいフォルダーを作成します。

5.  次のコードを含む新しいスタイル シート ファイル **polls/static/polls/style.css** を追加します。

        body {
            color: darkblue;
            background: white url("images/background.jpg");
        }

6.  **polls/static/polls/images** フォルダーに既存の画像を追加し、**background.jpg** という名前を付けます。

7.  このプロジェクトのファイル構成は次のようになります。

    ![Solution Explorer][1]

8.  すべてのテンプレートのヘッダーを次のように編集し、スタイルシートを参照するように指定します。

        <head>
        {% load staticfiles %}
        <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
        </head>

9.  Web サイトをもう一度実行します。作成したスタイルシート (青色のテキストと背景画像) がインデックス ページ、投票ページ、結果ページに反映されます。

    ![アンケート項目の詳細][2]

## <a name="debugging"></a>デバッグ

Python Tools for Visual Studio には、Django テンプレートをデバッグするための特殊な機能が備わっています。

1.  **polls/templates/polls/index.html** を開き、**F9** キーを使って次の行にブレークポイントを設定します。

        {% if latest_poll_list %}

2.  **F5** キーを押してデバッグを開始します。テンプレート ファイル内に設定したブレークポイントで Visual Studio が一時停止します。

3.  **\[デバッグ]、[ウィンドウ]、[ローカル]** の順に選択します。**[ローカル]** ウィンドウが開き、**latest\_poll\_list** 変数とその値が表示されます。

4.  **F10** キーを押すと、通常の Python コードと同じようにステップ実行されます。for ループ内では **poll** の値を確認できます。

    ![デバッグ][3]

## Azure でデータベースを作成する

投票アプリケーションがローカルで正常に動作することがわかったので、Azure 上でホストされるデータベースへ切り替えましょう。

この後の 2 つのセクションでは、SQL データベースと MySQL データベースの使用方法を説明します。これらはいずれもホステッド サービスです。

これとは別に、仮想マシンを作成してデータベース サーバーをインストールする方法もあります。Azure Linux VM 上に MySQL を設定する方法は、[こちら][こちら]の説明を参照してください。

**注:** Azure 上の sqlite3 データベースを使用できます (開発目的のみ。運用環境では使用しないでください)。このデータベースをデプロイして django アプリケーションで使用するには、プロジェクトに **db.sqlite3** ファイルを追加する必要があります。

### SQL Database

このセクションでは、Azure 上に SQL データベースを作成し、必要なパッケージを仮想環境に追加した上で、新しいデータベースを使用できるように設定を変更します。

1.  WindowsAzure 管理ポータルで **[SQL データベース]** を選択します。

2.  最初に、このデータベースをホストするサーバーを作成します。**[サーバー]** を選択し、**[追加]** をクリックします。

3.  新しく作成したサーバーの **[構成]** タブに、現在のクライアント IP アドレスが表示されます。その隣に表示される **[使用できる IP アドレスに追加します]** をクリックします。

    **注:** Azure がクライアント IP アドレスを正しく検出しない場合もあります。データベースの同期時にエラーが表示された場合は、エラー メッセージの IP アドレスをコピーし、許可された IP アドレスに追加してください。

4.  次に、データベースを作成します。**[データベース]** タブの下部バーにある **[追加]** をクリックします。

5.  Visual Studio で、Django から SQL Server データベースへアクセスするためのパッケージを仮想環境にインストールします。

6.  **[env]** を右クリックし、**[Python パッケージのインストール]** を選択します。**easy\_install** を使用して **pyodbc** をインストールします。

7.  **[env]** を右クリックし、**[Python パッケージのインストール]** を選択します。**pip** を使用して **django-pyodbc-azure** をインストールします。

8.  **tutorial/settings.py** を編集し、**DATABASES** 定義を次のように変更します。**NAME**、**USER**、**PASSWORD**、**HOST** を、それぞれ ClearDB コントロール パネルに表示される値に書き換えてください。

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<database name>',
                'USER': '<user name>@<server name>',
                'PASSWORD': '<user password>',
                'HOST': '<server name>.database.windows.net',
                'PORT': '',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': True,
                },
            }
        }

    コンピューターにインストールしているドライバーが使用されるように設定する必要があります。スタート メニューまたはスタート画面から **[管理ツール]** を開き、**[ODBC データ ソース (32 ビット)]** を選択します。**[ドライバー]** タブにドライバーが一覧表示されます。

    Azure Websites で実行する場合、**SQL Server Native Client 10.0** と **SQL Server Native Client 11.0** を両方使用できます。

    Azure Cloud Services で実行する場合、**SQL Server Native Client 11.0** のみ使用できます。

9.  データベースを同期し、ローカル sqlite3 データベースの場合と同じように管理者の資格情報を作成します。

### MySQL データベース

このセクションでは、Azure 上に MySQL データベースを作成し、必要なパッケージを仮想環境に追加した上で、新しいデータベースを使用できるように設定を変更します。

Azure ストアでは、MySQL データベースなどさまざまなサービスをアカウントに追加できます。試用アカウントを無料で作成でき、データベースも小規模 (特定のサイズまで) であれば無料です。

1.  Azure 管理ポータルで、**[新規]**、**[ストア]**、**[アプリ サービス]**、**[ClearDB MySQL データベース]** の順に選択します。無料プランでデータベースを作成します。

2.  次に、Django から MySQL データベースにアクセスするためのパッケージを仮想環境にインストールします。**[env]** を右クリックし、**[Python パッケージのインストール]** を選択します。**easy\_install** を使用して **mysql-python** をインストールします。

3.  **tutorial/settings.py** を編集し、**DATABASES** 定義を次のように変更します。**NAME**、**USER**、**PASSWORD**、**HOST** を、それぞれ ClearDB コントロール パネルに表示される値に書き換えてください。

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<database name>',
                'USER': '<user name>',
                'PASSWORD': '<user password>',
                'HOST': '<host url>',
                'PORT': '',
            }
        }

4.  データベースを同期し、ローカル sqlite3 データベースの場合と同じように管理者の資格情報を作成します。

## Django シェルを使用する

1.  プロジェクト ノードを右クリックして、**[Django]**、**[Django シェルを開く]** の順に選択します。

2.  この対話型ウィンドウでは、モデルを使用してデータベースにアクセスできます。次のコードを入力して、データベースにアンケート項目を追加します。

        from polls.models import Poll, Choice
        from django.utils import timezone

        p = Poll(question="Favorite way to host Django on Azure?", pub_date=timezone.now())
        p.save()

        p.choice_set.create(choice_text='Web Site', votes=0)
        p.choice_set.create(choice_text='Cloud Service', votes=0)
        p.choice_set.create(choice_text='Virtual Machine', votes=0)

    ![Django シェルでのアンケート項目の追加][Django シェルでのアンケート項目の追加]

3.  モデルの静的解析では、完全な API の一部のみが表示されます。対話型ウィンドウではライブ オブジェクトに対して IntelliSense が適用されるので、API の確認に役立ちます。対話型ウィンドウでの例を示します。

        # all poll objects
        Poll.objects.all()

        # primary key for poll
        p.id

        # all choice objects for the poll
        p.choice_set.all()

        # get object by primary key
        Poll.objects.get(pk=1)

    ![Django シェルでのクエリによるアンケート項目][Django シェルでのクエリによるアンケート項目]

4.  Web サイトを開きます。Django シェルを使って追加したアンケート項目が表示されます。

## Azure に発行する

Azure 上にデータベースを作成したところで、次は Web サイト自体を Azure でホストします。

Azure には、Django アプリケーションをホストするための方法がいくつか用意されています。

-   [Web サイト][Web サイト]
-   [クラウド サービス][クラウド サービス]
-   [仮想マシン][仮想マシン]

Python Tools for Visual Studio には、Azure Websites と Cloud Services へ発行するための機能が備わっています。次の 2 つのセクションではこれらの発行方法を説明します。必要に応じて、どちらか一方または両方を選択してください。

いずれの場合も、IIS が自動的に構成され、プロジェクトにまだ web.config ファイルが存在しない場合は自動的に生成されます。settings.py で STATIC\_ROOT を設定していれば、静的ファイルが自動的に収集されます (manage.py collectstatic)。

仮想マシンで Django をホストする方法は、このチュートリアルでは詳しく説明しません。適切なオペレーティング システム (Windows または Linux) で VM を作成し、Python をインストールした後、Django アプリケーションを手動でデプロイします。

### Azure Websites

1.  まず、Web サイトを作成する必要があります。Azure 管理ポータルで、**[新規]**、**[コンピューティング]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。適切な名前を選択します。

2.  Web サイトを作成したら、その Web サイトの発行プロファイルをダウンロードします。

    ![Web サイトのプロファイルのダウンロード][Web サイトのプロファイルのダウンロード]

3.  Visual Studio で、該当するプロジェクト ノードを右クリックして **[発行]** を選択します。

    ![Web サイトを発行する][Web サイトを発行する]

4.  前にダウンロードした Web サイト発行プロファイル ファイルをインポートします。

5.  既定値をそのまま使用し、**[発行]** をクリックして発行処理を開始します。

6.  発行が完了すると Web ブラウザーが開き、発行した Web サイトが表示されます。

    ![Web サイト ブラウザー][Web サイト ブラウザー]

### Azure Cloud Services

1.  プロジェクト ノードを右クリックして、**[Azure クラウド サービス プロジェクトの追加]** を選択するか、**[変換]、[Azure クラウド サービス プロジェクトへの変換]** の順に選択します (お使いの Visual Studio のバージョンに応じてどちらかが表示されます)。新しいプロジェクト (拡張子 .Azure) がソリューションに追加されます。この新しいプロジェクトは、ソリューションのスタートアップ プロジェクトとなります。

    **注:** プロジェクトのコンテキスト メニューに Azure クラウド サービス プロジェクトを作成するコマンドが表示されない場合は、Azure Tools for Visual Studio をインストールする必要があります。これは、Azure SDK for .NET の一部としてインストールされます。このチュートリアルの冒頭にある要件セクションを参照してください。

    ![Solution Explorer][4]

#### エミュレーターで実行する

1.  コンピューティング エミュレーターで実行するには、**管理者として Visual Studio を再起動**する必要があります。

2.  **F5** キーを押してデバッグを開始します。アプリケーションが実行され、コンピューティング エミュレーターにデプロイされます。管理インターフェイスが正常に機能し、アンケートに投票できることを確認します。

    ![コンピューティング エミュレーター][コンピューティング エミュレーター]

3.  管理者としての操作を続行しない場合は、ここで Visual Studio を再起動します。

#### Azure Cloud Services への発行

1.  次に、作成したクラウド サービスを Azure に発行します。**tutorial.Azure** クラウド サービス プロジェクトを右クリックし、**[発行]** を選択します。

    **注:** 必ず該当するクラウド サービス プロジェクトで **[発行]** を選択してください。Azure Cloud Services への発行に使用する **[Azure アプリケーションの発行]** ダイアログ ボックスが表示されます。Django プロジェクトで **[発行]** を選択すると、Azure Websites への発行に使用する **[Web の発行]** ダイアログ ボックスが表示されます。

    ![クラウド サービスの発行][クラウド サービスの発行]

2.  Azure サブスクリプション ファイルをインポートする必要があります。[[サインインして資格情報をダウンロードする]][] リンクをクリックして、Azure ポータルからファイルをダウンロードします。

3.  [設定] ページで、[クラウド サービス] ボックスの一覧の **[新規作成]** を選択して、新しいクラウド サービスを作成します。このクラウド サービスには自由に名前を付けることができます。

    ![クラウド サービスの設定][クラウド サービスの設定]

4.  既定値をそのまま使用し、**[発行]** をクリックします。Cloud Services の VM をプロビジョニングする必要があるため、この処理は Websites に発行する場合より長い時間がかかります。その間、進捗状況が [Azure のアクティビティ ログ] ウィンドウに表示されます。

    ![クラウド サービスの発行][5]

5.  処理が完了したら、[Azure のアクティビティ ログ] ウィンドウで Web サイトの URL をクリックして Web ブラウザーを開きます。

    ![クラウド サービス ブラウザー][クラウド サービス ブラウザー]

## まとめ

このチュートリアルでは、[Python Tools for Visual Studio][Python Tools 2.0 for Visual Studio] を使って Django アプリケーションを作成しました。ここで使用したデータベースは、sqlite3、SQL Server、MySQL の 3 種類です。最後に、Azure Websites と Cloud Services にこのアプリケーションを発行しました。

  [チュートリアルを見る]: http://www.youtube.com/watch?v=wkqjafvvU5w
  []: https://www.djangoproject.com/
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Python Tools 2.0 for Visual Studio]: http://pytools.codeplex.com
  [Python 2.7 (32 ビット)]: http://www.python.org/download/
  [このプロジェクトのソース コードをダウンロードする]: http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376
  [仮想環境を作成する]: #creating-a-virtual-environment
  [デバッグ]: #debugging

  [仮想環境の追加]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png
  [Django のインストール]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png
  [Django のインストールに関する出力]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png
  [Django を示す Web ブラウザー]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png
  [Django アプリケーションの追加]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png
  [Solution Explorer]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png
  [Django 同期データベース]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png
  [Web ブラウザー]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png
  [アンケート項目の追加]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png
  [アンケート項目]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png
  []: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png
  [アンケート項目の詳細]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png
  [アンケート項目の結果]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png
  [1]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png
  [2]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png
  [3]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png
  [こちら]: http://www.windowsazure.com/ja-jp/manage/linux/common-tasks/mysql-on-a-linux-vm/
  [Django シェルでのアンケート項目の追加]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png
  [Django シェルでのクエリによるアンケート項目]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png
  [Web サイト]: http://www.windowsazure.com/ja-jp/services/web-sites/
  [クラウド サービス]: http://www.windowsazure.com/ja-jp/services/cloud-services/
  [仮想マシン]: http://www.windowsazure.com/ja-jp/services/virtual-machines/
  [Web サイトのプロファイルのダウンロード]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png
  [Web サイトを発行する]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png
  [Web サイト ブラウザー]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png
  [4]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png
  [コンピューティング エミュレーター]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png
  [クラウド サービスの発行]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png

  [クラウド サービスの設定]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png
  [5]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png
  [クラウド サービス ブラウザー]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png
