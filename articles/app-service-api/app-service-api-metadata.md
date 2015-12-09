<properties
	pageTitle="API の検出とコードの生成用の App Service API Apps メタデータ | Microsoft Azure"
	description="Azure App Service の API Apps で Swagger メタデータを使用して、API の検出とコードの生成を簡単にする方法について説明します。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# API の検出とコードの生成用の App Service API Apps メタデータ 

[Swagger 2.0](http://swagger.io/) API メタデータのサポートは、App Service API Apps に組み込まれています。Swagger を使用する必要はありませんが、使用すると、検出と使用をより簡単にする API Apps の機能を活用できます。

## Swagger のエンドポイント

API アプリのプロパティでは、その API アプリの Swagger 2.0 JSON メタデータを提供するエンドポイントを指定できます。このエンドポイントは、API アプリのベース URL、または絶対 URL を基準に指定できます。絶対 URL は、API アプリの外部を示します。

この URL はパブリックにアクセスできる必要があります (ユーザーまたはサービスの認証によって保護されていません)。

[Azure ポータル](https://portal.azure.com/)の **[API の定義]** ブレードでは、エンドポイントの URL を確認および変更できます。

![](./media/app-service-api-metadata/apidefblade.png)

Visual Studio を使用して API アプリを作成すると、API 定義のエンドポイントは、自動的に、その API アプリのベース URL に `/swagger/docs/v1` が追加されたものに設定されます。これは既定の URL で、ASP.NET Web API プロジェクトの動的に生成された Swagger メタデータを提供するために [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet パッケージで使用されます。

## コードの生成

Azure API アプリに Swagger を統合する利点の 1 つは、自動コード生成です。生成されたクライアント クラスにより、API アプリを呼び出すコードの記述が容易になります。

API アプリのクライアント コードは、Visual Studio を使用して生成するか、コマンド ラインから生成することができます。Visual Studio で ASP.NET Web API プロジェクトのクライアント クラスを生成する方法については、[API Apps と ASP.NET の使用](app-service-api-dotnet-get-started.md#codegen)に関するページを参照してください。サポートされているすべての言語のコマンド ラインからこの操作を実行する方法については、GitHub.com の [Azure/autorest](https://github.com/azure/autorest) リポジトリにある readme ファイルを参照してください。
 
## 次のステップ

API アプリの作成、デプロイ、使用の手順を説明するステップ バイ ステップ チュートリアルについては、[Azure App Service での API アプリの使用](app-service-api-dotnet-get-started.md)に関するページを参照してください。

<!---HONumber=AcomDC_1203_2015-->