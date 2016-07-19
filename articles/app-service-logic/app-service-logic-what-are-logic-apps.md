<properties 
	pageTitle="Logic Apps とは" 
	description="App Service Logic Apps ついてさらに詳しく説明します" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="07/12/2016"
	ms.author="klam"/>

#Logic Apps とは

| クイック リファレンス |
| --------------- |
| [Logic Apps の定義言語](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Logic Apps コネクターのドキュメント](../connectors/apis-list.md) |
| [Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Logic Apps を利用すれば、複雑なビジネス プロセスを簡素化および実装できます。ワークフローと呼ばれる一連のステップとしてビジネス プロセスをモデル化し、自動化するためのビジュアル デザイナーが用意されています。トリガーで開始され、各ステップを実行するワークフローを設計できます。各ステップでは、認証とベスト プラクティス (チェックポイント処理や永続的な実行など) に確実に対処しながら、API を呼び出します。

Logic Apps を使用する利点は次のとおりです。

- わかりやすい設計ツールを使って複雑なプロセスを設計できるため、時間を節約できる
- コードでの実装が難しいタスクも簡単に実装できる
- テンプレートを基に簡単に設計を開始できる
- API を使ってロジック アプリをカスタマイズできる
- 異なるシステムを接続できる
- Marketplace でロジック アプリを販売できる
- 手軽に開始し、必要に応じて拡張できる

Logic Apps は、ホスティング、スケーラビリティ、可用性、管理に伴う余計な作業を開発者が負担せずに済むようにする、完全に管理された iPaaS (サービスとしての統合プラットフォーム) です。

何より、Logic Apps を組み込みの[マネージ コネクタ][managedapis]と組み合わせることで、扱いが難しい統合シナリオにも簡単に対処できるようになります。

![フロー アプリ デザイナー](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

既に説明したように、Logic Apps を使うと、ビジネス プロセスを自動化できます。いくつかの例を次に示します。
 
* SQL DB の新しいレコードを自動的にレプリケートし、フロント デスクにメールを送信できます。
* ネガティブなツイートを自動的に検出し、スラック チャネルに送信できます。
* FTP サーバー上にファイルが作成されると、そのファイルを解析し、Dynamics CRM に新しいレコードを追加して、SharePoint リストにアイテムを作成します。

こうしたシナリオはすべてビジュアル デザイナーで、コードを 1 行も記述することなく構成できます。[ロジック アプリの構築を今すぐ開始][create]しましょう。

## なぜ Logic Apps か

ビジネス プロセスの自動化 (ネガティブなツイートを検出して組織内のスラック チャネルに送信する、SQL の新しい顧客レコードが到着したら、そのレコードを CRM システムにレプリケートするなど) が必要な場合、Logic Apps を使用すると、クラウドからオンプレミスに異なるデータ ソースを簡単に統合できます。[マネージ コネクタ][managedapis]について確認して何かひらめいたら、[作業を開始][create]して実際に何ができるか確かめましょう。

さらに、[エンタープライズ統合アカウント][biztalk]を使えば、[XML メッセージング][xml]や[取引先管理][tpm]などを活用して、成熟した統合シナリオへと拡張することができます。

- **使いやすいデザイン ツール** - Logic Apps は、ブラウザーで全体を設計することができます。トリガーで開始 - 単純なスケジュールから会社についてのツイートが表示される各タイミングまで。次に、豊富なコネクタのギャラリーを使用して必要な数のアクションを調整します。

- **SaaS を簡単に構成** - 簡単に説明できるような構成タスクでさえ、コードとして実装するには困難が伴います。Logic Apps を使用すれば異なるシステムへの接続も簡単です。Facebook や Twitter アカウントのアクティビティに基づいて CRM ソフトウェアにタスクを作成しようとしていますか? クラウド マーケティング ソリューションをオンプレミスの請求書作成システムに接続しようとしていますか? Logic Apps は、これらの問題にソリューションを提供する、最も迅速で信頼できる手段です。

- **テンプレートからすばやく開始** - 作業開始を手助けするため、一般的なソリューションを速やかに作成できる[テンプレート ギャラリー][templates]が用意されています。高度な BizTalk ソリューションから単純な SaaS 接続まであるうえ、単なる 'お遊び用' のものも用意しています。Logic Apps の能力を理解するには、このギャラリーが一番の近道です。

- **組み込みの拡張機能** - 必要なコネクタが見つかりませんか? Logic Apps は、API Apps と連携するように設計されています。独自の API アプリを作成してカスタム API として簡単に使用できます。自分だけの新しいアプリを作成するか、Marketplace で共有して収益化してください。

- **真の統合能力** - 手軽に始めて必要に応じて拡張できます。Logic Apps は、Microsoft の業界屈指の統合ソリューション、BizTalk の能力を統合担当者が簡単に利用して必要なソリューションを構築できるようにしています。[Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md) の詳細をご確認ください。

## Logic Apps の概念

Logic Apps の機能を構成する主な要素とは次のとおりです。

- **ワークフロー** - Logic Apps では、ビジネス プロセスを一連の手順やワークフローとしてグラフィカルにモデル化できます。
- **マネージ コネクタ** - ロジック アプリにはデータとサービスへのアクセスが必要です。マネージ コネクタは、データへの接続とデータの操作を支援する目的で作成されています。[マネージ コネクタ][managedapis]に関するページで、現在利用できるコネクタの一覧を確認してください。
- **トリガー** - 一部のマネージ コネクタは、トリガーとしても動作します。トリガーは、電子メールや Azure のストレージ アカウントの変更の到着などの特定のイベントに基づいて、ワークフローの新しいインスタンスを開始します。
-  **アクション** - ワークフローにおけるトリガーの後の各ステップは、アクションと呼ばれます。通常、各アクションはマネージ コネクタまたはカスタム API アプリでの操作にマップされます。
- **Enterprise Integration Pack** - Logic Apps には、高度な統合シナリオ向けに BizTalk の機能が含まれています。BizTalk は、Microsoft による業界屈指の統合プラットフォームです。Enterprise Integration Pack コネクタにより、Logic Apps ワークフローに検証や変換などを簡単に含めることができます。

## Getting Started (概要)  

 - Logic Apps の使用を開始するには、[ロジック アプリの作成][create]に関するページのチュートリアルに従ってください。
 - [一般的な例とシナリオを確認する](app-service-logic-examples-and-scenarios.md)
 - [Logic Apps を使用してビジネス プロセスを自動化する](http://channel9.msdn.com/Events/Build/2016/T694)
 - [Logic Apps を使用してシステムを統合する方法を説明する](http://channel9.msdn.com/Events/Build/2016/P462)
- Azure App Service プラットフォームの詳細については、[Azure App Service][appservice] に関するページを参照してください。

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md

<!---HONumber=AcomDC_0713_2016-->