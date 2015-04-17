<properties 
	pageTitle="API Apps とは" 
	description="Azure App Service が RESTful API を開発、発行、ホストするために最適なプラットフォームである理由について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tdykstra"/>

# API Apps とは

## 概要

Azure App Service は、プロフェッショナル開発者向けの完全に管理されたコンピューティング プラットフォームで、Web、モバイル、統合のシナリオのための機能セットが豊富に用意されています。API Apps は App Service スイートの一部であり、これを使用するすべての技術ユーザーや開発者は、機能が豊富でスケーラブルかつグローバルに使用できる最新のクラウド プラットフォームで API および SaaS コネクタを検出、ホスト、管理、および収益化することができます。

## API Apps を使う理由

Azure App Service の API Apps を使用すると、API の開発、発行、管理、および収益化が容易になるためです。

- **API を現状のまま使用** - API には、ASP.NET、Java、PHP、Node.js、または Python を使用します。API では、変更なしで Azure App Service の機能を活用できます。

- **アプリを一般的な SaaS プラットフォームに接続** - Azure App Service を使用すると、Salesforce、Office 365、Twitter、Facebook、Dropbox など一般的な SaaS プラットフォームに容易に接続することができます。

- **単純なアクセス制御** - API は、Azure App Service 内の他のアプリケーションまたはパブリックに公開することができます。さらに、コードを変更せずに、Azure Active Directory またはサードパーティのサービスによる認証を追加することもできます。

- **API の公開および組織ギャラリー** - API の独自のプライベート組織ギャラリーを使用すると、API を組織の他のチームと容易に共有することができます。また、サードパーティの開発者が使用できるように API をパブリックに共有することもできます。

- **SDK の自動生成** - Azure App Service では、C#、Java、Javascript などさまざまな言語で SDK を自動的に生成できるため、多くのプラットフォームで API を利用することができます。

- **市場で最も優れた IDE を使用** - [Visual 
Studio](/campaigns/visual-studio-2013/) との統合により、API の作成、デバッグ、パッケージ化、発行が簡素化され、アプリケーションのライフサイクル全体を管理できます。

<!--このセクションのサイズを減らすために、指示に応じて削除
- **操作不要** - 修正プログラムが自動的に適用される高可用性環境で API アプリを実行します。Azure App Service を使用してデプロイされた API アプリは独立しており、ユーザーのアプリケーション専用の VM でホストされます。これにより、パフォーマンスを予測することができ、セキュリティの分離が実現されます。

- **自動スケーリング** - Azure App Service を使用すると、受け取ったすべてのユーザーの負荷を処理するために、迅速にスケールアップまたはスケールアウトできます。VM の数とサイズを手動で選択するか、
[auto-scaling](/documentation/videos/auto-scaling-azure-web-sites/)
 を設定して、負荷またはスケジュールに応じてサーバーをスケーリングします。
-->

- **オンプレミスのデータへのアクセス** - API アプリでは、[ハイブリッド接続](integration-hybrid-connection-overview.md)と [VNET](web-sites-integrate-with-vnet.md) を使用して、自社のデータ センター内のデータを使用することができます。

- **円滑なデプロイ** - Visual Studio Online、GitHub、TeamCity、Hudson、または BitBucket との継続的な統合とデプロイのワークフローを設定します。これにより、コードのチェックインまたは統合テストが成功するたびに、API アプリを自動的にビルド、テスト、デプロイすることができます。

## API Apps の概念 ##

- **API Apps ギャラリー** - 増加し続ける既存の API アプリ テンプレートの一覧から選択します。
- **コネクタ** - API アプリの一種で、Salesforce、Office 365 など SaaS プラットフォームへの接続を容易にします。
- **ゲートウェイ** - API の管理機能を処理する Web アプリで、リソース グループのすべての API アプリの認証などを行います。 
- **自動スケーリング** - API アプリは、受け取ったすべての顧客の負荷を処理するために、自動的にスケールアップまたはスケールアウトすることができます。VM の数とサイズを手動で選択するか、auto-scaling を設定して、負荷またはスケジュールに応じてサーバーをスケーリングします。
- **継続的な統合** - VSO、GitHub、TeamCity、Hudson、または BitBucket との継続的な統合とデプロイのワークフローを設定します。これにより、コードのチェックインまたは統合テストが成功するたびに、Web アプリを自動的にビルド、テスト、デプロイすることができます。

## 使用の開始

API アプリの使用を開始するには、[API アプリの作成](app-service-dotnet-create-api-app.md)に関するチュートリアルに従ってください。

Azure App Service プラットフォームの詳細については、[Azure App Service](app-service-value-prop-what-is.md) に関するトピックを参照してください。

<!--HONumber=49-->