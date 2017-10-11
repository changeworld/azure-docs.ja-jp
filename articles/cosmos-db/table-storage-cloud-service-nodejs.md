---
title: "テーブル ストレージを使用した Web アプリケーション (Node.js) | Microsoft Docs"
description: "Express を使用する Web アプリケーションのチュートリアルを基に、Azure Storage サービスと Azure モジュールを追加するチュートリアル。"
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: b802f880c1131abb7eb9ba00dd8f2e65017bc802
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
# <a name="nodejs-web-application-using-storage"></a>ストレージを使用する Node.js Web アプリケーション
## <a name="overview"></a>概要
このチュートリアルでは、アプリケーションで作成した、 [Express を使用した Node.js Web アプリケーション]データ管理サービスを使用する、Microsoft Azure Client Libraries for Node.js を使用してチュートリアルを拡張します。 アプリケーションを拡張するには、Azure に展開できる web ベースのタスク リスト アプリケーションを作成します。 このタスク一覧では、ユーザーがタスクの取得、新しいタスクの追加、タスクの完了済みのマーク付けを実行できます。

タスク項目は Azure ストレージに格納されます。 Azure ストレージは、フォールト トレランスと可用性に優れた非構造化データ ストレージです。 Azure ストレージには、いくつかのデータ構造で保存し、データにアクセスが含まれています。 Azure SDK に含まれる for Node.js か REST Api を介して Api から記憶域サービスを使用することができます。 詳しくは、「[Azure でのデータの格納とアクセス]」をご覧ください。

このチュートリアルは、[Node.js Web アプリケーション]および [Express を使用した Node.js][Express を使用した Node.js Web アプリケーション] のチュートリアルを完了していることを前提としています。

次の情報が含まれています。

* Jade テンプレート エンジンを操作する方法
* Azure データ管理サービスを操作する方法

次に示すのは完成したアプリケーションのスクリーンショットです。

![Internet Explorer で表示された完成した Web ページ](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Web.Config のストレージ資格情報の設定
Azure ストレージにアクセスするストレージの資格情報で渡す必要があります。 これは、web.config のアプリケーション設定を使用することによって行います。
Web.config の設定に渡される環境変数としてノード、Azure SDK で読み取らです。

> [!NOTE]
> ストレージの資格情報は、アプリケーションが Azure にデプロイされた場合にのみ使用されます。 エミュレーターで実行されている、ときに、アプリケーションは、ストレージ エミュレーターを使用します。
>
>

ストレージ アカウントの資格情報を取得し、web.config 設定に追加するには、次の手順を実行します。

1. Azure PowerShell をまだ開いていない場合は起動します。**[スタート]** メニューから **[すべてのプログラム]、[Azure]** の順に展開し、**[Azure PowerShell]** を右クリックして、**[管理者として実行]** を選びます。
2. アプリケーションが含まれているフォルダーに移動します。 たとえば、C:\\node\\tasklist\\WebRole1 です。
3. Azure Powershell ウィンドウで、ストレージ アカウントの情報を取得する次のコマンドレットを入力します。

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts
    ```

   上記のコマンドレットは、ストレージ アカウントとキーは、ホステッド サービスに関連付けられているアカウントの一覧を取得します。

   > [!NOTE]
   > Azure SDK では、サービスを展開するときにストレージ アカウントが作成されるので、以前のガイドでアプリケーションを展開したときから、ストレージ アカウントは既に存在しています。
   >
   >
4. **ServiceDefinition.csdef** ファイルを開きます。このファイルには、アプリケーションを Azure にデプロイするときに使われる環境設定が含まれます。

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. 次のブロックを **Environment** 要素の下に挿入し、{STORAGE ACCOUNT} と {STORAGE ACCESS KEY} を、デプロイメントに使うストレージ アカウントのアカウント名とプライマリ キーに置き換えます。

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![web.cloud.config ファイルの内容](./media/table-storage-cloud-service-nodejs/node37.png)

6. ファイルを保存して、メモ帳を閉じます。

### <a name="install-additional-modules"></a>追加モジュールをインストールする
1. 次のコマンドを入力して、[azure]、[node-uuid]、[nconf]、および [async] モジュールをローカルにインストールし、これらのモジュールのエントリを **package.json** ファイルに保存します。

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  このコマンドの出力は次のように表示されます。

  ```
  node-uuid@1.4.1 node_modules\node-uuid

  nconf@0.6.9 node_modules\nconf
  ├── ini@1.1.0
  ├── async@0.2.9
  └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

  azure-storage@0.1.0 node_modules\azure-storage
  ├── extend@1.2.1
  ├── xmlbuilder@0.4.3
  ├── mime@1.2.11
  ├── underscore@1.4.4
  ├── validator@3.1.0
  ├── node-uuid@1.4.1
  ├── xml2js@0.2.7 (sax@0.5.2)
  └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)
  ```

## <a name="using-the-table-service-in-a-node-application"></a>ノード アプリケーションでのテーブル サービスの使用
このセクションでは、基本的なアプリケーションがによって作成された、 **express**コマンドに追加することによって拡張されて、 **task.js**タスク用のモデルを含むファイルです。 既存の変更**app.js**ファイルされ、新しい作成**tasklist.js**モデルを使用するファイル。

### <a name="create-the-model"></a>モデルの作成
1. **WebRole1** ディレクトリ内に、**models** という名前の新しいディレクトリを作ります。
2. **models** ディレクトリ内に、**task.js** という名前の新しいファイルを作ります。 このファイルには、アプリケーションによって作成されたタスクのモデルが含まれています。
3. **task.js** ファイルの先頭に、必要なライブラリを参照する次のコードを追加します。

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. 次に、定義して、タスク オブジェクトをエクスポートするコードを追加します。 タスク オブジェクトは、テーブルへの接続をします。

    ```nodejs
    module.exports = Task;

    function Task(storageClient, tableName, partitionKey) {
      this.storageClient = storageClient;
      this.tableName = tableName;
      this.partitionKey = partitionKey;
      this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
        if(error) {
          throw error;
        }
      });
    };
    ```

5. 次に、Task オブジェクトの追加のメソッドを定義する次のコードを追加します。このメソッドによって、テーブルに格納されたデータを操作できます。

    ```nodejs
    Task.prototype = {
      find: function(query, callback) {
        self = this;
        self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
          if(error) {
            callback(error);
          } else {
            callback(null, result.entries);
          }
        });
      },

      addItem: function(item, callback) {
        self = this;
        // use entityGenerator to set types
        // NOTE: RowKey must be a string type, even though
        // it contains a GUID in this example.
        var itemDescriptor = {
          PartitionKey: entityGen.String(self.partitionKey),
          RowKey: entityGen.String(uuid()),
          name: entityGen.String(item.name),
          category: entityGen.String(item.category),
          completed: entityGen.Boolean(false)
        };

        self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
          if(error){
            callback(error);
          }
          callback(null);
        });
      },

      updateItem: function(rKey, callback) {
        self = this;
        self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
          if(error) {
            callback(error);
          }
          entity.completed._ = true;
          self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
            if(error) {
              callback(error);
            }
            callback(null);
          });
        });
      }
    }
    ```

6. **task.js** ファイルを保存して閉じます。

### <a name="create-the-controller"></a>コントローラーの作成
1. **WebRole1/routes** ディレクトリに **tasklist.js** という名前の新しいファイルを作り、テキスト エディターで開きます。
2. 次のコードを **tasklist.js** に追加します。 このコードで使用され、azure と非同期モジュールが読み込まれます**tasklist.js**を定義し、 **TaskList**関数のインスタンスに渡されましたが、**タスク**前に定義したオブジェクトします。

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. **tasklist.js** ファイルへのコードの追加を続行し、タスクの表示、追加、完了に使われる、**showTasks**、**addTask**、および **completeTasks** の各メソッドを追加します。

    ```nodejs
    TaskList.prototype = {
      showTasks: function(req, res) {
        self = this;
        var query = azure.TableQuery()
          .where('completed eq ?', false);
        self.task.find(query, function itemsFound(error, items) {
          res.render('index',{title: 'My ToDo List ', tasks: items});
        });
      },

      addTask: function(req,res) {
        var self = this
        var item = req.body.item;
        self.task.addItem(item, function itemAdded(error) {
          if(error) {
            throw error;
          }
          res.redirect('/');
        });
      },

      completeTask: function(req,res) {
        var self = this;
        var completedTasks = Object.keys(req.body);
        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
          self.task.updateItem(completedTask, function itemsUpdated(error) {
            if(error){
              callback(error);
            } else {
              callback(null);
            }
          });
        }, function goHome(error){
          if(error) {
            throw error;
          } else {
            res.redirect('/');
          }
        });
      }
    }
    ```

4. **tasklist.js** ファイルを保存します。

### <a name="modify-appjs"></a>app.js の変更
1. **WebRole1** ディレクトリ内の **app.js** ファイルを、テキスト エディターで開きます。
2. ファイルの先頭に次のコードを追加します。このコードは、azure モジュールを読み込み、テーブル名とパーティション キーを設定します。

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. app.js ファイル内で、次の行が表示されるまで下へスクロールします。

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    前の行を次のコードに置き換えます。 このコードは、のインスタンスを初期化<strong>タスク</strong>ストレージ アカウントへの接続を使用します。 <strong>タスク</strong>に渡される、 <strong>TaskList</strong>、テーブル サービスとの通信に使用します。

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. **app.js** ファイルを保存します。

### <a name="modify-the-index-view"></a>index ビューの変更
1. **views** ディレクトリに移動し、テキスト エディターで **index.jade** ファイルを開きます。
2. 内容を置き換える、 **index.jade**を次のコード ファイル。 このコードは、既存のタスクを表示するビューを定義し、新しいタスクを追加して、完了済みとして既存のテーブルをマークするためのフォームを定義します。

    ```
    extends layout

    block content
      h1= title
      br

      form(action="/completetask", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Date
            td Complete
          if tasks != []
            tr
              td
          else
            each task in tasks
              tr
                td #{task.name._}
                td #{task.category._}
                - var day   = task.Timestamp._.getDate();
                - var month = task.Timestamp._.getMonth() + 1;
                - var year  = task.Timestamp._.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
        button.btn(type="submit") Update tasks
      hr
      form.well(action="/addtask", method="post")
        label Item Name:
        input(name="item[name]", type="textbox")
        label Item Category:
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item
    ```

3. **index.jade** ファイルを保存して閉じます。

### <a name="modify-the-global-layout"></a>グローバル レイアウトの変更
**views** ディレクトリ内の **layout.jade** ファイルは、他の **.jade** ファイルのグローバル テンプレートとして使われます。 この手順で変更、 **layout.jade**に使用するファイル[Twitter のブートス トラップ](https://github.com/twbs/bootstrap)、nice 探し求めている web サイトを設計しやすくツールキットはします。

1. [Twitter Bootstrap](http://getbootstrap.com/)のファイルをダウンロードして展開します。 **bootstrap\\dist\\css** フォルダーから **bootstrap.min.css** ファイルを tasklist アプリケーションの **public\\stylesheets** ディレクトリにコピーします。
2. **ビュー**フォルダーを開き、 **layout.jade**テキスト エディターでファイルし、次の内容を置き換えます。

    doctype html  html    head      title= title      link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')      link(rel='stylesheet', href='/stylesheets/style.css')    body.app      nav.navbar.navbar-default        div.navbar-header          a.navbar-brand(href='/') My Tasks      block content

3. **layout.jade** ファイルを保存します。

### <a name="running-the-application-in-the-emulator"></a>エミュレーターでのアプリケーションの実行
エミュレーターでアプリケーションを開始するには、次のコマンドを実行します。

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

ブラウザーが開き、次のページが表示されます。

![My Task List というタイトルの Web ページと新しいタスクを追加するためのタスクとフィールドを含むテーブル](./media/table-storage-cloud-service-nodejs/node44.png)

フォームを使用してアイテムを追加するか、完了済みのマークを付けることにより既存のアイテムを削除します。

## <a name="publishing-the-application-to-azure"></a>Azure にアプリケーションをデプロイする
Windows PowerShell ウィンドウで、次のコマンドレットを呼び出して、ホストされるサービスを Azure に再デプロイします。

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

**myuniquename** を、このアプリケーションの一意の名前に置き換えます。 **datacentername** を、Azure データ センターの名前で置き換えます (たとえば、**West US**)。

デプロイが完了すると、次のような応答が表示されます。

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
  WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
  WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
  WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
  WARNING: 2:19:01 PM - Connecting...
  WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
  WARNING: 2:19:40 PM - Upgrading...
  WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
  WARNING: 2:22:48 PM - Initializing...
  WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
  WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.
```

指定して、 **-起動**コマンドレットでは、以前、ブラウザーのオプションが開き、アプリケーションの発行が完了したときに、Azure で実行されているが表示されます。

![My Task List ページを表示しているブラウザー ウィンドウ。 URL から、ページが Azure でホストされていることがわかります](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>アプリケーションの停止と削除
アプリケーションのデプロイ後に、コストがかかることを避けたり、無料評価版の有効期間中に他のアプリケーションを作成してデプロイしたりするために、アプリケーションを無効にしたい場合があります。

Azure では、消費されたサーバー時間の 1 時間単位の料金が Web ロール インスタンスに課金されます。
インスタンスが実行されていない場合や停止状態の場合でも、アプリケーションを展開した直後からサーバー時間が消費されます。

次の手順では、アプリケーションの停止と削除の方法を示します。

1. Windows PowerShell ウィンドウで次のコマンドレットを実行し、前のセクションで作成したサービスの展開を停止します。

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   サービスの停止には、数分間かかる場合があります。 サービスが停止すると、停止したことを知らせるメッセージが表示されます。

2. サービスを削除するには、次のコマンドレットを呼び出します。

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、サービスを削除します。

   サービスの削除には、数分間かかる場合があります。 サービスを削除した後、サービスが削除されたことを示すメッセージが表示されます。

[Express を使用した Node.js Web アプリケーション]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[Azure でのデータの格納とアクセス]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Node.js Web アプリケーション]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/


