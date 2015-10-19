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
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="klam"/>

#Logic Apps とは

| クイック リファレンス |
| --------------- |
| [Logic Apps の定義言語](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Logic Apps コネクターのドキュメント](https://azure.microsoft.com/JA-JP/documentation/articles/app-service-logic-connectors-list/) |
| [Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/JA-JP/home?forum=azurelogicapps) |

Azure App Service は、Web アプリ、モバイル アプリ、統合アプリの作成を容易にする、開発者向けの完全に管理された PaaS (サービスとしてのプラットフォーム) です。Logic Apps はこのスイートの一部であり、どのような専門的なユーザーや開発者でも、使いやすいビジュアル デザイナーを使用してビジネス プロセスの実行とワークフローを自動化できます。

何より、Logic Apps は Marketplace の[コネクタ][connectors]と組み合わせることが可能なため、扱いが難しい統合シナリオでさえ容易に解決できるようになります。

![フロー アプリ デザイナー](./media/app-service-logic-what-are-logic-apps/Designer.png)

SQL DB の新しいレコードを自動的にレプリケートし、フロント デスクにメールする場合、または自動的にネガティブなツイートを見つけてスラック チャネルに送信する場合

##なぜ Logic Apps か

Logic Apps を使用することで、トリガーをきっかけに一連のステップを実行するワークフローの設計が可能になります。各ステップでは、認証と、チェックポイント処理や持続的な実行などのベスト プラクティスをしっかり処理しながら、App Service の API アプリを呼び出すことができます。

ビジネス プロセスの自動化 (ネガティブなツイートを見つけて組織内のスラック チャネルに送信する、SQL に新しい顧客レコードがあると、そのレコードを CRM システムにレプリケートするなど) が必要な場合は、Logic Apps を使用すると、クラウドからオンプレミスに異なるデータ ソースを簡単に統合できます。[コネクタ][connectors]について確認して何かひらめいたら、[作業を開始][create]して実際に何ができるか確かめましょう。

さらに、[BizTalk API Apps][biztalk] では、[ルール エンジン][rules]や[取引先管理][tpm]など、数多くの機能を利用して統合シナリオを拡張することができます。

- **使いやすいデザイン ツール** - Logic Apps は、ブラウザーで全体を設計することができます。トリガーで開始 - 単純なスケジュールから会社についてのツイートが表示される各タイミングまで。次に、豊富なコネクタのギャラリーを使用して必要な数のアクションを調整します。

- **SaaS を簡単に構成** - 簡単に説明できるような構成タスクでさえ、コードとして実装するには困難が伴います。Logic Apps を使用すれば異なるシステムへの接続も簡単です。Facebook や Twitter アカウントのアクティビティに基づいて CRM ソフトウェアにタスクを作成しようとしていますか? クラウド マーケティング ソリューションをオンプレミスの請求書作成システムに接続しようとしていますか? Logic Apps は、これらの問題にソリューションを提供する、最も迅速で信頼できる手段です。

- **テンプレートからすばやく開始** - 作業開始を手助けするため、一般的なソリューションを速やかに作成できる[テンプレート ギャラリー][templates]が用意されています。高度な BizTalk ソリューションから単純な SaaS 接続まであるうえ、単なる 'お遊び用' のものも用意しています。Logic Apps の能力を理解するには、このギャラリーが一番の近道です。

- **組み込みの拡張機能** - 必要なコネクタが見つかりませんか? Logic Apps は、App Service スイートの一部であり、API Apps と動作するように設計されています。独自の API アプリを作成してコネクタとして簡単に使用できます。自分だけの新しいアプリを作成するか、Marketplace で共有して収益化してください。

- **真の統合能力** - 手軽に始めて必要に応じて拡張できます。Logic Apps は、Microsoft の業界屈指の統合ソリューション、BizTalk の能力を統合担当者が簡単に利用して必要なソリューションを構築できるようにしています。[App Services で提供される BizTalk の機能][biztalk]について、詳細を確認してください。

## Logic Apps の概念

Logic Apps の機能を構成する主な要素とは次のとおりです。

- **ワークフロー** - Logic Apps では、ビジネス プロセスを一連の手順やワークフローとしてグラフィカルにモデル化できます。
- **コネクタ** - ロジック アプリにはデータとサービスへのアクセスが必要です。コネクタは、特殊な種類の API アプリです。コネクタは、データへの接続とデータの操作を補助するために、特別に作成されます。[コネクタの使用][connectors]に関するページで、現在利用できるコネクタの一覧を確認してください。
- **トリガー** - 一部のコネクタは、トリガーとしても動作します。トリガーは、電子メールや Azure のストレージ アカウントの変更の到着などの特定のイベントに基づいて、ワークフローの新しいインスタンスを開始します。
-  **アクション** - ワークフローにおけるトリガーの後の各ステップは、アクションと呼ばれます。通常、各アクションはコネクタまたはカスタム API アプリでの操作にマップされます。
- **BizTalk** - Azure App Services には、高度な統合シナリオ向けに Biztalk の機能が含まれています。Biztalk は、マイクロソフトの業界屈指の統合プラットフォームです。BizTalk API Apps によって、Logic Apps ワークフローに簡単に検証、変換、ルールなどを含めることができます。詳細については、[BizTalk API Apps とは何か][biztalk]を説明しているページを参照してください。

## Getting Started (概要)

Logic Apps の使用を開始するには、[ロジック アプリの作成][create]に関するページのチュートリアルに従ってください。

Azure App Service プラットフォームの詳細については、[Azure App Service][appservice] に関するページを参照してください。

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[connectors]: app-service-logic-connectors-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md
 

<!---HONumber=Oct15_HO2-->