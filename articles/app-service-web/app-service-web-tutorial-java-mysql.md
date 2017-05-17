---
title: "Azure で Java と MySQL Web アプリを構築する | Microsoft Docs"
description: "Azure MySQL データベース サービスに接続する Java アプリを Azure App Service で動作させる方法について説明します。"
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Azure で Java と MySQL Web アプリを構築する
このチュートリアルでは、Azure で Java Web アプリを作成し、MySQL データベースに接続する方法について説明します。 最初の手順は、ローカル コンピューターにアプリケーションを複製し、ローカル MySQL インスタンスで動作させることです。
次の手順は、Java アプリと MySQL 用に Azure サービスを設定した後、アプリケーションを Azure App Service にデプロイすることです。
このチュートリアルを完了すると、Azure で実行され、Azure MySQL データベース サービスに接続する To-Do List アプリケーションが完成します。

![Azure App Service で実行される Java アプリ](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>開始する前に

このサンプルを実行する前に、前提条件となる以下をローカルにインストールします。

1. [Git をダウンロードし、インストールします](https://git-scm.com/)
1. [Java 7 以降をダウンロードし、インストールします](http://Java.net/downloads.Java)
1. [Maven をダウンロードし、インストールします](https://maven.apache.org/download.cgi)
1. [MySQL をダウンロード、インストール、および起動します](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Azure CLI 2.0 をダウンロードし、インストールします](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>ローカル MySQL データベースを準備する

この手順では、このチュートリアルで使用するデータベースをローカル MySQL サーバーに作成します。

### <a name="connect-to-mysql-server"></a>MySQL サーバーに接続する
コマンドラインからローカル MySQL サーバーに接続します。

```bash
mysql -u root -p
```

コマンドが正常に実行されれば、MySQL サーバーは既に実行されています。 正常に実行されない場合は、[MySQL のインストール後の手順](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)に従って、MySQL サーバーが起動されたことを確認してください。

パスワードの入力を求められたら、`root` アカウントのパスワードを入力します。 ルート アカウントのパスワードを思い出せない場合は、「[MySQL: root のパスワードをリセットする方法](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)」を参照してください。


### <a name="create-a-database-and-table"></a>データベースとテーブルを作成する

`mysql` プロンプトで、データベースと To-Do 項目テーブルを作成します。

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

## <a name="create-local-java-application"></a>ローカル Java アプリケーションを作成する
この手順では、GitHub レポジトリを複製し、MySQL データベース接続を構成し、アプリをローカルで実行します。 

### <a name="clone-the-sample"></a>サンプルを複製する

コマンド プロンプトから、作業ディレクトリに移動します。  

次のコマンドを実行して、サンプル リポジトリを複製します。 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

次に、レポジトリの Readme の手順に従って、lombok.jar を設定します。


### <a name="configure-mysql-connection"></a>MySQL 接続を構成する

このアプリケーションでは、Maven Jetty プラグインを使用してアプリケーションをローカルで実行し、MySQL データベースに接続します。
ローカル MySQL インスタンスにアクセスできるようにするには、ローカル MySQL のユーザー ID とパスワードを WebContent/WEB-INF/jetty-env.xml に設定します。

ユーザーとパスワードの値をローカル MySQL インスタンスのユーザー ID とパスワードで更新します。

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt;Jetty による `jetty-env.xml` ファイルの使用方法については、[Jetty XML リファレンス](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html)を参照してください。
&gt; &gt;

### <a name="run-the-sample"></a>サンプルを実行する

Maven コマンドを使用してサンプルを実行します。 

```bash
mvn package jetty:run
```

次に、ブラウザーで `http://localhost:8080` に移動します。 ページにいくつかのタスクを追加します。

アプリケーションを任意のタイミングで停止するには、コマンド プロンプトで `Ctrl`+`C` キーを押します。 

## <a name="create-a-mysql-database-in-azure"></a>Azure で MySQL データベースを作成する

この手順では、MySQL データベースを[Azure Database for MySQL (Preview)](/azure/mysql) に作成します。 その後、このデータベースに接続するように Java アプリケーションを構成します。

### <a name="log-in-to-azure"></a>Azure にログインする

ターミナル ウィンドウで Azure CLI 2.0 を使用して、Azure App Service で Java アプリケーションをホストするために必要なリソースを作成します。 [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドを使用して、[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 Azure リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例は、北ヨーロッパ リージョンにリソース グループを作成します。

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

`--location` に使用できる値を確認するには、[az appservice list-locations](/cli/azure/appservice#list-locations) コマンドを使用します。

### <a name="create-a-mysql-server"></a>MySQL サーバーを作成する

[az mysql server create](/cli/azure/mysql/server#create) コマンドを使用して、Azure Database for MySQL (Preview) にサーバーを作成します。

`&lt;mysql_server_name&gt;` プレースホルダーを独自の一意の MySQL サーバー名に置き換えます。 この名前は、MySQL サーバーのホスト名 (`&lt;mysql_server_name&gt;.database.windows.net`) の一部であるため、グローバルに一意である必要があります。 `&lt;admin_user&gt;` と `&lt;admin_password&gt;` も独自の値に置き換えます。

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

MySQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>サーバーのファイアウォールを構成する

[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) コマンドを使用して、MySQL サーバーのクライアントへの接続を許可するファイアウォール ルールを作成します。 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt; 現時点では、Azure Database for MySQL (Preview) は、Azure Services からの接続のみを有効にすることはできません。 Azure の IP アドレスは動的に割り当てられるため、当面はすべての IP アドレスを有効にしておくことをお勧めします。 サービスはプレビュー段階であるため、データベースを保護するためのより優れた方法はまもなく利用可能になる予定です。
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>MySQL サーバーに接続する

ターミナル ウィンドウで、Azure の MySQL サーバーに接続します。 `&lt;admin_user&gt;` と `&lt;mysql_server_name&gt;` に指定した値を使用します。

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>Azure MySQL サービスにデータベースとテーブルを作成する

`mysql` プロンプトで、データベースと To-Do 項目テーブルを作成します。

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>アクセス許可を持つユーザーを作成する

データベース ユーザーを作成し、`todoItemDb` データベースのすべての特権を付与します。 プレースホルダーの `&lt;Javaapp_user&gt;` と `&lt;Javaapp_password&gt;` を独自の一意の名前に置き換えます。

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>ローカル MySQL 接続を新しい Azure MySQL サービスで構成する

この手順では、Java アプリケーションを Azure Database for MySQL (Preview) に作成した MySQL データベースに接続します。 

ローカル アプリケーションから Azure MySQL サービスにアクセスできるようにするには、新しい MySQL エンドポイント、ユーザー ID、およびパスワードを WebContent/WEB-INF/jetty-env.xml に設定します。

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

変更を保存します。

## <a name="test-the-application"></a>アプリケーションをテストする

前回と同じ Maven コマンドを使用してサンプルをローカルでもう一度実行しますが、今回は Azure MySQL サービスに接続します。 

```bash
mvn package jetty:run
```

ブラウザーで `http://localhost:8080` にアクセスします。 エラーの発生なしでページが読み込まれれば、Java アプリケーションは Azure の MySQL データベースに接続しています。 

ページにタスクを追加する必要はありません。

アプリケーションを任意のタイミングで停止するには、ターミナルで `Ctrl`+`C` キーを押します。 

### <a name="secure-sensitive-data"></a>機微なデータをセキュリティで保護する

`WebContent/WEB-INF/jetty-env.xml` の機微なデータが Git にコミットされないことを確認します。

これを行うには、レポジトリのルートから `.gitignore` を開き、新しい行に `WebContent/WEB-INF/jetty-env.xml` を追加します。 変更を保存します。

変更を `.gitignore` にコミットします。

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>Azure に Java アプリケーションを展開する
次に、Java アプリケーションを Azure App Service にデプロイします。

### <a name="create-an-appservice-plan"></a>App Service プランを作成する

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドを使用して、App Service プランを作成します。 

&gt; [!NOTE] 
&gt; App Service プランは、アプリをホストするために使用される物理リソースのコレクションを表しています。 App Service プランに割り当てられたすべてのアプリケーションは、プランで定義されたリソースを共有します。これにより、複数のアプリをホストする際にコストを節約できます。 
&gt; &gt; App Service プランは、&gt; &gt; * リージョン (北ヨーロッパ、米国東部、東南アジア) &gt; * インスタンス サイズ (大、中、小) &gt; * スケール カウント (インスタンス数 1、2、3 など) &gt; * SKU (Free、Shared、Basic、Standard、Premium) &gt; の各項目を選択して定義します。 

次の例は、**Free** 価格レベルを使用して、`myAppServicePlan` という名前の App Service プランを作成します。

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure Web アプリを作成する

App Service プランを作成したので、`myAppServicePlan` App Service プラン内に Azure Web アプリを作成します。 Web アプリにより、コードをデプロイするためのホスト領域が取得され、デプロイされたアプリケーションを表示するための URL が提供されます。 Web アプリを作成するには、[az appservice web create](/cli/azure/appservice/web#create) コマンドを使用します。 

次のコマンドで、`&lt;app_name&gt;` プレースホルダーを独自の一意のアプリ名に置き換えます。 この一意の名前は、Web アプリの既定のドメイン名の一部として使用されます。そのため、この名前は Azure のすべてのアプリで一意である必要があります。 後で、Web アプリをユーザーに公開する前に、任意のカスタム DNS エントリを Web アプリにマップできます。 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
```

Web アプリが作成されると、Azure CLI によって次の例のような情報が表示されます。 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>Java のバージョン、Java アプリケーション サーバーの種類、およびアプリケーション サーバーのバージョンを設定する

[az appservice web config update](/cli/azure/appservice/web/config#update) コマンドを使用して、Java のバージョン、Java App Server (コンテナー)、およびコンテナーのバージョンを設定します。

次のコマンドは、Java のバージョンを 8 に、Java App Server を Jetty に、Jetty のバージョンを Newest Jetty 9.3 に設定します。

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>FTP を使用して Web アプリにデプロイするための資格情報を取得する 

アプリケーションを Azure App Service にデプロイするには、FTP、ローカル Git、GitHub、Visual Studio Team Services、BitBucket など、さまざまな方法があります。 この例では、Maven を使用して .WAR ファイルをコンパイルし、FTP を使用して .WAR ファイルを Web アプリにデプロイします。

ftp コマンドで Web アプリに渡す資格情報を確認するには、[az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles) コマンドを次のように使用します。 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>Web アプリにデプロイするローカル アプリケーションをコンパイルする 

ローカル Java アプリケーションを Azure Web アプリで実行するための準備を行うには、Java アプリケーション内のすべてのリソースを単一の .WAR ファイルに再コンパイルしてデプロイできるようにします。 アプリケーションの pom.xml があるディレクトリに移動し、次のように入力します。

```bash 
mvn clean package
``` 
Maven パッケージ プロセスの最後に .WAR ファイルの場所が表示されます。  出力は次のようになります。

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

.War の場所をメモし、任意の FTP メソッドを使用して .War ファイルを Jetty WebApps フォルダーにデプロイします。  Jetty WebApps フォルダーは Azure Web App の /site/wwwroot/webapps に配置されています。 

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリを参照する

`http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;` を参照し、一覧にいくつかのタスクを追加します。 

![Azure App Service で実行される Java アプリ](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**お疲れさまでした。** データ主導型の Java アプリが Azure App Service で実行されています。
アプリを更新するには、mvn clean package コマンドを繰り返し実行し、FTP 経由でアプリを再びデプロイします。

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

Azure ポータルに移動し、[https://portal.azure.com](https://portal.azure.com) にサインインして、作成した Web アプリを表示します。

左側のメニューで **[App Service]** をクリックした後、Azure Web アプリの名前をクリックします。

Web アプリの "_ブレード_" (水平方向に開かれるポータル ページ) が表示されます。

既定では、Web アプリのブレードは **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ブレードの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

**[アプリケーション設定]** ページでは、次の設定を管理できます。 

![Azure aAp Service Web アプリのアプリケーション設定](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



ブレードのこれらのタブは、Web アプリに追加することができるさまざまな優れた機能を示しています。 次の一覧では、ほんの一部の例を示しています。

* カスタム DNS 名をマップする
* カスタム SSL 証明書をバインドする
* 継続的なデプロイを構成する
* スケールアップとスケールアウトを行う
* ユーザー認証を追加する

## <a name="more-resources"></a>その他のリソース

- [既存のカスタム DNS 名を Azure Web Apps にマップする](app-service-web-tutorial-custom-domain.md)
- [既存のカスタム SSL 証明書を Azure Web Apps にバインドする](app-service-web-tutorial-custom-ssl.md)
- [Web アプリの CLI スクリプト](app-service-cli-samples.md)</PRE>

