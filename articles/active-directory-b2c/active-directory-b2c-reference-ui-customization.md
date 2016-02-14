<properties
	pageTitle="Azure Active Directory B2C プレビュー: ユーザー インターフェイス (UI) のカスタマイズ | Microsoft Azure"
	description="Azure Active Directory B2C のユーザー インターフェイス (UI) カスタマイズ機能についてのトピック"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: Azure AD B2C ユーザー インターフェイス (UI) をカスタマイズする方法

コンシューマー向けのアプリケーションでは、ユーザー エクスペリエンスが最も重要です。ユーザー エクスペリエンスは、単に悪くないだけのアプリケーションと優れたアプリケーションを分けるものであると同時に、コンシューマーがアクティブ ユーザーにとどまるか、それともコンシューマーから真のエンゲージメントを獲得できるかを分けるものでもあるからです。Azure Active Directory (AD) B2C では、コンシューマーのサインアップ ページ、サインイン ページ (*下記の注参照*)、およびプロファイルの編集ページをピクセル単位で正確に制御してカスタマイズすることができます。

> [AZURE.NOTE]
ローカル アカウントのサインイン ページ、電子メールの確認およびセルフサービスのパスワード リセットのページは現在、[会社のブランド化機能](../active-directory/active-directory-add-company-branding.md)を使用してのみカスタマイズできます。この記事で説明するメカニズムは使用できません。

この記事では、次のことについて説明します。

- ページのユーザー インターフェイス (UI) カスタマイズ機能の概要。
- サンプル コンテンツを使用してページの UI カスタマイズ機能をテストするためのヘルパー ツール。
- ページの種類ごとの基本的な UI 要素。
- この機能を実行するときのベスト プラクティス。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## ページのユーザー インターフェイス (UI) カスタマイズ機能

ページの UI カスタマイズ機能を使用すると、コンシューマーのサインアップ ページ、サインイン ページ、およびプロファイルの編集ページの外観を ([ポリシー](active-directory-b2c-reference-policies.md)を構成することによって) カスタマイズできます。Azure AD B2C サービスで提供されるページとアプリケーションの間を移動する際に、コンシューマーに一貫したユーザー エクスペリエンスを実現します。

その他のサービスでは、オプションは制限されていたり、API 経由での利用に限られていたりするのに対し、Azure AD B2C では、ページの UI のカスタマイズに最新の (わかりやすい) 手法を使用しています。しくみは次のとおりです。Azure AD B2C はコンシューマーのブラウザーでコードを実行すると共に、[クロス オリジン リソース共有 (CORS)](http://www.w3.org/TR/cors/) と呼ばれる最新の手法を使用して、ポリシーで指定した URL からコンテンツをロードします。URL はページごとに別々に指定することができます。Azure AD B2C のコンテンツ (UI 要素と呼ばれます) と URL からロードしたコンテンツをマージし、コンシューマーにページを表示する作業は、コードが担当します。このため、必要な作業は、適切な形式の HTML5 コンテンツを作成し、`<body>` 内に `<div id="api"></div>` 要素 (ここに Azure AD B2C のコンテンツとマージするコンテンツを記述します) を配置するだけです。それが終わったら、(CORS が有効な) HTTPS エンドポイントでコンテンツをホストします。Azure AD B2C UI 要素全体をスタイル設定することもできます。

## UI カスタマイズ機能を試す

Azure BLOB ストレージでホストされるサンプルの HTML と CSS のコンテンツを使用する UX カスタマイズ機能を試すことができるように、静的なコンテンツをアップロードして構成するための[単純なヘルパー ツール](active-directory-b2c-reference-ui-customization-helper-tool.md)が用意されています。

## ページの種類ごとの基本的な UI 要素

以降のセクションで、Azure AD B2C がコンテンツ内に配置された <div id="api"></div> 要素にマージする HTML5 フラグメントを (ページの種類ごとに) 示します。これらの UI 要素は独自のスタイル シートを使用してカスタマイズできます。これらのスタイル シートによって、<head> フラグメントの各ページに追加された既定のスタイル シートを上書きする必要があります。

> [AZURE.IMPORTANT]
	プレビューの間は、ユーザーのフィードバックに基づいて改善が施されるため、実際の UI 要素は変更になる可能性があります。常に最新の更新プログラムの既定のページのソース コードを調べてください。実際に考えられる最初の変更は、既定のスタイル シートの削除です。これは、コンテンツ内の UI 要素のための独自のスタイル シートを常に用意する必要があることを意味します。

## ID プロバイダーの選択ページ

このページには、サインアップ時やサインイン時にユーザーが選択できる ID プロバイダーの一覧が含まれます。この一覧には、Facebook や Google+ などのソーシャル ID プロバイダーとローカル アカウント (電子メール アドレスやユーザー名を使用するもの) の両方が含まれます。

```HTML

<div id="api" data-name="IdpSelections">
	<div class="intro">
         <p>Sign up</p>
	</div>
	
	<div>
		<ul>
			<li>
				<button class="accountButton" id="FacebookExchange">Facebook</button>
			</li>
			<li>
				<button class="accountButton" id="GoogleExchange">Google+</button>
			</li>
			<li>
				<button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
			</li>
		</ul>
	</div>
</div>

```

## ローカル アカウントのサインアップ ページ

このページには、ユーザーが電子メールアドレスやユーザー名を使ったローカル アカウントでサインアップするときに入力が必要になるサインアップ フォームが含まれます。このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン、複数選択チェック ボックスなどのさまざまな入力コントロールを含めることができます。

```HTML

<div id="api" data-name="SelfAsserted"> 
	<div class="intro"> 
		<p>Create your account by providing the following details</p>
	</div>
	
	<div id="attributeVerification"> 
		<div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
		<div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
		<div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
		<div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
		<div class="attr" id="attributeList"> 
			<ul>
				<li> 
					<div class="attrEntry validate">
						<div> 
							<div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
							<div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
							<div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
							<div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
							<div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
							<div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
							<div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
						</div>

					<div class="helpText show">This information is required</div>
						<label>Email</label>
						<input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

					<div class="buttons verify" claim_id="email"> 
						<div id="email_ver_wait" class="working" style="display: none;"></div>
							<label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label> 
							<input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
							<button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
							<button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
							<button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
							<button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
							<button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button> 
						</div>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
						<label>Enter password</label>
						<input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ "; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"> This information is required</div>
						<label>Reenter password</label>
						<input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Name</label>
						<input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Gender</label>
						<input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
						<label for="extension_Gender_F">Female</label>
						<input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
						<label for="extension_Gender_M">Male</label>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Loyalty number</label>
						<input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>State</label>
						<select class="dropdown_single" id="state">
							<option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
							<option value="WA">Washington</option>
							<option value="NY">New York</option>
							<option value="CA">California</option>
						</select>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Zip code</label>
						<input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
					</div>
				</li>
			</ul> 
		</div>
		<div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
	</div>
	<div class="verifying-modal"> 
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="verifying_blurb"></div>
	</div>
</div>

```

## ソーシャル アカウントのサインアップ ページ

このページには、Facebook や Google+ などのソーシャル ID プロバイダーから提供された既存のアカウントを使用してサインアップするときに、コンシューマーが入力する必要のあるサインアップ フォームが含まれます。このページは、パスワード入力フィールドを除いて、ローカル アカウントのサインアップ ページ (前のセクションを参照) とほぼ同じです。

## 多要素認証ページ

このページでは、ユーザーがサインアップやサインインをするときに、電話番号を (文字や音声を使用して) 確認することができます。

```HTML

<div id="api" data-name="Phonefactor">
	<div id="phonefactor_initial">
		<div class="intro">
			<p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
		</div>
		<div class="errorText" id="errorMessage" style="display:none"></div>
		<div class="phoneEntry" id="phoneEntry">
			<div class="phoneNumber">
				<select id="countryCode" style="display:inline-block">
					<option value="+93">Afghanistan (+93)</option>
					<!-- Not all country codes listed -->
					<option value="+44">United Kingdom (+44)</option>
					<option value="+1" selected="">United States (+1)</option>
					<!-- Not all country codes listed -->
				</select>
			</div>
			<div class="phoneNumber">
				<input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
			</div>
		</div>
		<div id="codeVerification" style="display:none">
			<div>
				<p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
				<input type="text" id="verificationCode" placeholder="Verification code">
			</div>
		</div>
		<div class="buttons">
			<button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
			<button id="verifyPhone" style="display:inline-block">Call Me</button>
			<button id="cancel" style="display:inline-block">Cancel</button>
		</div>
	</div>
	<div class="dialing-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="dialing_blurb"></div><div id="dialing_number"></div>
	</div>
</div>

```

## エラー ページ


```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
	<h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
	<div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
	<div class="error-page-messagedetails">
		<div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
		<div>Timestamp:2015-09-14 23:22:35Z</div>
		<div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
	</div>
</div>

```

## 独自のコンテンツを構築する際の注意点

ページの UI カスタマイズ機能を使用する場合には、以下のベスト プラクティスを確認してください。

- Azure AD B2C の既定のテンプレートをコピーしたり、変更したりしないでください。HTML5 コンテンツは最初から構築し、既定のテンプレートはあくまで参考としての利用にとどめることをお勧めします。
- セキュリティ上の理由から、コンテンツに JavaScript を含めることはできません。必要なものの多くは既に用意されているはずです。必要なものがない場合には、[ユーザーの声](http://feedback.azure.com/forums/169401-azure-active-directory)を使用して新しい機能を要求してください。
- サポートされているブラウザーのバージョン:
	- Internet Explorer 11
	- Internet Explorer 10
	- Internet Explorer 9 (制限あり)
	- Internet Explorer 8 (制限あり)
	- Google Chrome 43.0
	- Google Chrome 42.0
	- Mozilla Firefox 38.0
	- Mozilla Firefox 37.0

<!---HONumber=AcomDC_0204_2016-->