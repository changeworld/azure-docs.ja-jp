<properties
   pageTitle="Logic Apps での HTTP リスナーとコネクタの使用 | Microsoft Azure App Service"
   description="HTTP リスナーと HTTP アクションのコネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="prkumar"/>


# HTTP リスナーと HTTP アクションの使用を開始し、ロジック アプリにこれらを追加する
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

HTTP リソースに直接接続して、HTTP 要求をリッスンし、HTTP Web 要求を構成します。次のような一部のシナリオで、直接の HTTP 接続が必要になる場合があります。

1.	Web ユーザーまたはモバイル ユーザーの対話型のフロント エンドをサポートするロジック アプリを開発する。
2.	すぐに使用できるコネクタがない Web サービスからデータを取得して、処理する。
3.	Web サービスとして既に公開されているが、API アプリとしては利用できないアクションを実行する。

これらのシナリオに対して、2 つのオプションが用意されています。

1. **HTTP リスナー**: このコネクタは、トリガーとして機能し、構成されたエンドポイントで HTTP 要求をリッスンします。呼び出しが、構成されたエンドポイントで受信されると、このコネクタはフローの新しいインスタンスをトリガーし、処理のために、要求された受信データをフローに渡します。また、このコネクタは、フローが開始したときに受信要求に対して自動的に応答するか、またはフローの実行に合わせて応答を作成し、呼び出し元に応答を送信するように構成することもできます。
2. **HTTP アクション**: このアクションでは、インターネットで使用可能な任意のエンドポイントへの Web 要求を構成し、応答を取得して、使用するフローで他のアクションがその応答を使用できるようします。

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。この HTTP コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。

## Logic App の HTTP リスナーの作成
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "HTTP" を検索し、[HTTP リスナー] を選択して、**[作成]** を選択します。
3.	次のように、HTTP リスナーを構成します。![][1]

4.	パッケージの設定をセットアップするときに、次のオプションが表示され、リスナーが自動的に応答する必要があるのか、またはユーザーが明示的な応答を送信する必要があるのかを選択できます。これを **False** に設定し、ユーザー自身の応答を送信します。![][2]

5.	**[OK]** をクリックして作成します。
6.	API アプリのインスタンスが作成されたら、この設定を開き、セキュリティを構成します。現在、HTTP リスナーは基本認証をサポートします。HTTP リスナーを開くと、[セキュリティ] オプションを使用してセキュリティを構成できます。![][3]
  
	**既知の問題** *セキュリティ設定には既定値の [なし] が表示されていますが、まだ未定義の状態です。HTTP リスナーを正しく構成するには、保存する前にこの設定をいったん [基本] に変更して再度 [なし] に戻す必要があります。*

7. 最後に、外部のクライアントがエンド ポイントにアクセスできるように、API アプリのセキュリティ設定を [パブリック(匿名)] に設定します。この設定は HTTP リスナー API アプリの [すべての設定] > [アプリケーションの設定] にあります。![][10]

完了すると、HTTP リスナーを使用するためのロジック アプリを作成できます。

## ロジック アプリでの HTTP リスナーの使用
API アプリが作成されると、ロジック アプリのトリガーとして HTTP リスナーを使用できます。そのためには、次の手順を実行する必要があります。

4.	新しいロジック アプリを作成します。
5.	[トリガーとアクション] を選択して Logic Apps デザイナーを開き、フローを構成します。HTTP リスナーがギャラリーの一覧に表示されます。それを選択します。
6.	これで、HTTP メソッドと、フローをトリガーするリスナーを必要とする相対 URL を設定できます。![][4] ![][5]

7.	完全な URI を取得するには、HTTP リスナーをダブルクリックしてその構成設定を表示し、API アプリの "ホスト" の URL をコピーします。![][6]
8.	これで、HTTP 要求で受信したデータを、次のようにフローのその他のアクションで使用できます。![][7] ![][8]
9.	最後に、応答を送信するには、もう一つの HTTP リスナーを追加して、HTTP 応答の送信アクションを選択します。要求 ID に HTTP リスナーから取得した要求 ID を設定して、返す対象の応答の本文と HTTP ステータスに入力します。![][9]

## HTTP アクションの使用
HTTP アクションは、Logic Apps がネイティブでサポートしており、使用可能にするために最初に API アプリを作成する必要はありません。ロジック アプリの任意の場所に HTTP アクションを挿入し、呼び出しの URI、ヘッダーおよび本文を選択できます。HTTP アクションはクライアント側のセキュリティの複数のオプションをサポートします。[クライアント側のセキュリティ オプションに関するページ](../scheduler/scheduler-outbound-authentication.md)を参照してください。

HTTP アクションの出力は、ヘッダーと本文です。これらは、その他のアクションとコネクタの出力を使用する場合と同様、フローのさらに下流で使用することができます。

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

> [AZURE.NOTE] Azure アカウントにはサインアップせずに Azure Logic Apps を体験することもできます。「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」にアクセスしてください。App Service で短時間有効な簡易版のロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png

<!---HONumber=AcomDC_0224_2016-->