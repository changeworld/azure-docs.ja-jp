<properties
   pageTitle="Azure リソース コネクタ"
   description="Azure リソース コネクタ"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/16/2015"
   ms.author="stepsic"/>

#Azure リソース コネクタ
Azure リソース コネクタを使用すると、ロジック アプリ内の Azure リソースを簡単に管理できます。

##API アプリの作成
Azure リソース コネクタ API アプリを使用するにはまず、そのインスタンスを作成する必要があります。インスタンスの作成には、ロジック アプリを作成するときにインラインで行う方法と、Azure Marketplace から Azure Resource Manger Connector API アプリを選択して行う方法とがあります。

インスタンスを構成するには、Azure での作業に必要な権限を持ったサービス プリンシパルを設定する必要があります。すべての呼び出しは、ここで設定したサービス プリンシパルの下で実行されます。これによってコネクタの動作を作業上必要な範囲内に限定することができます。

David Ebbo の[ブログ記事](http://blog.davidebbo.com/2014/12/azure-service-principal.html)に、この設定方法が詳しく解説されています。解説されている手順に従って、**テナント ID**、**クライアント ID**、**シークレット**を入手してください。その 3 つのフィールドと**サブスクリプション ID** が、コネクタを構成するうえで必要となります。

##Logic Apps のデザイナー画面での Azure リソース コネクタの使用
###トリガー
Azure リソース コネクタでは、2 つのトリガーがサポートされています。

名前 | 説明 
---- | ----------- 
Event occurs | ご利用のサブスクリプション内のリソースに対してイベントが発生したときに作用するトリガーです。 
Metric crosses threshold | メトリックが特定のしきい値を満たしたときに作用するトリガーです。

###アクション

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
これでコネクタが作成されたため、このコネクタをロジック アプリを使用してビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。「[API アプリとコネクタの管理と監視](../app-service-api/app-service-api-manage-in-portal.md)」を参照してください。

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=July15_HO4-->