<properties 
	pageTitle="Microsoft Azure API Apps プロトコル コネクタ | API Apps マイクロサービス" 
	description="Microsoft Azure プロトコル コネクタ API アプリを作成して API アプリを Logic Apps、マイクロサービスに追加する方法を説明します。" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="Deonhe" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/> 


# Microsoft Azure App Service のプロトコル コネクタ


## プロトコル コネクタとは
プロトコル コネクタは、HTTP、SMTP、FTP などの幅広いプロトコルを使用して通信するアプリを作成するために使用できる API アプリ*です。 

Microsoft Azure ポータルで **[新規]**、**[Web + モバイル]**、**[Azure Marketplace]** の順にクリックして検索キーワード「**プロトコル**」を入力し、Enter キーを押すと、プロトコル コネクタを見つけることができます。

次のプロトコル コネクタを Azure Marketplace で入手できます。

- SFTP
- POP3
- FTP
- HTTP
- SMTP
- ファイル

ここで各プロトコル コネクタについて簡単に説明します。 

<table>
<tr>
<th> 名前</th>
<th> 説明</th>
<th> トリガー</th>
<th> actions</th>

<tr>
<td>SFTP
<td>SFTP コネクタを使用すると、SFTP サーバーからファイルをダウンロード、または SFTP サーバーへファイルをアップロードすることができます。
<td>SFTP フォルダーで新しいファイルが使用可能になる

<td>ファイル: アップロード、削除、一覧、取得 

</tr>

<tr>
<td>POP3
<td>POP3 コネクタを使用すると、POP3 サーバーから電子メールをダウンロードできます。
<td>新しいメッセージが到着する
<td>該当なし
</tr>

<tr>
<td>FTP
<td>FTP コネクタを使用すると、FTP サーバーからファイルをダウンロード、または FTP サーバーへファイルをアップロードすることができます。
<td>FTP フォルダーで新しいファイルが使用可能になる
<td>ファイル: アップロード、取得、削除、一覧
</tr>

<tr>
<td>HTTP
<td>HTTP コネクタを使用すると、HTTP または HTTPS プロトコルを使用して HTTP サーバーにデータを送信できます。
<td>なし
<td>POST、取得、削除、PUT
</tr>

<tr>
<td>SMTP
<td>SMTP コネクタを使用すると、SMTP サーバーから電子メールを送信できます。
<td>該当なし
<td>電子メールの送信
</tr>

<tr>
<td>ファイル
<td>ファイル コネクタを使用すると、オンプレミスのファイル サーバーに接続して、ファイルのアップロード、取得、削除、一覧などのアクションを実行できます。ファイルを取得するときにもトリガーがあります。
<td>デバイスに新しいファイルが追加される
<td>ファイル: アップロード、取得、削除、一覧
</tr>


</table>

プロトコル コネクタの機能の説明が終了しました。次に、これらのコネクタの簡単な使用事例をいくつか見てみましょう。

### 顧客からのフィードバックの監視 ###
会社で新しいアプリを最近リリースしたため、チームでソーシャル メディアから顧客の声を集めようとしている状況を想像してください。各チーム メンバーは、さまざまなソーシャル メディア サイトを定期的にチェックして、顧客がアプリについて話すときに使用する可能性のあるキーワードを推測できます。これをもっと手際よく実行するには、Twitter コネクタを作成し、特定のハッシュタグ、メンション、キーワードについて Twitter を監視するように構成することができます。次に SMTP コネクタを使用して、一致するツイートの内容を含む電子メールを作成してチーム メンバーに送信することができます。その後に HTTP コネクタを使用して、社内の Web サイトにそれらのツイートを投稿できます。これらの一連の作業は、コードを 1 行も記述することなく行うことができます。  

それでは始めましょう。 

## コネクタの作成

コネクタは、Azure ポータルまたは REST API を使用して作成できます。[REST API を使用してコネクタを作成する](http://go.microsoft.com/fwlink/p/?LinkId=529766)

### Azure ポータルでのコネクタの作成

Azure Marketplace を使用して SMTP コネクタを作成してみましょう。

1. Microsoft Azure [ポータル](https://manage.windowsazure.com)にログインします。
2. **[新規]**、**[Web + モバイル]**、**[Azure Marketplace]** の順に選択します。
3. **[検索ボックス]** に「**プロトコル**」と入力し、**Enter** キーを押します。一覧から選択することもできます。選択すると、新しいブレード (ウィンドウ) が開きます。**[作成]** を選択します。 
4. 次のプロパティを入力します。

<table>
<tr><th>プロパティ</th> <th>説明</th> </tr>
<tr><td>名前</td> <td>説明</td> </tr>
<tr><td>パッケージの設定</td> <td>パッケージの設定のプロパティを使用して、SMTP サーバーのユーザー名、パスワード、ポート番号、サーバー アドレスなどの認証の詳細情報をすべて入力します。 </td> </tr>
<tr><td>App Service プラン</td> <td>支払プランの一覧を表示します。プランは必要なリソースの増減に応じて変更することができます。</th> </td>
<tr><td>価格レベル</td> <td>Azure サブスクリプション内での価格のカテゴリの一覧を表示する読み取り専用プロパティです。</td> </tr>
<tr><td>リソース グループ</td> <td>新しいリソース グループを作成するか、既存のグループを使用します。リソース グループの使用に関するページに、このプロパティの説明があります。</td> </tr>
<tr><td>サブスクリプション</td> <td>現在のサブスクリプションを一覧表示する読み取り専用のプロパティ。</td> </tr>
<tr><td>場所</td> <td>Azure サービスをホストする地理的場所。</th> </td>
<tr><td>スタート画面への追加</td> <td>スタート画面 (ホーム ページ) に API アプリを追加するには、このプロパティを選択します。</td></tr>
</table> 

### REST API を使用したコネクタの作成
[REST API でのコネクタの作成](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## アプリケーションにコネクタを追加する 
Microsoft Azure App Service は、上記のコネクタが使用できるさまざまな種類のアプリケーションを公開しています。たとえば、1 つ以上のコネクタを 1 つのアプリと *logically*組み合わせることによって、 *Logic* アプリを作成できます。

 *Logic* アプリ内でコネクタを使用する場合は、構成済みのコネクタを一覧から選択してデザイン ワーク フローに追加し、必要な構成変更を行って、いつでも使用できるようにしてください。 

上記手順を実行する場合は、Web アプリ、モバイル アプリ、またはロジック アプリが必要です。具体的な方法については、<> を参照してください。アプリケーションが使用できる状態になったら、コネクタを追加します。その方法は次のとおりです。

以下の手順に従って、コネクタをロジック アプリに追加します。 

1. Azure ポータルのスタート画面 (ホーム ページ) で、**Marketplace** に移動し、ロジック アプリ、モバイル アプリ、または Web アプリを検索します。 

	新規にアプリを作成する場合は、ロジック アプリ、モバイル アプリ、または Web アプリを検索します。アプリを選択し、新しいブレードで **[作成]** を選択します。[ロジック アプリの作成] (app-service-logic-create-a-logic-app.md) に関するページに手順の一覧が記載されています。 

2. アプリを開き、**[トリガーとアクション]** を選択します。 
3. **[ギャラリー]** でコネクタを選択します。選択したコネクタがアプリに追加されます。
4. コネクタを構成します。
5. どのコネクタにも、接続しているサービスおよび環境に固有のプロパティがあります。プロパティの詳細を入力します。一部のプロパティは省略可能です。
6. **[OK]** を選択して変更を保存します。


## セキュリティ
コネクタは、OAuth か、ユーザー名とパスワードを使用します。

## Logic Apps と Web Apps に関するガイド
[Logic Apps とは?](app-service-logic-what-are-logic-apps.md) |
[Azure App Service の Websites と Web Apps](app-service-web-app-azure-portal.md) |


## その他のコネクタ

[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md) |
[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md) |
[企業間コネクタ](app-service-logic-b2b-connectors.md) |
[ソーシャル コネクタ](app-service-logic-social-connectors.md) |
[アプリおよびデータ サービス コネクタ](app-service-logic-data-connectors.md) |
[コネクタと API Apps の一覧](app-service-logic-connectors-list.md)

<!--HONumber=49-->