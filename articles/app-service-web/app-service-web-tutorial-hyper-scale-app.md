---
title: "Azure でハイパースケール アプリを構築する | Microsoft Docs"
description: "さまざまな Azure サービスを使用して、Azure で ASP.NET アプリのパフォーマンスを最大限にする方法について説明します。"
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: erikre
editor: 
ms.assetid: a4d49ac7-0f97-4997-84c5-cdb9c4465757
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: eac9c5b0d8d0f7802d88e6f4f27d9d23c406e025
ms.contentlocale: ja-jp
ms.lasthandoff: 04/28/2017


---
# <a name="build-a-hyper-scale-web-app-in-azure"></a>Azure でハイパースケール Web アプリを構築する

このチュートリアルでは、ユーザー要求を最大限にするために、Azure で ASP.NET Web アプリをスケールアウトする方法について説明します。

このチュートリアルを開始する前に、コンピューターに [Azure CLI がインストール](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)されていることを確認してください。 また、サンプル アプリケーションを実行するために、ローカル コンピューター上に [Visual Studio](https://www.visualstudio.com/vs/) が必要です。

## <a name="step-1---get-sample-application"></a>手順 1 - サンプル アプリケーションを取得する
この手順では、ローカル ASP.NET プロジェクトを設定します。

### <a name="clone-the-application-repository"></a>アプリケーション レポジトリを複製する

任意のコマンド ライン ターミナルと `CD` を作業ディレクトリに開きます。 次のコマンドを実行して、サンプル アプリケーションを複製します。 

```powershell
git clone https://github.com/cephalin/HighScaleApp.git
```

### <a name="run-the-sample-application-in-visual-studio"></a>Visual Studio でサンプル アプリケーションを実行する

Visual Studio でソリューションを開きます。

```powershell
cd HighScaleApp
.\HighScaleApp.sln
```

`F5` キーを押してアプリケーションを実行します。

このサンプル ASP.NET Web アプリケーションは、既定のテンプレートを使用して作成されており、ユーザー セッションを維持し、出力キャッシュを使用します。 `HighScaleApp\Controllers\HomeController.cs` を確認します。 `Index()` メソッドは、セッションにデータを追加します。

```csharp
Session.Add("visited", "true"); 
```

`About()` メソッドと `Contact()` メソッドは、出力をキャッシュします。

```csharp
[OutputCache(Duration = 60)]
```

## <a name="step-2---deploy-to-azure"></a>手順 2 - Azure にデプロイする
この手順では、Azure Web アプリを作成し、サンプル ASP.NET アプリケーションをデプロイします。

### <a name="create-a-resource-group"></a>リソース グループの作成   
[az group create](https://docs.microsoft.com/cli/azure/group#create) を使用して、西ヨーロッパ リージョンに[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループには、Web アプリや SQL Database バックエンドなど、まとめて管理するすべての Azure リソースを配置します。

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

`---location` に使用できる値を確認するには、[az appservice list-locations](https://docs.microsoft.com/en-us/cli/azure/appservice#list-locations) コマンドを使用します。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する
[az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) を使用して、"B1" [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

App Service プランはスケール ユニットであり、同じ App Service インフラストラクチャ上でまとめてスケールアップまたはスケールアウトするアプリをいくつでも含めることができます。 各プランには、[価格レベル](https://azure.microsoft.com/en-us/pricing/details/app-service/)も割り当てられています。 レベルが高いほど、優れたハードウェアと多数の機能 (スケールアウト インスタンス数の増加など) が含まれます。

このチュートリアルで使用する B1 は、3 インスタンスにスケールアウトできる最低レベルです。 アプリの価格レベルは、[az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) を実行することによって、後でいつでも上げ下げできます。 

### <a name="create-a-web-app"></a>Web アプリを作成する
[az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create) を使用して Web アプリを作成します。`$appName` には、一意の名前を使用します。

```azurecli
$appName = "<replace-with-a-unique-name>"
az appservice web create --name $appName --resource-group myResourceGroup --plan myAppServicePlan
```

### <a name="set-deployment-credentials"></a>デプロイメント資格情報の設定
[az appservice web deployment user set](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/user#set) を使用して、App Service のアカウント レベルのデプロイ資格情報を設定します。

```azurecli
az appservice web deployment user set --user-name <letters-numbers> --password <mininum-8-char-captital-lowercase-letters-numbers>
```

### <a name="configure-git-deployment"></a>Git デプロイを構成する
[az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) コマンドを使用して、ローカル Git デプロイを構成します。

```azurecli
az appservice web source-control config-local-git --name $appName --resource-group myResourceGroup
```

このコマンドは次のような出力を返します。

```json
{
  "url": "https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git"
}
```

返された URL を使用して、Git リモートを構成します。 次のコマンドでは、前の出力例を使用しています。

```powershell
git remote add azure https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-the-sample-application"></a>サンプル アプリケーションをデプロイする
これでサンプル アプリケーションをデプロイする準備ができました。 `git push` を実行します。

```powershell
git push azure master
```

パスワードの入力を求められたら、`az appservice web deployment user set` を実行したときに指定したパスワードを使用します。

### <a name="browse-to-azure-web-app"></a>Azure Web アプリを参照する
[az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) を使用して、Azure でアプリがライブ実行されていることを確認します。

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-3---connect-to-redis"></a>手順 3 - Redis に接続する
この手順では、Azure Web アプリの外部併置型キャッシュとして Azure Redis Cache を設定します。 Redis を使用すると、ページ出力を迅速にキャッシュできます。 また、後で Web アプリをスケールアウトしたときに、複数のインスタンス間でユーザー セッションを確実に維持できます。

### <a name="create-an-azure-redis-cache"></a>Azure Redis Cache の作成
[az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) を使用して Azure Redis Cache を作成し、JSON 出力を保存します。 `$cacheName` には、一意の名前を使用します。

```powershell
$cacheName = "<replace-with-a-unique-cache-name>"
$redis = (az redis create --name $cacheName --resource-group myResourceGroup --location "West Europe" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

### <a name="configure-the-application-to-use-redis"></a>Redis を使用するようにアプリケーションを構成する
キャッシュの接続文字列の書式を設定します。

```powershell
$connstring = "$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False"
$connstring 
```

2 行目で次のような出力が返されます。

```
mycachename.redis.cache.windows.net:6380,password=/rQP/TLz1mrEPpmh9b/gnfns/t9vBRXqXn3i1RwBjGA=,ssl=True,abortConnect=False
```

Visual Studio で、プロジェクトのルートに `redis.config` という Web 構成ファイルを作成し、次のコードを貼り付けます。 `value` には、PowerShell の出力から取得した接続文字列を使用します。

```xml
<appSettings>
  <add key="RedisConnection" value="your-azure-redis-cache-connection-string"/>
</appSettings>
```

Git レポジトリで `.gitignore` ファイルを確認すると、このファイルがソース管理から除外されていることがわかります。 このようにして機密情報が保護されます。 

`Web.config`を開きます。 `<appSettings file="redis.config">` 要素を確認します。この要素は、`redis.config` に作成された設定を取得します。 

`<sessionState>` と `<caching>` が含まれたコメント化されたセクションを見つけます。 このセクションをコメント解除します。

![](./media/app-service-web-tutorial-hyper-scale-app/redisproviders.png)

このコードは、`RedisConnection` で定義された Redis 接続文字列を探します。 

これで、アプリケーションは Redis を使用してセッションとキャッシュを管理できるようになりました。 `F5` キーを押してアプリケーションを実行します。 必要に応じて、Redis 管理クライアントをダウンロードして、キャッシュに保存されたデータを視覚化できます。

### <a name="configure-the-connection-string-in-azure"></a>Azure で接続文字列を構成する

Azure でアプリケーションを動作させるには、Azure Web アプリで同じ Redis 接続文字列を構成する必要があります。 ソース管理で `redis.config` は管理されないため、Git デプロイを実行しても Azure にはデプロイされません。

[az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) を使用して、同じ名前 (`RedisConnection`) で接続文字列を追加します。

az appservice web config appsettings update --settings "RedisConnection=$connstring" --name $appName --resource-group myResourceGroup

`$connstring` には、書式設定された接続文字列が含まれることに注意してください。

### <a name="redeploy-the-application-to-azure"></a>アプリケーションを Azure に再デプロイする
Git コマンドを使用して、変更を Azure にプッシュします。

```bash
git add .
git commit -m "now use Redis providers"
git push azure master
```

パスワードの入力を求められたら、`az appservice web deployment user set` を実行したときに指定したパスワードを使用します。

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリを参照する
[az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) を使用して、Azure で変更が反映されていることを確認します。

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-4---scale-to-multiple-instances"></a>手順 4 - 複数のインスタンスにスケールする
App Service プランは、Azure Web アプリのスケール ユニットです。 Web アプリをスケールアウトするには、App Service プランをスケールします。

[az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) を使用して、App Service プランを 3 インスタンスにスケールアウトします。これは、B1 価格レベルで許可されている最大数です。 B1 は、App Service プランの作成時に選択した価格レベルです。 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --number-of-workers 3 
```

## <a name="step-5---scale-geographically"></a>手順 5 - 地理的にスケールする
地理的にスケールするときは、Azure クラウドの複数のリージョンでアプリを実行します。 このセットアップでは、地理的な場所に基づいてアプリの負荷がさらに分散され、クライアント ブラウザーの近くにアプリを配置することで応答時間が短縮されます。

この手順では、[Azure Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/) を使用して、ASP.NET Web アプリを 2 つ目のリージョンにスケールします。 手順が完了すると、西ヨーロッパで実行される Web アプリ (作成済み) と東南アジアで実行される Web アプリ (未作成) を持つことになります。 2 つのアプリは、Traffic Manager の同じ URL から提供されます。

### <a name="scale-up-the-europe-app-to-standard-tier"></a>ヨーロッパのアプリを Standard レベルにスケールアップする
App Service で Azure Traffic Manager と統合するには、Standard 価格レベルが必要です。 [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) を使用して、App Service プランを S1 にスケールアップします。 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --sku S1
```
### <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成 
[az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#create) を使用して、Traffic Manager プロファイルを作成し、リソース グループに追加します。 $dnsName には一意の DNS 名を使用します。

```azurecli
$dnsName = "<replace-with-unique-dns-name>"
az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name $dnsName
```

> [!NOTE]
> `--routing-method Performance` は、このプロファイルが[最も近いエンドポイントにユーザー トラフィックをルーティング](../traffic-manager/traffic-manager-routing-methods.md)することを示します。

### <a name="get-the-resource-id-of-the-europe-app"></a>ヨーロッパのアプリのリソース ID を取得する
[az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show) を使用して、Web アプリのリソース ID を取得します。

```azurecli
$appId = az appservice web show --name $appName --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-europe-app"></a>ヨーロッパのアプリの Traffic Manager エンドポイントを追加する
[az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create) を使用して、Traffic Manager プロファイルにエンドポイントを追加し、ターゲットとして Web アプリのリソース ID を使用します。

```azurecli
az network traffic-manager endpoint create --name myWestEuropeEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appId
```

### <a name="get-the-traffic-manager-endpoint-url"></a>Traffic Manager エンドポイントの URL を取得する
Traffic Manager プロファイルに、既存の Web アプリを参照するエンドポイントが追加されました。 [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#show) を使用して、エンドポイントの URL を取得します。 

```azurecli
az network traffic-manager profile show --name myTrafficManagerProfile --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
```

出力をブラウザーにコピーします。 Web アプリが再度表示されます。

### <a name="create-an-azure-redis-cache-in-asia"></a>アジアに Azure Redis Cache を作成する
次に、Azure Web アプリを東南アジア リージョンにレプリケートします。 まず、[az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) を使用して、2 つ目の Azure Redis Cache を東南アジアに作成します。 このキャッシュは、アジアのアプリと併置する必要があります。

```powershell
$redis = (az redis create --name $cacheName-asia --resource-group myResourceGroup --location "Southeast Asia" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

`--name $cacheName-asia` は、このキャッシュに、`-asia` というサフィックスを付けた西ヨーロッパのキャッシュの名前を付けます。

### <a name="create-an-app-service-plan-in-asia"></a>アジアに App Service プランを作成する
[az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) を使用して、西ヨーロッパのプランと同じ S1 レベルを使用した 2 つ目の App Service プランを東南アジア リージョンに作成します。

```azurecli
az appservice plan create --name myAppServicePlanAsia --resource-group myResourceGroup --location "Southeast Asia" --sku S1
```

### <a name="create-a-web-app-in-asia"></a>アジアに Web アプリを作成する
[az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create) を使用して、2 つ目の Web アプリを作成します。

```azurecli
az appservice web create --name $appName-asia --resource-group myResourceGroup --plan myAppServicePlanAsia
```

`--name $appName-asia` は、このアプリに、`-asia` というサフィックスを付けた西ヨーロッパのアプリの名前を付けます。

### <a name="configure-the-connection-string-for-redis"></a>Redis の接続文字列を構成する
[az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) を使用して、東南アジアのキャッシュの接続文字列を Web アプリに追加します。

az appservice web config appsettings update --settings "RedisConnection=$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False" --name $appName-asia --resource-group myResourceGroup

### <a name="configure-git-deployment-for-the-asia-app"></a>アジアのアプリの Git デプロイを構成します。
[az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) を使用して、2 つ目の Web アプリのローカル Git デプロイを構成します。

```azurecli
az appservice web source-control config-local-git --name $appName-asia --resource-group myResourceGroup
```

このコマンドは次のような出力を返します。

```json
{
  "url": "https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git"
}
```

返された URL を使用して、ローカル レポジトリの 2 つ目の Git リモートを構成します。 次のコマンドでは、前の出力例を使用しています。

```bash
git remote add azure-asia https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-your-sample-application"></a>サンプル アプリケーションをデプロイする
`git push` を実行して、サンプル アプリケーションを 2 つ目の Git リモートにデプロイします。 

```bash
git push azure-asia master
```

パスワードの入力を求められたら、`az appservice web deployment user set` を実行したときに指定したパスワードを使用します。

### <a name="browse-to-the-asia-app"></a>アジアのアプリを参照する
[az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) を使用して、Azure でアプリがライブ実行されていることを確認します。

```azurecli
az appservice web browse --name $appName-asia --resource-group myResourceGroup
```

### <a name="get-the-resource-id-of-the-asia-app"></a>アジアのアプリのリソース ID を取得する
[az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show) を使用して、東南アジアの Web アプリのリソース ID を取得します。

```azurecli
$appIdAsia = az appservice web show --name $appName-asia --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-asia-app"></a>アジアのアプリの Traffic Manager エンドポイントを追加する
[az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create) を使用して、Traffic Manager プロファイルに 2 つ目のエンドポイントを追加します。

```azurecli
az network traffic-manager endpoint create --name myAsiaEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appIdAsia
```

### <a name="add-region-identifier-to-web-apps"></a>Web アプリにリージョン識別子を追加する
[az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) を使用して、リージョン固有の環境変数を追加します。

```azurecli
az appservice web config appsettings update --settings "Region=West Europe" --name $appName --resource-group myResourceGroup
az appservice web config appsettings update --settings "Region=Southeast Asia" --name $appName-asia --resource-group myResourceGroup
```

アプリケーション コードでは、このアプリケーション設定を既に使用しています。 `HighScaleApp\Views\Home\Index.cshtml` を確認します。

### <a name="complete"></a>これで完了です。

異なる地理的リージョンのブラウザーから Traffic Manager プロファイルの URL にアクセスしてみます。 ヨーロッパのクライアント ブラウザーには "ASP.NET West Europe" と表示され、アジアのクライアント ブラウザーには "ASP.NET Southeast Asia" と表示されます。

## <a name="more-resources"></a>その他のリソース

