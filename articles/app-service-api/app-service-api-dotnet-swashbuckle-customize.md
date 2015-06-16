
<properties 
	pageTitle="Swashbuckle で生成された操作 ID のカスタマイズ" 
	description="Azure App Service で API アプリに対して Swashbuckle によって生成される Swagger 操作 ID をカスタマイズする方法を説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# Swashbuckle で生成された操作 ID のカスタマイズ 

## 概要

Swashbuckle は、コントローラー名とメソッド名を連結することによって、Swagger 操作の ID を生成します。このパターンでは、メソッドに複数のオーバーロードがある場合に問題が発生します。Swashbuckle は重複する操作 ID を生成し、これは無効な Swagger JSON です。

たとえば、次のコントローラー コードでは、Swashbuckle は 3 つの Contact_Get 操作 ID を生成します。

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

メソッドに一意の名前を付けることによって、手動で問題を解決できます。この例ではたとえば次のようにします。

* 取得
* GetById
* GetPage

あるいは、一意の操作 ID を自動的に生成するように Swashbuckle を拡張します。この記事ではその方法を示します。

## Swashbuckle の拡張 

次の手順では、Visual Studio API Apps Preview プロジェクト テンプレートによってプロジェクトに組み込まれる *SwaggerConfig.cs* ファイルを使用して Swashbuckle をカスタマイズする方法を示します。または、API アプリとしてのデプロイ用に構成する Web API プロジェクトで Swashbuckle をカスタマイズすることもできます。

1. `IOperationFilter` のカスタム実装の作成 

	`IOperationFilter` インターフェイスは、Swagger メタデータ プロセスのさまざまな部分をカスタマイズする Swashbuckle ユーザーに機能拡張ポイントを提供します。次のコードでは、操作 ID 生成動作を変更する 1 つの方法を示します。このコードは、操作 ID 名にパラメーター名を追加します。

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. *App_Start\SwaggerConfig.cs* ファイルで、`OperationFilter` メソッドを呼び出し、Swashbuckle が `IOperationFilter` の新しい実装を使うようにします。

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Swashbuckle NuGet パッケージに含まれる *SwaggerConfig.cs* ファイルでは、多くの機能拡張ポイントの例がコメント化されています。他のコメントはここでは示しません。

	この変更を行った後は、`IOperationFilter` の実装を使用して一意の操作 ID が生成されます。
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

## 次のステップ

この記事では、一意の操作 ID を生成するように Swashbuckle をカスタマイズする方法を説明しました。詳細については、[GitHub の Swashbuckle](https://github.com/domaindrivendev/Swashbuckle) を参照してください。

<!---HONumber=58--> 