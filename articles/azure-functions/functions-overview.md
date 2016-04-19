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
   ms.date="03/09/2016"
   ms.author="cfowler;mahender"/>
   
   
# Azure Functions の概要

## 機能を手軽に実現する方法

どのような機能も 1 分未満で記述できます。データベースをクリーンアップする単純なジョブが必要な場合も、接続されたデバイスからの数百万件のメッセージを処理する機能を作成する必要がある場合にも便利です。好きな開発言語 (C#、Node.JS、Python など) を使用できます。コードが実行された時間に対してだけ料金を支払えばよく、必要に応じてスケールできます。

Azure Functions は、小規模なコード ("機能") をクラウドで手軽に実行できるソリューションです。目の前の問題に必要なコードだけを記述すればよく、アプリケーション全体や、コードを実行するインフラストラクチャのことを考える必要がありません。そのため、開発の生産性がさらに高まり、[最初の機能をわずか数分で作成できます](functions-create-first-azure-function.md)。

## Functions でできること

Azure Functions は、データの処理、システムの統合、モノのインターネット (IoT) の操作、単純な API とマイクロサービスの構築に適した優れたソリューションです。

Functions には次のような一般的なシナリオに対応したテンプレートのギャラリーが用意されています。

* GitHub Webhook 要求に応答する
* Azure Storage にアップロードされた画像のサイズを変更する
* 注文処理キューを操作する
* その他、さまざまなシナリオに対応したテンプレートがあります。 

Functions のしくみの詳細と構成例については、[開発者向けリファレンス](functions-reference.md)をご覧ください。

## 特徴

Azure Functions は多彩な機能を備えたエンタープライズ対応のプラットフォームであり、複雑な統合と接続タスクを容易にするさまざまな機能を提供します。この機能のコア セットにより、Azure Functions を使う開発者は、インフラストラクチャの統合ではなく本来の目的のみに集中できるため、さらに生産性が高くなります。

Azure Functions には次の機能があります。
    
* **言語の選択** - C#、Node.js、Python、F#、PHP、Batch、Bash、Java、その他実行可能な言語を使って機能を記述できます。  
* **従量課金制の価格モデル** - コードの実行に要した時間に対してだけ料金を支払えば済みます。下の[価格のセクション](#pricing)で動的 App Service プラン オプションをご覧ください。  
* **独自の依存要素の統合** - Functions は NuGet と NPM をサポートしているので、お気に入りのライブラリを使用できます。  
* **セキュリティの統合** - Azure Active Directory、Facebook、Google、Twitter、Microsoft アカウントなどの OAuth プロバイダーにより、HTTP によってトリガーされる機能を保護できます。  
* **コードなしの統合** - Azure サービスとサービスとしてのソフトウェア (SaaS) のプランを簡単に活用できます。例については、下の[統合のセクション](#integrations)をご覧ください。  
* **柔軟な開発** - 機能をポータル内のエディターで変更することも、継続的な統合を設定し、GitHub や Visual Studio Team Services などを通じてコードをデプロイすることもできます。  
* **オープン ソース** - 機能はオープン ソースであり、[GitHub](https://github.com/azure/azure-webjobs-sdk-script) で提供されています。  

### <a name="integrations"></a>統合

Azure Functions は Azure や サード パーティのサービスとの幅広い統合をサポートしています。これらを機能のトリガーとして利用し、実行を開始したり、コードの入出力として利用したりできます。以下の表に、Azure Functions によってサポートされている統合の例をいくつか示します。

[AZURE.INCLUDE [動的コンピューティング](../../includes/functions-bindings.md)]

## <a name="pricing"></a>Functions の価格

Azure Functions の実行方法には、動的 App Service プランを使用する方法と、クラシック App Service プランを使用する方法の 2 つがあります。

**動的 App Service プラン**では、リソース管理の心配は必要ありません。機能の実行時に、Azure によって必要なコンピューティング リソースがすべて提供されます。コードの実行に要した時間に対してだけ料金を支払えば済みます。価格の詳細については、[Functions の価格のページ](/pricing/details/functions)をご覧ください。

**クラシック App Service プラン**では、機能を Web アプリ、モバイル アプリ、API アプリと同様に実行できます。これは、既に App Service を他のアプリケーションに使用している場合に好都合のソリューションです。追加コストなしで、機能を同じプランで実行できます。詳細については、[App Service の価格のページ](/pricing/details/app-service/)をご覧ください。

## 問題の報告

[AZURE.INCLUDE [問題の報告](../../includes/functions-reporting-issues.md)]

<!---HONumber=AcomDC_0413_2016-->