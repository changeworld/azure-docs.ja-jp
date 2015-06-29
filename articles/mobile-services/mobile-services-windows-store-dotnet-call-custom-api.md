<properties 
	pageTitle="Windows ストア クライアントからのカスタム API の呼び出し - Mobile Services" 
	description="カスタム API を定義し、Azure Mobile Services を使用する Windows ストア アプリからそれを呼び出す方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# クライアントからのカスタム API 呼び出し

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

このトピックでは、Windows ストア アプリからカスタム API を呼び出す方法について説明します。カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

このトピックで作成したカスタム API では、テーブル内のすべての Todo 項目の完了フラグを `true` に設定する単一の POST 要求を送信できます。このカスタム API を使用せずにこれを行う場合、クライアントは、テーブル内の各 Todo 項目について個別にフラグの更新要求を送信する必要があります。

このチュートリアルは、Mobile Services のクイック スタートに基づいています。このチュートリアルを開始する前に、「[Mobile Services の使用]」または[既存のアプリケーションへの Mobile Services の追加]に関するチュートリアルを完了しておく必要があります。

## <a name="define-custom-api"></a>カスタム API を定義する

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]


## 次のステップ

このトピックでは、Windows ストア アプリから **InvokeApiAsync** メソッドを使用して、ごく簡単なカスタム API を呼び出す方法を紹介しました。**InvokeApiAsync** メソッドの詳しい使用方法については、[Azure Mobile Services でのカスタム API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) に関するページを参照してください。

また、次の Mobile Services のトピックの詳細を確認することをお勧めします。

* [Mobile Services のサーバー スクリプト リファレンス] <br/>カスタム API の作成について説明します。

* [ソース管理へのサーバー スクリプトの保存] <br/> ソース管理機能を使用して、カスタム API スクリプト コードを簡単かつ安全に開発、発行する方法について説明します。

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services の使用]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[既存のアプリケーションへの Mobile Services の追加]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md
[Define a custom API that supports periodic notifications]: mobile-services-windows-store-dotnet-create-pull-notifications.md
[ソース管理へのサーバー スクリプトの保存]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=58_postMigration-->