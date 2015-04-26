<properties 
	pageTitle="Mobile Services .NET バックエンドの使用 - Azure Mobile Services" 
	description="テーブル データ、API、認証、スケジュールされたジョブの操作方法を含め、Azure Mobile Services 向けの .NET バックエンド プログラミング モデルの詳細を示します。" 
	services="" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="mahender"/>
# Mobile Services .NET バックエンドの使用

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">JavaScript バックエンド</a></div>

この記事では、Azure Mobile Services で .NET バックエンドを使用する方法についての詳細な情報と例を提供します。このトピックのセクションは次のとおりです。

+ [はじめに](#intro)
+ [テーブル操作](#table-scripts)

##<a name="intro"></a>はじめに

Mobile Services .NET バックエンドにより、[ASP.NET Web API](http://www.asp.net/web-api) と好みの .NET 言語を使用して、強力なバックエンド ビジネス ロジックを記述できます。Mobile Services は、[NuGet パッケージ](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22)として小規模なプログラミング モデルの一部を公開し、Windows、Android、iOS などに対応するクロスプラットフォームの Mobile Services クライアント SDK からサービスにシームレスにアクセスしやすくします。これらの API により、認証サポートとプッシュ通知サポートの追加も可能な限り容易になります。ただし、このプログラミング モデルは、ほとんどが Web API に基づいているため、Web API に慣れている開発者にとって使いやすいものとなっています。 

##<a name="table-scripts"></a>テーブル操作

Mobile Services .NET バックエンドは、データベース ストレージ用の CRUD ベースの HTTP API を表す汎用的な "テーブル" 抽象化を提供します。この抽象化では、データ ストレージの問題をビジネス ロジックから分離し、モバイル サービスがさまざまなストアを一貫した JSON ベースのワイヤ形式で公開できるようにします。この形式は、クロスプラットフォームの Mobile Services クライアント SDK によって容易に認識されます。 

このプログラミング モデルの根幹となるのは [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx) クラスです。これは単なる通常の Web API [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx) で、CRUD のデータ アクセス パターンに合わせてカスタマイズでされます。**TableController** では、SQL ([Entity Framework](http://msdn.microsoft.com/data/ef.aspx) 経由)、[Azure テーブル ストレージ](http://azure.microsoft.com/documentation/services/storage/)、[MongoDB](http://www.mongodb.org)、または独自のカスタム ストアを含む、さまざまなデータ ストアを使用できます。


<!--HONumber=42-->
