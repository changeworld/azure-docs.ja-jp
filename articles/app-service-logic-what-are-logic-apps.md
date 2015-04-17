<properties 
	pageTitle="Logic Apps とは" 
	description="App Service Logic Apps ついてさらに詳しく説明します" 
	authors="joshtwist" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="jtwist"/>

#Logic Apps とは

Azure App Service は、プロの開発者のための完全に管理されたサービスとしてのプラットフォーム (PaaS) 製品であり、Web、モバイル、および統合シナリオ向けに豊富な機能を提供します。Logic Apps は、App Service スイートの一部であり、どのような専門的なユーザーや開発者も、使いやすいビジュアル デザイナーを使用してビジネス プロセスの実行を自動化できます。

何より、Logic Apps を使用すると Marketplace から API Apps やコネクタと簡単に結合できるため、注意が必要な統合シナリオの解決が容易になります。

![Flow app designer](./media/app-service-learn-about-flows-preview/Designer.png)

##SaaS、PaaS、およびハイブリッドの展開

クラウドの時代は SaaS と PaaS のシステムの使用方法に衝撃を与え、開発者の負担はすべてにおいて増大しています。その一方で、IT アプリケーションの積み残しが蓄積される時間はさらに長くなり、異種が混在する分散型のソリューションによって統合に関する新たな課題が産まれています。さらに、企業は、ソリューションの中でオンプレミスのデータとサービスを安全に活用する必要があります。

これらのシステムにわたってソリューションを構築するには時間がかかり、開発チームがミスを犯しがちな重要な課題も横たわっていています。

##なぜ Logic Apps か

Logic Apps を使用すると、開発者はトリガーと一連の手順によって、目的を組織化したワークフローを設計できます。それぞれから App Service API Apps を呼び出し、認証と、持続性のある実行のようなベスト プラクティスを確実に考慮します。

- **使いやすいデザイン ツール** - Logic Apps は、ブラウザーで全体を設計することができます。トリガーで開始 - 単純なスケジュールから会社についてのツイートが表示される各タイミングまで。次に、豊富なコネクタのギャラリーを使用して必要な数のアクションを調整します。

- **SaaS を簡単に作成** - 説明が簡単な構成タスクであっても、コードの実装は困難です。Logic Apps を使用すれば異なるシステムへの接続も簡単です。Facebook や Twitter アカウントのアクティビティに基づいて CRM ソフトウェアにタスクを作成しようとしていますか?クラウド マーケティング ソリューションをオンプレミスの請求書作成システムに接続しようとしていますか?Logic Apps は、これらの問題にソリューションを提供する、最も迅速で信頼できる手段です。

- **テンプレートから簡単に開始** - 簡単に開始できるようにするために、一般的なソリューションを迅速に作成するためのテンプレートのギャラリーを提供しています。高度な BizTalk ソリューションから単純な SaaS の接続まで、単なる  'for fun' も - ギャラリーを見れば Logic Apps の機能をすぐに理解できます。

- **拡張機能を組み込み** - 必要なコネクタが表示されていませんか?Logic Apps は、App Service スイートの一部であり、API Apps と動作するように設計されています。独自の API アプリを作成してコネクタとして簡単に使用できます。自分だけの新しいアプリを作成するか、Marketplace で共有して収益化してください。

- **真の統合処理能力** - 手軽に始めて必要に応じて拡張します。Logic Apps は、Microsoft の業界屈指の統合ソリューション、BizTalk の能力を統合担当者が簡単に利用して必要なソリューションを構築できるようにしています。[App Services に提供されている BizTalk の機能][biztalk]に関するページを参照してください。

## Logic Apps の概念

Logic Apps の機能を構成する主な要素とは次のとおりです。 

- **ワークフロー** - Logic Apps によって、ビジネス プロセスを一連の手順やワークフローとしてグラフィカルにモデル化できます。
- **コネクタ** - ロジック アプリはデータとサービスへのアクセスを必要とします。コネクタは、特殊な種類の API アプリです。コネクタは、データへの接続とデータの操作を補助するために、特別に作成されます。詳細については、[コネクタとは何か][biztalk]を説明しているページを参照してください。
- **トリガー** - トリガーとして動作するコネクタもあります。トリガーは、電子メールや Azure のストレージ アカウントの変更の到着などの特定のイベントに基づいて、ワークフローの新しいインスタンスを開始します。
- **BizTalk** - Azure App Services には、高度な統合シナリオ向けに Biztalk の機能が含まれます。Biztalk は、マイクロソフトの業界屈指の統合プラットフォームです。BizTalk API Apps によって、Logic Apps ワークフローに簡単に検証、変換、ルールなどを含めることができます。詳細については、[BizTalk API Apps とは何か][biztalk]を説明しているページを参照してください。

## 作業の開始

Logic Apps を使用するには、「[ロジック アプリを作成する][create]」のチュートリアルに従ってください。

Azure App Service プラットフォームの詳細については、[Azure App Service][appservice] に関するページを参照してください。

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md




<!--HONumber=49-->