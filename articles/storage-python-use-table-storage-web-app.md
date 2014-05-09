<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="BLOB ストレージを使用する Web アプリケーション" pageTitle="テーブル ストレージを使用する Python Web アプリケーション | Microsoft Azure" metaKeywords="Azure テーブル ストレージ Python, Azure Python アプリケーション, Azure Python チュートリアル, Azure Python 例" description="Azure クライアント ライブラリを使用して Python Web アプリケーションを作成する方法について説明したチュートリアルです。Django を Web フレームワークとして使用します。" metaCanonical="" services="storage" documentationCenter="Python" title="テーブル ストレージを使用する Python Web アプリケーション" authors="" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />





# テーブル ストレージを使用する Python Web アプリケーション

このチュートリアルでは、Python 用 Azure クライアント ライブラリに基づいて、テーブル ストレージを使用するアプリケーションの作成方法について説明します。このアプリケーションが最初の Python Azure アプリケーションである場合は、「[Django Hello World Web アプリケーション][]」を先に参照することをお勧めします。

このガイドでは、Azure に展開できる Web ベースのタスク一覧アプリケーションを作成します。このタスク一覧では、ユーザーがタスクの取得、新しいタスクの追加、タスクの完了済みのマーク付けを実行できます。また、Web フレームワークとして Django を使用します。

タスク項目は Azure ストレージに格納されます。Azure ストレージは、フォールト トレランスと可用性に優れた非構造化データ ストレージです。Azure ストレージには、データを格納してアクセスできるデータ構造がいくつか用意されています。Azure SDK for Python に含まれる API または REST API を通じて、そのストレージ サービスを
活用できます。詳細については、「[Azure のデータの格納とアクセス]」を参照してください。

学習内容:

-   Azure テーブル ストレージ サービスを使用する方法

完成したアプリケーションのスクリーンショットは次のようになります (追加されるタスク項目は異なる場合があります)。

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

##<a id="setup"> </a>開発環境の設定

**注:** Python またはクライアント ライブラリをインストールする必要がある場合は、「[Python Installation Guide (Python インストール ガイド)](http://windowsazure.com/ja-jp/documentation/articles/python-how-to-install)」を参照してください。



*Windows に関する注*: WebPI インストーラーを使用した場合は、Django とクライアント ライブラリが既にインストールされています。

## Azure でのストレージ アカウントの作成

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## Django プロジェクトの作成

次に、アプリケーションを作成するための手順を示します。

-   "TableserviceSample" という名前の既定の Django プロジェクトを作成します。
- 	コマンド ラインで、コードの保存先となるディレクトリに移動し、次のコマンドを実行します。

		django-admin.py startproject TableserviceSample

-   新しい Python ファイル **views.py** をプロジェクトに追加します。
-   必要な Django サポートをインポートするために、次のコードを **views.py** に追加します。
           
        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   **TableserviceSample/TableserviceSample** フォルダーの下に **templates** という名前の新しいフォルダーを作成します。
-   テンプレートを見つけることができるように、アプリケーション設定を変更します。**settings.py** を開き、次のエントリを INSTALLED_APPS に追加します。

        'TableserviceSample',

-   新しい Django テンプレート ファイル **mytasks.html** を **templates** フォルダーに追加し、次のコードをそのファイルに追加します。
 
<pre>
	&lt;html&gt;
	&lt;head&gt;&lt;title&gt;&lt;/title&gt;&lt;/head&gt;
	&lt;body&gt;
	&lt;h2&gt;My Tasks&lt;/h2&gt; &lt;br&gt;
	&lt;table border="1"&gt; 
	&lt;tr&gt;
	&lt;td&gt;Name&lt;/td&gt;&lt;td&gt;Category&lt;/td&gt;&lt;td&gt;Date&lt;/td&gt;&lt;td&gt;Complete&lt;/td&gt;&lt;td&gt;Action&lt;/td&gt;&lt;/tr&gt;
	{% for entity in entities %}
	&lt;form action=&quot;update_task&quot; method=&quot;GET&quot;&gt;
	&lt;tr&gt;&lt;td&gt;{{entity.name}} &lt;input type=&quot;hidden&quot; name='name' value=&quot;{{entity.name}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.category}} &lt;input type=&quot;hidden&quot; name='category' value=&quot;{{entity.category}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.date}} &lt;input type=&quot;hidden&quot; name='date' value=&quot;{{entity.date}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.complete}} &lt;input type=&quot;hidden&quot; name='complete' value=&quot;{{entity.complete}}&quot;&gt;&lt;/td&gt;

	&lt;td&gt;&lt;input type=&quot;submit&quot; value=&quot;Complete&quot;&gt;&lt;/td&gt;
	&lt;/tr&gt;
	&lt;/form&gt;
	{% endfor %}
	&lt;/table&gt;
	&lt;br&gt;
	&lt;hr&gt;
	&lt;table border=&quot;1&quot;&gt;
	&lt;form action=&quot;add_task&quot; method=&quot;GET&quot;&gt;
	&lt;tr&gt;&lt;td&gt;Name:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;name&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;Category:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;category&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;Item Date:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;date&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;&lt;input type=&quot;submit&quot; value=&quot;add task&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;/form&gt;
	&lt;/table&gt;
	&lt;/body&gt;
	&lt;/html&gt;    

</pre> 

    
## Azure ストレージ モジュールのインポート
**views.py** の先頭部分で、次のコードを Django のインポートの直後に追加します。

	from azure.storage import TableService

## ストレージ アカウント名とアカウント キーの取得
**views.py** で、次のコードを Azure のインポートの直後に追加し、"youraccount" と "yourkey" の部分を実際のアカウント名とアカウント キーに置き換えます。これで、アカウント名とアカウント キーを Azure 管理ポータルから取得できます。

	account_name = 'youraccount'
	account_key = 'yourkey'

## TableService の作成
次のコードを **account_name** の後に追加します。

	table_service = TableService(account_name=account_name, account_key=account_key)
	table_service.create_table('mytasks')

## タスクの一覧表示
関数 list_tasks を **views.py** に追加します。

	def list_tasks(request): 
		entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
		html = render_to_string('mytasks.html', Context({'entities':entities}))
		return HttpResponse(html)

##  タスクの追加
関数 add_task を **views.py** に追加します。

	def add_task(request):
		name = request.GET['name']
		category = request.GET['category']
		date = request.GET['date']
		table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
		entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
		html = render_to_string('mytasks.html', Context({'entities':entities}))
		return HttpResponse(html)

## タスク状態の更新
関数 update_task を **views.py** に追加します。

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


-  **TableserviceSample** ディレクトリに移動し (現在のディレクトリがこのディレクトリではない場合)、次のコマンドを実行します。

	python manage.py runserver

-   ブラウザーで `http://127.0.0.1:8000/` にアクセスします。8000 は実際のポート番号に置き換えてください。

これで、**[Add Task]** をクリックするとタスクが 1 つ作成されます。**[Complete]** ボタンをクリックするとタスクが更新され、状態が [Yes] に設定されます。



## コンピューティング エミュレーターでのアプリケーションの実行と、アプリケーションの発行、停止、削除

これで、組み込みの Django サーバーでアプリケーションが適切に実行されました。このアプリケーションを Azure エミュレーターに展開し (Windows のみ)、その後 Azure に発行することで、さらに詳細なテストを行うことができます。これを実行する方法の一般的な手順については、「[Django Hello World Web アプリケーション]」の記事を参照してください。この記事では手順が詳細に説明されています。


<h2><a id="NextSteps"></a>次のステップ</h2>

これで、Azure テーブル ストレージ サービスの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

- MSDN リファレンス: [Azure のデータの格納とアクセス] []
- Azure のストレージ チーム ブログ: <http://blogs.msdn.com/b/windowsazurestorage/>


[Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx

[インストール ガイド]: ../python-how-to-install

[ Django Hello World Web アプリケーション]: http://windowsazure.com/ja-jp/documentation/articles/virtual-machines-python-django-web-app-windows-server

