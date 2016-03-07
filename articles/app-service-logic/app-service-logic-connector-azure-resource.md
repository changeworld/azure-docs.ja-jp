<properties
   pageTitle="Logic Apps での Azure リソース コネクタの使用 | Microsoft Azure App Service"
   description="Azure リソース コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="stepsic"/>

# Azure リソース コネクタの使用開始とロジック アプリへの追加 
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

Azure リソース コネクタを使用して、Logic App 内の Azure リソースを簡単に管理できます。

## Azure リソース コネクタの作成
Azure リソース コネクタ API アプリを使用するにはまず、そのインスタンスを作成する必要があります。インスタンスの作成には、ロジック アプリを作成するときにインラインで行う方法と、Azure Marketplace から Azure Resource Manger Connector API アプリを選択して行う方法とがあります。

インスタンスを構成するには、Azure での作業に必要な権限を持ったサービス プリンシパルを設定する必要があります。すべての呼び出しは、ここで設定したサービス プリンシパルの下で実行されます。これによってコネクタの動作を作業上必要な範囲内に限定することができます。

David Ebbo の[ブログ記事](http://blog.davidebbo.com/2014/12/azure-service-principal.html)に、この設定方法が詳しく解説されています。解説されている手順に従って、**テナント ID**、**クライアント ID**、**シークレット**を入手してください。その 3 つのフィールドと**サブスクリプション ID** が、コネクタを構成するうえで必要となります。

## Logic Apps デザイナーでの Azure リソース コネクタの使用
### トリガー
Azure リソース コネクタでは、2 つのトリガーがサポートされています。

名前 | 説明 
---- | ----------- 
Event occurs | ご利用のサブスクリプション内のリソースに対してイベントが発生したときに作用するトリガーです。 
Metric crosses threshold | メトリックが特定のしきい値を満たしたときに作用するトリガーです。

### アクション

同様に、ご利用の Azure サブスクリプション内で多数のアクションを指定することができます。
 
**リソース グループ**に関しては、次のアクションを実行できます。

名前 | 説明 
---- | -----------
List resource groups | サブスクリプションに含まれているすべてのリソース グループをリストします。
Get resource group | リソース グループをその ID で取得します。
Create resource group | リソース グループを作成または更新します。
Delete resource group | リソース グループを削除します。

**リソース**に関しては、次のアクションを実行できます。

名前 | 説明 
---- | -----------
List resources | サブスクリプションに含まれているリソースを各種フィルターでリストします。
Get resource | 単一のリソースをそのリソース ID で取得します。
Create or update resource | リソースを作成したり、既存のリソースを更新したりします。対象となるリソースのすべてのプロパティを指定する必要があります。
Resource action | その他のアクションをリソースに対して実行します。アクションの名前とそのアクションに渡されるペイロード (存在する場合) を把握しておく必要があります。
Delete resource | リソースを削除します。

**リソース プロバイダー**に関しては、次のアクションを実行できます。

名前 | 説明 
---- | -----------
List resource providers | サブスクリプション内の利用可能なリソース プロバイダーをすべてリストします。

**リソース グループ デプロイ**に関しては、次のアクションを実行できます。

名前 | 説明 
---- | -----------
List deployments | リソース グループに含まれているデプロイをすべてリストします。
Get deployment | テンプレート デプロイをその ID で取得します。
Create deployment | テンプレートを指定して新しいリソース グループ デプロイを作成します。

リソースについての**イベント**に関しては、次のアクションを実行できます。

名前 | 説明
---- | -----------
Get events | サブスクリプション内のイベントやリソースのイベントを取得します。

リソースについての**メトリック**に関しては、次のアクションを実行できます。

名前 | 説明
---- | -----------
Get metrics | リソース ID のメトリックを取得します。

## コネクタでできること
これでコネクタが作成されたため、ロジック アプリを使用してこのコネクタをビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。「[API アプリとコネクタの管理と監視](../app-service-api/app-service-api-manage-in-portal.md)」を参照してください。

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0224_2016-->