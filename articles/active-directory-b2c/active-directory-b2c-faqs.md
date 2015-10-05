<properties
	pageTitle="Azure Active Directory B2C プレビュー: FAQ | Microsoft Azure"
	description="Azure Active Directory B2C についてよく寄せられる質問"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: FAQ

このページには、Azure Active Directory (AD) B2C プレビューに関してよく寄せられる質問への回答が記載されています。常に最新情報をチェックしてください。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### 既存の従業員ベースの Azure AD ディレクトリで Azure AD B2C を使用できますか。

現在のことろ、既存の Azure AD ディレクトリで Azure AD B2C 機能を有効にすることはできません。Azure AD B2C 機能を使用するための別個のディレクトリを作成し、お客様を管理することをお勧めします。

### Azure AD B2C を使用してソーシャル ログイン (Facebook および Google+) を Office 365 に提供することはできますか。

Azure AD B2C は Office 365 と共に使用することはできません。一般に、SaaS アプリ (Salesforce、Workday など) に認証を提供するために使用することはできません。これは、消費者向けの Web およびモバイル アプリケーションの ID およびアクセス管理を提供するものであり、従業員やパートナー向けではありません。

### Azure AD B2C の "ローカル アカウント" とは何ですか。 それらは、Azure AD の "職場または学校のアカウント" とはどのような点が異なるのですか。

Azure AD ディレクトリでは、ディレクトリ内のすべてのユーザー (既存の Microsoft アカウントを持つユーザーを除く) は、`<xyz>@<directory domain>` という形式の電子メール アドレスを使用してサインインします。`<directory domain>` はディレクトリ内のいずれかの有効なドメイン、または初期 `<...>.onmicrosoft.com` ドメインです。このタイプのアカウントは "職場または学校のアカウント" であり、"組織アカウント" とも呼ばれます。

Azure AD B2C ディレクトリでは、ユーザーは大部分のアプリに任意の電子メール アドレス (joe@comcast.net、bob@gmail.com、sarah@contoso.com、jim@live.com) など) を使用してサインインします。このタイプのアカウントは "ローカル アカウント" です。現在、ローカル アカウントとして任意のユーザー名 (単なるプレーン文字列) もサポートしています (joe、bob、sarah、jim など)。Azure AD B2C サービスでは、これらの 2 つの "タイプ" のローカル アカウントからいずれかを選択できます。

### 現在サポートされているソーシャル ID プロバイダーはどれですか。 将来サポートする予定のプロバイダーはどれですか。

現在、Facebook、Google+、LinkedIn、および Amazon をサポートしています。お客様のご要望に基づいて、Microsoft アカウントやその他の人気のあるソーシャル ID プロバイダーのサポートを追加する予定です。

### さまざまなソーシャル ID プロバイダーからお客様に関する情報をさらに収集するために "スコープ" を構成できますか。

いいえ。ただし、この機能は検討中です。サポートされている一連のソーシャル ID プロバイダーに使用されている、既定のスコープは次のとおりです。

- Facebook: 電子メール
- Google +: 電子メール
- Amazon: プロファイル
- LinkedIn: r\_emailaddress r\_basicprofile

### 使用しているアプリケーションを Azure AD B2C で機能させるには、Azure で実行する必要がありますか。

いいえ。アプリケーションは任意の場所でホストできます (クラウドまたはオンプレミス)。Azure AD B2C とやり取りするために必要なのは、パブリックにアクセス可能なエンドポイントで HTTP 要求を送受信する機能のみです。

### 複数の Azure AD B2C ディレクトリがあります。Azure プレビュー ポータルでそれらを管理するにはどのようにすればいいですか。

Azure プレビュー ポータルには、各 Azure AD B2C ディレクトリ独自の B2C 機能ブレードがあります。Azure プレビュー ポータルで特定のディレクトリの B2C 機能ブレードに移動する方法については、[ここ](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)をご覧ください。Azure プレビュー ポータルで Azure AD B2C ディレクトリを切り替えると、大部分のブラウザでは B2C 機能ブレードが開いたままになりません。

### Azure AD B2C によって送信される検証電子メールをカスタマイズするにはどうすればいいですか (コンテンツ、および送信者フィールド ("From:" フィールド))。

詳細については、[UI のカスタマイズに関するこの記事](active-directory-b2c-reference-ui-customization.md)をご覧ください。

### 既存のユーザー名、パスワード、およびプロファイルを自分のデータベースから Azure AD B2C に移行するにはどのようにすればいいですか。

Azure AD Graph API ([ここ](active-directory-b2c-devquickstarts-graph-dotnet.md)からサンプルを参照) を使用して、移行ツールを作成できます。将来、さまざまな移行オプションとツールをすぐに使用できる状態で提供する予定です。

### Azure AD Connect を使用して、自分のオンプレミス Active Directory に保存されているお客様の ID を Azure AD B2C に移行できますか。

いいえ。Azure AD Connect は Azure AD B2C と連携するようには設計されていません。将来、さまざまな移行オプションとツールをすぐに使用できる状態で提供する予定です。

### Azure AD B2C は Microsoft Dynamics のような CRM システムと連携しますか。

現時点では連携しません。これらのシステムとの統合を検討中です。

### Azure AD B2C は SharePoint On-Premises 2016 以前と連携しますか。

現時点では連携しません。Azure AD B2C では SAML 1.1 トークンはサポートされません。

### Azure AD B2C ではどのようなレポート機能と監査機能が提供されますか。 それは Azure AD Premium の機能と同じですか。

いいえ。Azure AD B2C では Azure AD Premium と同じレポート セットはサポートされていません。Azure AD B2C では、基本レポート API および監査 API が間もなくリリースされる予定です。

### Azure AD B2C で提供されているページの UI をローカライズできますか。 どの言語がサポートされていますか。

現在、Azure AD B2C は英語のみに最適化されています。可能な限り早急にローカライズ機能を展開する予定です。

### Azure AD B2C によって提供されているサインアップおよびサインイン ページで独自の URL を使用できますか。 たとえば、URL を login.microsoftonline.com から login.contoso.com に変更できますか。

現時点では変更できません。ただし、この機能は検討中です。

### Enterprise Mobility Suite (EMS) の一部として Azure AD B2C を取得できますか。

いいえ。Azure AD B2C は従量課金の Azure サービスであり、EMS には含まれていません。

### Azure AD B2C に関する問題を報告するにはどのようにすればいいですか。

Azure AD B2C に関する[このサポート トピック](active-directory-b2c-support.md)を確認してください。

### Azure AD B2C が一般に使用できるようになるのはいつですか。

現時点では、一般に使用可能になる日付に関する情報を提供することはできません。

## 詳細情報

現在の[プレビューの制限事項および制約事項](active-directory-b2c-limitations.md)を確認することもできます。

<!---HONumber=Sept15_HO4-->