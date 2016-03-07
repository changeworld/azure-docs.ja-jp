<properties
   pageTitle="Logic Apps での SMTP コネクタの使用 | Microsoft Azure App Service"
   description="SMTP コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="rajram"/>


# SMTP コネクタの使用開始とロジック アプリへの追加
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。2015-08-01-preview スキーマ バージョンについては、こちらの [SMTP API](../connectors/create-api-smtp.md) をクリックしてください。

SMTP サーバーに接続して、電子メール (添付ファイルを含むメールを含む) を送信します。SMTP コネクタの [電子メールの送信] アクションを使用すると、指定されたメール アドレスにメールを送信できます。

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、ワークフローの一環としてデータの取得と処理のためのコネクタを提供します。この SMTP コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。


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

> [AZURE.IMPORTANT] SMTP サーバーによっては、このコネクタの動作方法によって問題が発生する場合があります (SendGrid および Gmail)。SendGrid から電子メールを送信する場合は、[GitHub リポジトリ](https://github.com/logicappsio/SendGridAPI)のカスタム API を使用して SendGrid API と直接やり取りすることができます。

## ロジック アプリで SMTP コネクタを使用する
コネクタが作成されたら、ロジック アプリのアクションとして SMTP コネクタを使用できます。これを行うには、次の手順を実行します。

1.	新しいロジック アプリを作成します。![][2]
2.	**[トリガーとアクション]** を開き、Logic Apps デザイナーを開いてワークフローを構成します。![][3]
3.	SMTP コネクタが右側のギャラリーの [このリソース グループの API Apps] セクションに表示されます。それを選択します。![][4]
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

![][5] ![][6]

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=AcomDC_0224_2016-->