---
title: Twilio を使用して音声通話と SMS を実行する方法 (Python) | Microsoft Docs
description: Azure で Twilio API サービスを使用して、音声通話と SMS メッセージの送信を行う方法を説明します。 コード サンプルは Python で記述されています。
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: 6d2f2b9cd145a2472ccc9ed24fd78f30107db710
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110463811"
---
# <a name="use-twilio-for-voice-and-sms-capabilities-in-python"></a>Python で Twilio を使用して、音声通話と SMS メッセージの送信を行う
この記事では、Azure で Twilio API サービスを使用して、よくあるプログラミング作業を行う方法を説明します。 ここで取り上げるシナリオは、音声通話とショート メッセージ サービス (SMS) メッセージの送信です。 

Twilio の詳細とアプリケーションで音声と SMS を使用する方法については、「 [次の手順](#NextSteps) 」を参照してください。

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Twilio とは
開発者は Twilio を使用して、音声通話、ボイス オーバー IP (VoIP)、メッセージ送信機能をアプリケーションに組み込むことができます。 開発者は、必要なすべてのインスラストラクチャをバーチャル化してクラウドベースのグローバル環境に配置し、Twilio API プラットフォームで取り扱うことができます。 アプリケーションは構築しやすく、スケーラビリティも優れています。

Twilio のコンポーネントには次のものが含まれます。

- **Twilio Voice**: アプリケーションで音声通話の発信と受信ができます。
- **Twilio SMS**: アプリケーションでテキスト メッセージの送受信ができます。
- **Twilio Client**: あらゆるスマートフォン、タブレット、ブラウザーから VoIP の発信ができます。 WebRTC のリアルタイム通信の仕様をサポートしています。

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio の価格と特別プラン
Azure の顧客は、[特別プラン][special_offer]として、Twilio アカウントをアップグレードすると、$10 の Twilio クレジットを受け取れます。 このクレジットは、Twilio のあらゆるサービスで使用できます。 電話番号の属する地域と宛先の地域によりますが、$10 のクレジットを使用すれば、1,000 件の SMS メッセージを送信したり、1,000 分相当の音声通話を受信したりできます。

Twilio は、従量課金制サービスです。 セットアップ料金はかからず、アカウントはいつでも削除できます。 詳細については、 [Twilio の料金のページ][twilio_pricing]をご覧ください。

## <a name="concepts"></a><a id="Concepts"></a>概念
Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。 クライアント ライブラリは、複数の言語で使用できます。 [Twilio API ライブラリ][twilio_libraries]のページでリストを確認できます。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio 動詞
API では、Twilio に命令するのに動詞を使用します。ここに、いくつか例を挙げます。

* **&lt;Dial&gt;** : 呼び出し元を別の電話に接続します。
* **&lt;Gather&gt;** : 電話キーパッドに入力された数字を収集します。
* **&lt;Hangup&gt;** : 通話を終了します。
* **&lt;Pause&gt;** : 何も行わずに指定された秒数待機します。
* **&lt;Play&gt;** : 音声ファイルを再生します。
* **&lt;Queue&gt;** : 通話発信者のキューに追加します。
* **&lt;Record&gt;** : 呼び出し元の声を録音し、声が録音されたファイルの URL を返します。
* **&lt;Redirect&gt;** : 通話または SMS の制御を別の URL の TwiML に転送します。
* **&lt;Reject&gt;** : Twilio 番号への着信通話を拒否します。課金はされません。
* **&lt;Say&gt;** : テキストを音声に変換し、通話時に再生します。
* **&lt;Sms&gt;** : SMS メッセージを送信します。

[Twilio Markup Language のドキュメント][twiml]で他の動詞と機能について説明します。

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML は、Twilio 動詞を使用する XML ベースの命令の集まりであり、音声通話または SMS メッセージの処理方法を Twilio に指示します。

例として、次の TwiML では、**Hello World** というテキストを音声に変換します。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

アプリケーションで [Twilio API][twilio_api] を呼び出すときは、TwiML 応答を返す URL を API パラメーターの 1 つに指定します。 開発目的であれば、アプリケーションで TwiML 応答を行うために、Twilio が提供する URL を使用できます。 自前の URL をホストして TwiML 応答を生成することもできます。その他、`TwiMLResponse` オブジェクトを使う方法もあります。

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio アカウントを作成する
Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][try_twilio]でサインアップします。 最初に無料アカウントを取得し、後からアカウントをアップグレードできます。

Twilio にサインアップしてアカウントを取得すると、アカウント セキュリティ ID (SID) と認証トークンを受け取ります。 Twilio API 呼び出しを行うには、この両方が必要です。 自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。 アカウント SID と認証トークンは、[Twilio Console][twilio_console] の **[ACCOUNT SID]\(アカウント SID\)** 、 **[AUTH TOKEN]\(認証トークン\)** というラベルが付いた欄で見ることができます。

## <a name="create-a-python-application"></a><a id="create_app"></a>Python アプリケーションを作成する
Azure で Twilio を使用して動作する Python アプリケーションは、Twilio を使用する他の Python アプリケーションと変わりません。 Twilio のサービスは REST ベースであり、Python から呼び出す方法はいくつかありますが、この記事では、[GitHub の Python 用 Twilio ライブラリ][twilio_python]を用いて Twilio のサービスを使用する方法を説明します。 このライブラリの使用方法の詳細は、[Twilio の Python ライブラリのドキュメント][twilio_lib_docs]をご覧ください。

最初に、新しい Python Web アプリケーションのホストになる、[新しい Azure Linux 仮想マシンを設定します][azure_vm_setup]。 仮想マシンを実行できたら、公開ポートでアプリケーションを公開する必要があります。

受信の規則を追加します。
  1. [[ネットワーク セキュリティ グループ]][azure_nsg] ページに移動します。
  2. 仮想マシンに対応するネットワーク セキュリティ グループを選びます。
  3. **80 番ポート** を **送信の規則** 情報に追加します。 あらゆるアドレスによる呼び出しの受信を許可します。

DNS 名ラベルを設定します。
  1. [[パブリック IP アドレス]][azure_ips] ページに移動します。
  2. 仮想マシンに対応するパブリック IP アドレスを選択します。
  3. **[構成]** セクションで **DNS 名ラベル** の情報を設定します。 この例では、 *\<your-domain-label\>.centralus.cloudapp.azure.com* となっています。

SSH で仮想マシンに接続できたら、好みの Web フレームワークをインストールできます。 Python では [Flask](http://flask.pocoo.org/) と [Django](https://www.djangoproject.com) の 2 つが特によく知られています。 どちらも `pip install` コマンドでインストールできます。

80 番ポートでのみ通信を許可するよう仮想マシンを構成したことを覚えておいてください。 アプリケーションは、このポートを使うように構成します。

## <a name="configure-your-application-to-use-the-twilio-library"></a><a id="configure_app"></a>Twilio ライブラリを使用するようアプリケーションを構成する
Python 用 Twilio ライブラリを使用するようにアプリケーションを構成するには、次の 2 つの方法があります。

* 次のコマンドにより、Python 用 Twilio ライブラリの Pip パッケージをインストールします。
   
  `$ pip install twilio`

* [GitHub の Python 用 Twilio ライブラリ][twilio_python]をダウンロードし、このようにインストールします。

  `$ python setup.py install`

Python 用 Twilio ライブラリをインストールしたら、Python ファイルでインポートできるようになります。

`import twilio`

詳しくは、[GitHub で Twilio の readme](https://github.com/twilio/twilio-python/blob/master/README.md) をご覧ください。

## <a name="make-an-outgoing-call"></a><a id="howto_make_call"></a>発信通話する
次の例で、音声通話を発信する方法を示します。 このコードではまた、TwiML 応答を返すのに、Twilio が提供するサイトを使用しています。 `from_number` と `to_number` の電話番号は、適切な番号に入れ替えてください。 コードを実行する前に、Twilio のアカウントで、`from_number` の電話番号の確認手続きを行う必要があります。

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified.
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> 電話番号は、プラス記号と国番号を付けた形式にする必要があります。 `+16175551212` は 1 つの例です (E.164 形式)。 米国の電話番号については、`(415) 555-1212` や `415-555-1212` のように、形式を合わせていないものも Twilio で使用できます。

このコードでは、TwiML 応答を返すのに、Twilio が提供するサイトを使用しています。 独自のサイトを代わりに使用して TwiML 応答を返すこともできます。 詳しくは、「[独自の Web サイトから TwiML 応答を返す](#howto_provide_twiml_responses)」をご覧ください。

## <a name="send-an-sms-message"></a><a id="howto_send_sms"></a>SMS メッセージの送信
次の例で、`TwilioRestClient` クラスを使用して SMS メッセージを送信する方法を示します。 Twilio では、試用アカウントに対し、SMS メッセージを送信するための `from_number` 用の番号を提供しています。 コードを実行する前に、Twilio アカウントの `to_number` の番号を確認する必要があります。

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With a trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>独自の Web サイトで TwiML 応答を返す
Twilio では、アプリケーションから Twilio API の呼び出しを行うと、TwiML 応答を返すのに使用する URL に要求を送信します。 上の例では、Twilio が提供する URL [https://twimlets.com/message][twimlet_message_url] を使用しています。 

> [!NOTE]
> TwiML はTwilio で使うよう設計されていますが、ブラウザーで見ることもできます。 例えば、[https://twimlets.com/message][twimlet_message_url] にアクセスすると、空の `<Response>` 要素を見ることができます。 もう 1 つの例として、[https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] にアクセスすると、`<Say>` 要素を含む `<Response>` 要素を見ることができます。

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自のサイトを作成できます。 XML 応答を返すサイトはどの言語でも作成できます。 この記事では、Python で TwiML を作成することを想定しています。

次の例では、通話時の TwiML 応答で " **Hello World** " というテキストが読み上げられます。

Flask の場合:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Django の場合:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

上の例でも分かるように、TwiML 応答は XML ドキュメントそのものです。 Python 用 Twilio ライブラリには、TwiML を生成するクラスが用意されています。 次の例では、前に示した応答に相当するものを生成しますが、ここでは Python 用 Twilio ライブラリの `twiml` モジュールを使用します。

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

TwiML の詳細は、[TwiML の参考ドキュメント][twiml_reference]をご覧ください。

TwiML 応答を返すよう Python アプリケーションを設定したら、そのアプリケーションの URL を、`client.calls.create` メソッドに渡します。 例えば、*MyTwiML* という名前の Web アプリケーションを、Azure でホストするサービスにデプロイしているとすると、次の例に示すように、そのアプリケーションの URL を Webhook として使用できます。

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio Client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="use-additional-twilio-services"></a><a id="AdditionalServices"></a>その他の Twilio サービスを使用する
ここに示す例の他にも、Twilio では、Azure アプリケーションで Twilio の他の機能を使用できる Web ベースの API を用意しています。 詳細については、[Twilio API に関するドキュメント][twilio_api]を参照してください。

## <a name="next-steps"></a><a id="NextSteps"></a>次のステップ
これで、Twilio サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [Twilio のセキュリティ ガイドライン][twilio_security_guidelines]
* [Twilio の使い方ガイドとサンプルコード][twilio_howtos]
* [Twilio のクイックスタート チュートリアル][twilio_quickstarts]
* [GitHub 上の Twilio に関するページ][twilio_on_github]
* [Twilio に関するサポートへの連絡のページ][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/all
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[azure_ips]: ./virtual-network/virtual-network-public-ip-address.md
[azure_vm_setup]: ./virtual-machines/linux/quick-create-portal.md
[azure_nsg]: ./virtual-network/manage-network-security-group.md