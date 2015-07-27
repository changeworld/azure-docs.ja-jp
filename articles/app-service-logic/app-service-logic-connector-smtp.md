<properties
   pageTitle="SMTP コネクタの API アプリ"
   description="SMTPConnector の使用方法"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/01/2015"
   ms.author="andalmia"/>


# SMTP コネクタ

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、ワークフローの一環としてデータの取得と処理のためのコネクタを提供します。

SMTP コネクタを使用すると、SMTP サーバーに接続し、添付ファイル付きのメールを送信するアクションを実行できます。SMTP コネクタの [電子メールの送信] アクションを使用すると、指定されたメール アドレスにメールを送信できます。

## トリガーとアクション
*トリガー*とは、発生するイベントを指します。たとえば、注文が更新された、新しい顧客が追加された、といったイベントがあります。*アクション*は、トリガーの結果です。たとえば、注文が更新されたり、新しい顧客が追加されたりすると、その新しい顧客に電子メールを送信します。

SMTP コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。現時点では、このコネクタにはトリガーはありません。

SMTP コネクタでは、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
なし | 電子メールの送信


## SMTP コネクタの作成
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. **[API Apps]** を選択し、"SMTP コネクタ" を検索します。
3. コネクタを**作成**します。
4. 名前、App Service プラン、その他のプロパティを入力します。
5. 次のパッケージの設定を入力します。

	名前 | 必須 | 説明
	--- | --- | ---
	ユーザー名 | あり | SMTP サーバーに接続できるユーザー名を入力します。
	パスワード | あり | ユーザー名のパスワードを入力します。
	サーバー アドレス | あり | SMTP サーバーの名前または IP アドレスを入力します。
	サーバー ポート | あり | SMTP サーバーのポート番号を入力します。
	SSL を有効にする | いいえ | *true* を入力すると、セキュリティで保護された SSL/TLS チャネルで SMTP を使用します。

6. **[作成]** を選択します。

## ロジック アプリで SMTP コネクタを使用する
コネクタが作成されたら、ロジック アプリのアクションとして SMTP コネクタを使用できます。これを行うには、次の手順を実行します。

1.	新しいロジック アプリを作成します。

	![][2]
2.	**[トリガーとアクション]** を開き、Logic Apps デザイナーを開いてワークフローを構成します。

	![][3]
3.	SMTP コネクタが右側のギャラリーの [このリソース グループの API Apps] セクションに表示されます。それを選択します。

	![][4]
4.	SMTP コネクタを選択すると、ワークフロー デザイナーに自動的に追加されます。

これで、SMTP コネクタを構成して、ワークフローで使用できます。**Send Email** アクションを選択し、入力プロパティを次のように構成します。

	Property | Description
	--- | ---
	To | Enter the email address of recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Enter the email address of the carbon copy recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Enter the subject of the email.
	Body | Enter body of the email.
	Is HTML | When this property is set to true, the contents of the body are sent as HTML.
	Bcc | Enter the email address of recipient(s) for blind carbon copy. Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Enter the Importance of the email. The options are Normal, Low, and High.
	Attachments | Attachments to be sent along with the email. It contains the following fields: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

	![][5]
	![][6]

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

REST API を使用した API Apps を作成します。[コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)に関するページを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=July15_HO3-->