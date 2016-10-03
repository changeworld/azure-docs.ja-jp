<properties
	pageTitle="Azure MFA での連絡方法として携帯電話を使用する"
	description="このページは、Azure MFA の主要な連絡方法として携帯電話を使用する方法を示します。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication での連絡方法として携帯電話を使用する

主要な連絡方法として携帯電話を使用する場合は、この記事を使用できます。通話またはテキストによる連絡方法として携帯電話を使用できるよう多要素認証を設定する方法について説明します。

## 連絡方法として携帯電話を使用するには
<ol>
<li>ドロップダウン リストから [認証用電話] を選択します。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)</center>


<li>ドロップダウン リストからお住まいの国を指定し、携帯電話番号を入力します。</li>
<li>携帯電話で使用するモード (テキストまたは通話) を選択します。</li>
<li>[次へ] をクリックします。</li>
<li>[今すぐ確認する] ボタンをクリックします。これによって、携帯電話への通話またはテキスト送信が開始されます。必ず手元に用意してください。選択したモード ([テキスト] または [通話]) によって応答が異なります。<ul><li>テキスト モードを選択した場合、6 桁のコードがテキストの形で送信されます。このコードをブラウザーに表示されるボックスに入力します。</li> <li>通話モードを選択した場合は、電話がかかってきます。電話の # 記号を使用して、呼び出しに応答します。</li></ul>
<li>[次へ] をクリックします。</li>
<li>この時点で、連絡方法が設定されました。次に非ブラウザー アプリ (Outlook 2010 以降など) のアプリ パスワードを設定します。これらのアプリを使用していない場合は、**[完了]** をクリックします。使用している場合は、次の手順に進みます。
<li>これらのアプリを使用している場合は、指定されたアプリ パスワードをコピーします。</li>

<li>クリップボードにコピーされたパスワードを、非ブラウザー アプリケーションに貼り付けます。Outlook および Lync など、個々のアプリケーションでの手順については、「電子メールのパスワードをアプリ パスワードに変更する」と「アプリケーションのパスワードをアプリ パスワードに変更する」を参照してください。</li>
<li>[Done] をクリックします。</li>



<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-phone/app.png)</center>

<!---HONumber=AcomDC_0921_2016-->