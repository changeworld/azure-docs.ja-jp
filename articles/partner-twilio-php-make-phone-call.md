<properties 
	pageTitle="Twilio から通話する方法 (PHP) | Microsoft Azure" 
	description="Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。サンプルは PHP アプリケーション用です。" 
	documentationCenter="php" 
	services="" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Azure 上の PHP アプリケーションで Twilio を使用して通話する方法 

次の例では、Azure でホストされる PHP Web ページから Twilio を使用して通話する方法を示しています。次のスクリーン ショットに示すように、作成されたアプリケーションは通話に関する値の入力をユーザーに求めます。

![Twilio と PHP を使用する Azure 通話フォーム][twilio_php]

このトピックでコードを使用するためには次の操作を行う必要があります。

1. Twilio アカウントと認証トークンを取得します。Twilio を利用し始める前に、[http://www.twilio.com/pricing][twilio_pricing] で価格を検討します。試用アカウントには、[https://www.twilio.com/try-twilio][try_twilio] でサインアップできます。Twilio から提供される API の詳細については、[http://www.twilio.com/api][twilio_api] を参照してください。
2. PHP 用 Twilio ライブラリを入手します。このライブラリは GitHub ([https://github.com/twilio/twilio-php][twilio_php_github]) からダウンロードするか、PEAR パッケージとしてインストールすることができます。詳細については、[https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme] を参照してください。
3. Azure SDK for PHP をインストールします。SDK の概要とそのインストール手順については、[Azure SDK for PHP の設定に関する記事][setup_php_sdk]を参照してください。

## 通話用の Web フォームの作成

次の HTML コードは、通話用のユーザー データを取得する Web ページ (**callform.html**) の作成方法を示しています。

    <html>
	<head>
		<title>Automated call form</title>
	</head>
	<body>
	<h1>Automated Call Form</h1>
 	<p>Fill in all fields and click <b>Make this call</b>.</p>
  	<form action="makecall.php" method="post">
   	<table>
     	<tr>
       		<td>To:</td>
       		<td><input type="text" size=50 name="callTo" value=""></td>
     	</tr>
     	<tr>
       		<td>From:</td>
       		<td><input type="text" size=50 name="callFrom" value=""></td>
     	</tr>
     	<tr>
       		<td>Call message:</td>
       		<td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
     	</tr>
     	<tr>
       		<td colspan=2><input type="submit" value="Make this call"></td>
     	</tr>
   	</table>
 	</form>
 	<br/>
	</body>
	</html>

## 通話用のコードの作成
次のコードでは、**callform.html** によって表示されるフォームをユーザーが送信すると呼び出される、Web ページ (**makecall.php**) を作成する方法を示しています。次のコードによって通話メッセージが作成され、通話が生成されます(このコードで **$sid** と **$token** に設定しているプレースホルダー値は、Twilio アカウントと認証トークンに置き換えてください)。

    <html>
	<head><title>Making call...</title></head>
	<body>
	<p>Your call is being made.</p>

	<?php
	require_once 'Services/Twilio.php';

	$sid = "your_account_sid";
	$token = "your_authentication_token";

	$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
	$to_number = $_POST['callTo'];
	$message = $_POST['callText'];

	$client = new Services_Twilio($sid, $token, "2010-04-01");

	$call = $client->account->calls->create(
		$from_number, 
		$to_number,
  		'http://twimlets.com/message?Message='.urlencode($message)
	);

	echo "Call status: ".$call->status."<br />";
	echo "URI resource: ".$call->uri."<br />";
	?>
	</body>
	</html>

通話に加えて、**makecall.php** ではいくつかの通話メタデータの表示も行います (その例については次のスクリーンショットを参照)。通話メタデータの詳細については、[https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties] を参照してください。

![Twilio と PHP を使用する Azure 通話応答][twilio_php_response]

## アプリケーションの実行
次のステップでは、アプリケーションを Azure Websites にデプロイします。この後に示している記事は、Web サイトの作成と、Git、FTP、または WebMatrix によるコードのデプロイに関連しています (ただし各記事内のすべての情報が関連しているわけではありません)。

* [PHP-MySQL Azure Web サイトを作成して Git でデプロイする][website-git]
* [PHP-MySQL Azure の Web サイトを作成して FTP でデプロイする][website-ftp]。
* [WebMatrix を使用して PHP-MySQL Azure の Web サイトを作成およびデプロイする][website-webmatrix]

## 次のステップ
Azure 上の PHP で Twilio を使用した基本機能を示すために、このコードが用意されました。運用環境で Azure にデプロイする前に、エラー処理やその他の機能をさらに追加することができます。次に例を示します。

* Web フォームを使用する代わりに、Azure ストレージ BLOB または SQL Database を使用して、電話番号と通話テキストを保存できます。PHP で Azure ストレージ BLOB を使用する方法の詳細については、[PHP アプリケーションでの Azure Storage の使用に関するページ][howto_blob_storage_php]を参照してください。PHP で SQL Database を使用する方法の詳細については、[PHP アプリケーションでの SQL Database][howto_sql_azure_php]を参照してください。
* **makecall.php** コードで、Twilio から提供される URL ([http://twimlets.com/message][twimlet_message_url]) を使用して、通話の次の動作を Twilio に指示する TwiML (Twilio マークアップ言語) 応答が返されるようにします。たとえば、返される TwiML 応答に `<Say>` 動詞を含めて、通話受信者に対してテキストが読み上げられるようにできます。Twilio から提供される URL を使用する代わりに、独自のサービスを作成して Twilio の要求への応答を返すことができます。詳細については、「[PHP で音声および SMS 機能に Twilio を使用する方法][howto_twilio_voice_sms_php]」を参照してください。TwiML の詳細については、[http://www.twilio.com/docs/api/twiml][twiml] で確認できます。`<Say>` を始めとする Twilio の動詞の詳細については、[http://www.twilio.com/docs/api/twiml/say][twilio_say] で確認できます。
* また、Twilio に関するセキュリティ ガイドライン [https://www.twilio.com/docs/security][twilio_docs_security] も参照してください。

Twilio の詳細については、[https://www.twilio.com/docs][twilio_docs] を参照してください。

## 関連項目
* [PHP で音声および SMS 機能に Twilio を使用する方法](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_php]: https://github.com/twilio/twilio-php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/
[website-ftp]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/
[website-webmatrix]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-webmatrix/
[twilio_php_github]: https://github.com/twilio/twilio-php

<!---HONumber=August15_HO7-->