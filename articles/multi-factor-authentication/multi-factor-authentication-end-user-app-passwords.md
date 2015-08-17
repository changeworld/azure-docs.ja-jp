<properties 
	pageTitle="Azure MFA のアプリ パスワードとは" 
	description="このページは、アプリ パスワードの詳細と、Azure MFA に関連した用途を理解するのに役立ちます。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>




# Azure Multi-Factor Authentication のアプリ パスワードとは

現在、特定の非ブラウザー アプリ (Exchange Active Sync を使用する Apple ネイティブ電子メールクライアントなど) は、多要素認証をサポートしていません。多要素認証はユーザーごとに有効にします。これは、ユーザーで多要素認証が有効になると、非ブラウザー アプリを使用しようとしても、操作を実行できないことを意味します。アプリ パスワードによってこれが発生します。

>[AZURE.NOTE]Office 2013 クライアントのための最新の認証
>
> Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートするようになり、Multi-Factor Authentication をサポートするように有効化できます。つまり、Multi-Factor Authentication を有効にすると、Office 2013 クライアントでアプリ パスワードは不要になります。詳しくは、「[発表された Office 2013 の最新の認証のパブリック プレビュー](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」をご覧ください。
 
## アプリ パスワードを使用する方法

アプリ パスワードの使用方法の注意点を次に示します。

- 実際のパスワードは自動的に生成され、ユーザーが指定するわけではありません。これは、自動的に生成されたパスワードのほうが攻撃者から推測されづらく、より安全なためです。
- 現在、ユーザーあたりのパスワード数の制限は 40 個です。制限に達した後に作成しようとした場合、新しいものを作成できるよう既存のアプリ パスワードのいずれかを削除するよう求められます。
- アプリ パスワードは、アプリケーションごとではなく、デバイスごとに作成することをお勧めします。たとえば、ノート PC 用に 1 つのアプリ パスワードを作成し、そのアプリ パスワードをノート PC 上のすべてのアプリケーションで使用します。
- 最初にサインインするときにアプリ パスワードが提供されます。追加のアプリ パスワードが必要な場合は、作成できます。
 
<center>![Setup](./media/multi-factor-authentication-end-user-app-passwords/app.png)</center>

アプリ パスワードが取得できたら、これらの非ブラウザー アプリに対して、元のパスワードの代わりに使用します。たとえば、電話で多要素認証と Apple ネイティブ電子メール クライアントを使用している場合は、多要素認証をバイパスし、作業を続行できるようにアプリ パスワードを使用します。

### アプリ パスワードの作成
最初のサインイン時に、使用可能なアプリ パスワードが提供されます。さらに、後でアプリ パスワードを作成することもできます。そうする方法は、多要素認証を使用する方法によって異なります。最も適切な方法を選択します。

多要素認証の使用方法|説明
:------------- | :------------- | 
[Office 365 で使用する](multi-factor-authentication-end-user-manage-o365.md)| これは、Office 365 ポータルでアプリ パスワードを作成することを意味します。
[Microsoft Azure で使用する](multi-factor-authentication-end-user-manage-azure.md)| これは、Azure ポータルでアプリ パスワードを作成することを意味します。
[わからない](multi-factor-authentication-end-user-manage-myapps.md)|これは、[https://myapps.microsoft.com](https://myapps.microsoft.com) でアプリ パスワードを作成することを意味します。




 

<!---HONumber=August15_HO6-->