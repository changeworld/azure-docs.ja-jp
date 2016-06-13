<properties
   pageTitle="Logic Apps デプロイ テンプレートを作成する | Microsoft Azure"
   description="Logic Apps デプロイ テンプレートの作成方法とリリース管理のための利用方法について説明します。"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/25/2016"
   ms.author="jehollan"/>
   
# Logic Apps デプロイ テンプレートを作成する

ロジック アプリが作成されたら、それを Azure リソース テンプレートとして作成すると便利です。あらゆる環境や必要なリソース グループに ロジック アプリを簡単にデプロイできます。Resource Manager テンプレートの概要は、「[Authoring Azure Resource Manager template (Azure Resource Manager テンプレートを作成する)](../resource-group-authoring-templates.md)」と「[Deploy resources with Azure Resource Manager template (Azure Resource Manager テンプレートでリソースをデプロイする)](../resource-group-template-deploy.md)」で確認できます。

## ロジック アプリ デプロイ テンプレートの概要

ロジック アプリは 3 つの基本コンポーネントで構成されています。

* **ロジック アプリ リソース** - 価格設定計画、場所、ワークフロー定義などの情報が含まれているリソース。
* **ワークフロー定義** - これは**コード ビュー**を選択したときに表示されます。フローとエンジンの実行手順の定義です。これは Logic App リソースの `definition` プロパティです。
* **接続** - 接続文字列やアクセス トークンなど、コネクタ接続関連のメタデータを安全に保存するための別個のリソース。Logic App では、Logic App リソースの`parameters`として参照されます。

以上のコンポーネントを既存の Logic Apps で参照する場合、[Azure リソース エクスプローラー](http://resources.azure.com)などのツールを利用できます。

リソース グループ デプロイで機能する Logic App のテンプレートを作成するには、リソースを定義し、必要に応じてパラメーター化する必要があります。たとえば、開発、テスト、運用の環境にデプロイする場合、環境ごとに異なる接続文字列を SQL データベースに使用するか、異なるサブスクリプションやリソース グループ内でデプロイすると効果的です。

## ロジック アプリ デプロイ テンプレートの作成

ロジック アプリ デプロイ テンプレートの作成に便利なツールがいくつかあります。上記のリソースを取得し、必要に応じてパラメーターを作成する方法で手作業作成することはできます。また、[Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) という PowerShell モジュールを利用することもできます。オープン ソース モジュールは、Logic App とそれが利用している接続を評価し、デプロイに必要なパラメーターと共にテンプレート リソースを生成することで機能します。たとえば、Logic App が Service Bus キューからメッセージを受信し、SQL Azure データベースにデータを追加した場合、このツールはすべてのオーケストレーション ロジックを保存し、デプロイ時に設定できるように、SQL と Service Bus 接続文字列をパラメーター化します。

>[AZURE.NOTE] 接続は Logic App と同じリソース グループ内に置く必要があります。

### Logic App Template PowerShell モジュールのインストール

最も簡単なインストール方法は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)とコマンド `Install-Module -Name LogicAppTemplate` を使用することです。

手動でインストールすることもできます。

1. [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases) の最新リリースをダウンロードします。  
1. PowerShell モジュール フォルダー (通常は、`%UserProfile%\Documents\WindowsPowerShell\Modules`) にフォルダーを解凍します。

モジュールがあらゆるテナントやサブスクリプション アクセス トークンで機能するように、[armclient](https://github.com/projectkudu/ARMClient) コマンド ライン ツールの使用が推奨されます。`armclient` の詳細は[ここ](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)で確認できます。

### PowerShell で Logic App テンプレートを生成する

インストール後、次のコマンドでテンプレートを生成できます。

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` は Azure サブスクリプション ID です。この行は最初に `armclient` からアクセス トークンを取得します。それを PowerShell スクリプトにパイプ処理し、テンプレートを `.json` ファイルに出力します。

## Logic App テンプレートにパラメーターを追加する

Logic App テンプレートが用意されたら、必要に応じてパラメーターを追加したり、変更したりできます。たとえば、定義により、1 回のデプロイでデプロイを予定している Azure Functions または入れ子になったワークフローにリソース ID が追加される場合、テンプレートにリソースを追加し、必要に応じて ID をパラメーター化すると便利です。カスタム API や Swagger エンドポイントの参照をリソース グループごとにデプロイする予定の場合がそうです。

## Logic App テンプレートをデプロイする

テンプレートが用意されたら、PowerShell、REST API、Visual Studio、リソース管理、Azure ポータル テンプレート デプロイなど、さまざまなツールでデプロイできます。デプロイの詳細については[ここ](../resource-group-template-deploy.md)で確認できます。パラメーターの値を保存する[パラメーター ファイル](../resource-group-template-deploy.md#parameter-file)の作成も推奨されます。

### OAuth 接続を作成する

デプロイ後、Logic App は有効なパラメーターでエンドツーエンドで機能しますが、有効なアクセス トークンを生成するには、OAuth 接続を認証する必要があります。具体的には、デザイナーで Logic App を開き、接続を認証します。あるいは、自動化する場合、各 OAuth 接続に同意するスクリプトを利用することもできます。スクリプトのサンプルは GitHub の [Connection Auth](https://github.com/logicappsio/LogicAppConnectionAuth) プロジェクトにあります。

## Visual Studio リリース管理を使用する

環境をデプロイし、管理する一般的なシナリオは、Visual Studio リリース管理のようなツールを ロジック アプリ デプロイ テンプレートと共に使用することです。VSTS に含まれる [Deploy Azure Resource Group (Azure リソース グループのデプロイ)](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) タスクは、あらゆるビルドまたはリリース パイプラインに追加できます。認証には[サービス プリンシパル](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)をデプロイする必要があります。その後、リリース定義を生成できます。

1. **[新しいリリース定義の作成]** で**[空]**を選択して**新しい定義**を開始します。

    ![][1]

1. これに必要なアーティファクトを選択します。多くの場合、手作業で生成したか、ビルド プロセスで生成された Logic App テンプレートを選択します。
1. **Azure Resource Group Deployment (Azure リソース グループ デプロイ)** タスクを追加します。
1. [サービス プリンシパル](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)で構成し、**テンプレート** ファイルと**テンプレート パラメーター** ファイルを参照します。
1. 必要に応じて、その他の環境、自動化されたテスト、承認者のために、リリース プロセスの手順の構築を続行します。
    
<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

<!---HONumber=AcomDC_0601_2016-->