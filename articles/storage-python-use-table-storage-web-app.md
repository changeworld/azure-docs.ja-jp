<properties urlDisplayName="Web App with Blob Storage" pageTitle="テーブル ストレージを使用した Python Web アプリケーション | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="Azure クライアント ライブラリを使用して Python Web アプリケーションを作成する方法について説明したチュートリアルです。Django を Web フレームワークとして使用します。" metaCanonical="" services="storage" documentationCenter="Python" title="テーブル ストレージを使用する Python Web アプリケーション" authors="huvalo" solutions="" videoId="" scriptId="" manager="wpickett" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# テーブル ストレージを使用する Python Web アプリケーション

このチュートリアルでは、Python 用 Azure クライアント ライブラリに基づいて、テーブル ストレージを使用するアプリケーションの作成方法について説明します。このアプリケーションが最初の Python Azure アプリケーションである場合は、「[Django Hello World Web アプリケーション][Django Hello World Web アプリケーション]」を先に参照することをお勧めします。

このガイドでは、Azure に展開できる Web ベースのタスク一覧アプリケーションを作成します。このタスク一覧では、ユーザーがタスクの取得、新しいタスクの追加、タスクの完了済みのマーク付けを実行できます。また、Web フレームワークとして Django を使用します。

タスク項目は Azure ストレージに格納されます。Azure ストレージは、フォールト トレランスと可用性に優れた非構造化データ ストレージです。Azure Storage には、データを格納してアクセスできるデータ構造がいくつか用意されています。Azure SDK for Python に含まれる API または REST API を通じて、そのストレージ サービスを活用できます。
詳細については、「[Azure のデータの格納とアクセス][Azure のデータの格納とアクセス]」を参照してください。

学習内容:

-   Azure テーブル ストレージ サービスを使用する方法

完成したアプリケーションのスクリーンショットは次のようになります (追加されるタスク項目は異なる場合があります)。

![][0]

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span id="setup"></span> </a>開発環境の設定

**注:** Python またはクライアント ライブラリをインストールする場合は、[Python インストール ガイド][Python インストール ガイド]を参照してください。

*Windows に関するメモ*: Windows WebPI インストーラーを使用した場合、Django とクライアント ライブラリが既にインストールされています。

## Azure でのストレージ アカウントの作成

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## Django プロジェクトの作成

次に、アプリケーションを作成するための手順を示します。

-   "TableserviceSample" という名前の既定の Django プロジェクトを作成します。
-   コマンド ラインで、コードの保存先となるディレクトリに移動し、次のコマンドを実行します。

        django-admin.py startproject TableserviceSample

-   新しい Python ファイル **views.py** をプロジェクトに追加します。
-   必要な Django サポートをインポートするために、次のコードを **views.py** に追加します。

        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   **TableserviceSample/TableserviceSample** フォルダーの下に **templates** という名前の新しいフォルダーを作成します。
-   テンプレートを見つけることができるように、アプリケーション設定を変更します。**settings.py** を開き、次のエントリを INSTALLED\_APPS に追加します。

        'TableserviceSample',

-   新しい Django テンプレート ファイル **mytasks.html** を **templates** フォルダーに追加し、次のコードをそのファイルに追加します。

<!-- -->

    <html>
    <head><title></title></head>
    <body>
    <h2>My Tasks</h2> <br>
    <table border="1"> 
    <tr>
    <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
    {% for entity in entities %}
    <form action="update_task" method="GET">
    <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
    <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
    <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
    <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>

    <td><input type="submit" value="Complete"></td>
    </tr>
    </form>
    {% endfor %}
    </table>
    <br>
    <hr>
    <table border="1">
    <form action="add_task" method="GET">
    <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
    <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
    <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
    <tr><td><input type="submit" value="add task"></input></td></tr>
    </form>
    </table>
    </body>
    </html>    

## Windows Azure ストレージ モジュールのインポート

次のコードを、**views.py** の先頭部分で Django のインポートの直後に追加します。

    from azure.storage import TableService

## ストレージ アカウント名とアカウント キーの取得

次のコードを、**views.py** にある Windows Azure スレージ モジュールのインポートの直後に追加し、"youraccount" と "yourkey" の部分を実際のアカウント名とアカウント キーに置き換えます。これで、アカウント名とアカウント キーを Azure 管理ポータルから取得できます。

    account_name = 'youraccount'
    account_key = 'yourkey'

## TableService の作成

次のコードを **account\_name** の後に追加します。

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

## タスクの一覧表示

関数 list\_tasks を **views.py** に追加します。

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## タスクの追加

関数 add\_task を **views.py** に追加します。

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## タスク状態の更新

関数 update\_task を **views.py** に追加します。

    def update_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        partition_key = name + category
        row_key = date
        table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name': name, 'category':category, 'date':date, 'complete':'Yes'})
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## URL のマッピング

ここで、Django アプリケーションで URL をマップする必要があります。**urls.py** を開き、次のマッピングを urlpatterns に追加します。

    url(r'^$', 'TableserviceSample.views.list_tasks'),
    url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
    url(r'^add_task$', 'TableserviceSample.views.add_task'),
    url(r'^update_task$', 'TableserviceSample.views.update_task'),

## アプリケーションの実行

-   **TableserviceSample** ディレクトリに移動し (現在のディレクトリがこのディレクトリではない場合)、次のコマンドを実行します。

    python manage.py runserver

-   ブラウザーで `http://127.0.0.1:8000/` を参照します。8000 は実際のポート番号に置き換えてください。

これで、**[Add Task]** をクリックするとタスクが 1 つ作成されます。**[Complete]** ボタンをクリックするとタスクが更新され、状態が [Yes] に設定されます。

## コンピューティング エミュレーターでのアプリケーションの実行と、アプリケーションの発行、停止、削除

これで、組み込みの Django サーバーでアプリケーションが適切に実行されました。このアプリケーションを Azure エミュレーターに展開し (Windows のみ)、その後 Azure に発行することで、さらに詳細なテストを行うことができます。これを実行する方法の一般的な手順については、「[Django Hello World Web アプリケーション][Django Hello World Web アプリケーション]」の記事を参照してください。この記事では手順が詳細に説明されています。

## <span id="NextSteps"></span></a> 次のステップ

これで、Azure テーブル ストレージ サービスの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス][Azure のデータの格納とアクセス]
-   Azure のストレージ チーム ブログ: <http://blogs.msdn.com/b/windowsazurestorage/>

  [Django Hello World Web アプリケーション]: http://windowsazure.com/ja-jp/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [0]: ./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png
  [Python インストール ガイド]: http://windowsazure.com/ja-jp/documentation/articles/python-how-to-install
