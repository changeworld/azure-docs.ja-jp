<properties
   pageTitle="Azure Functions の概要 | Microsoft Azure"
   description="Azure Functions を利用すれば、単純な機能を数分で記述して非同期ワークロードを最適化できます。この記事では、そのしくみについて説明します。"
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/21/2016"
   ms.author="cfowler;mahender"/>
   
   
# Azure Functions の概要

このトピックでは、Azure Functions の概要について説明します。Azure Functions の利用をすぐに開始する場合は、「[初めての Azure 関数の作成](functions-create-first-azure-function.md)」を参照してください。Azure Functions の詳細な技術情報をお探しの場合は、[開発者向けリファレンス](functions-reference.md)を参照してください。

## 機能を手軽に実現する方法

どのような機能も 1 分未満で記述できます。データベースをクリーンアップする単純なジョブが必要な場合も、接続されたデバイスからの数百万件のメッセージを処理する機能を作成する必要がある場合にも便利です。好きな開発言語 (C#、Node.JS、Python など) を使用できます。コードが実行された時間に対してだけ料金を支払えばよく、必要に応じてスケールできます。

Azure Functions は、小規模なコード ("機能") をクラウドで手軽に実行できるソリューションです。目の前の問題に必要なコードだけを記述すればよく、アプリケーション全体や、コードを実行するインフラストラクチャのことを考える必要がありません。そのため開発の生産性をさらに高めることができるほか、[最初の関数はわずか数分で作成できます](functions-create-first-azure-function.md)。

## Functions でできること

Azure Functions は、データの処理、システムの統合、モノのインターネット (IoT) の操作、単純な API とマイクロサービスの構築に適した優れたソリューションです。

Functions には次のような一般的なシナリオに対応したテンプレートのギャラリーが用意されています。

* GitHub Webhook 要求に応答する
* Azure Storage にアップロードされた画像のサイズを変更する
* 注文処理キューを操作する
* その他、さまざまなシナリオに対応したテンプレートがあります。 

Functions のしくみの詳細と構成例については、[開発者向けリファレンス](functions-reference.md)を参照してください。

## 特徴

Azure Functions は多彩な機能を備えたエンタープライズ対応のプラットフォームであり、複雑な統合と接続タスクを容易にするさまざまな機能を提供します。この機能のコア セットにより、Azure Functions を使う開発者は、インフラストラクチャの統合ではなく本来の目的のみに集中できるため、さらに生産性が高くなります。

Azure Functions には次の機能があります。
    
* **言語の選択** - C#、Node.js、Python、F#、PHP、Batch、Bash、Java、その他実行可能な言語を使って関数を記述できます。  
* **従量課金制の価格モデル** - コードの実行に要した時間に対してのみ課金されます。下の[価格に関するセクション](#pricing)で動的 App Service プランのオプションをご覧ください。  
* **独自の依存関係を使用** - Functions では NuGet と NPM をサポートしているので、お気に入りのライブラリを使用できます。  
* **セキュリティの統合** - Azure Active Directory、Facebook、Google、Twitter、Microsoft アカウントなどの OAuth プロバイダーにより、HTTP によってトリガーされる関数を保護できます。  
* **コードなしの統合** - Azure サービスと、サービスとしてのソフトウェア (SaaS) のプランを簡単に活用できます。統合の例については、下の「[統合](#integrations)」セクションをご覧ください。  
* **柔軟な開発** - 関数をポータル内のエディターで変更することも、継続的な統合を設定し、GitHub や Visual Studio Team Services などを通じてコードをデプロイすることもできます。  
* **オープン ソース** - 関数はオープン ソースであり、[GitHub](https://github.com/azure/azure-webjobs-sdk-script) で提供されています。  

### <a name="integrations"></a>統合

Azure Functions は Azure や サード パーティのサービスとの幅広い統合をサポートしています。これらを機能のトリガーとして利用し、実行を開始したり、コードの入出力として利用したりできます。以下の表に、Azure Functions によってサポートされている統合の例をいくつか示します。

[AZURE.INCLUDE [動的コンピューティング](../../includes/functions-bindings.md)]

## <a name="pricing"></a>Functions の価格

Azure Functions の実行方法には、動的 App Service プランを使用する方法と、クラシック App Service プランを使用する方法の 2 つがあります。

**動的 App Service プラン**では、リソース管理について気にする必要はありません。機能の実行時に、Azure によって必要なコンピューティング リソースがすべて提供されます。コードの実行に要した時間に対してだけ料金を支払えば済みます。価格の詳細については、[Functions の価格のページ](/pricing/details/functions)をご覧ください。

**クラシック App Service プラン**では、関数を Web アプリ、モバイル アプリ、API アプリと同様に実行できます。これは、既に App Service を他のアプリケーションに使用している場合に好都合のソリューションです。追加コストなしで、機能を同じプランで実行できます。詳細については、[App Service の価格のページ](/pricing/details/app-service/)をご覧ください。

## 問題の報告

[AZURE.INCLUDE [問題の報告](../../includes/functions-reporting-issues.md)]

##次のステップ

+ [初めての Azure 関数の作成](functions-create-first-azure-function.md) Azure Functions のクイック スタートですぐに最初の関数を作成します。 
+ [Azure Functions 開発者向けリファレンス](functions-reference.md) Azure Functions ランタイムに関する詳細な技術情報と、関数のコーディングやトリガーおよびバインドの定義に関するリファレンスを提供します。
+ [Azure Functions のテスト](functions-test-a-function.md) 関数をテストするための各種ツールと手法について説明します。
+ [Azure Functions のスケーリング方法](functions-scale.md) Azure Functions で利用できるサービス プラン (動的サービス プランを含む) と、適切なプランを選択する方法について説明します。 
+ [Azure App Service とは](../app-service/app-service-value-prop-what-is.md) Azure Functions では、デプロイ、環境変数、診断などの主要な機能に Azure App Service プラットフォームを活用しています。 

<!---HONumber=AcomDC_0427_2016-->