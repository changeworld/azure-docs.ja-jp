<properties 
	pageTitle="Azure Multi-Factor Authentication レポート" 
	description="ここでは、追加のセキュリティ確認を強制するなど、ユーザー設定を変更する方法について説明します。" 
	documentationCenter="" 
	services="multi-factor-authentication" 
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

# クラウドでの Azure Multi-factor Authentication によるユーザー設定の管理

管理者は、ユーザーとデバイスに関する次の設定を管理できます。

- [選択したユーザーについて連絡方法の再指定を必須にする](#require-selected-users-to-provide-contact-methods-again)
- [Delete users existing app passwords](#delete-users-existing-app-passwords)
- [Restore MFA on all suspended devices for a user (パブリック プレビュー)](#restore-mfa-on-all-suspended-devices-for-a-user)






これは、コンピューターまたはデバイスが紛失または盗難にあった場合や、ユーザーのアクセスを削除する必要がある場合に便利です。


## 選択したユーザーについて連絡方法の再指定を必須にする

この設定は、ユーザーのサインイン時に、もう一度登録プロセスを完了することを強制します。ブラウザー以外のアプリは、ユーザーがアプリ パスワードを持っている場合に引き続き動作しますので注意してください。ユーザー アプリ パスワードを削除するには、**[選択したユーザーが生成したすべての既存のアプリ パスワードを削除する]** を選択することもできます。

### 連絡方法をもう一度提供するようユーザーに要求する方法

<ol>
<li>Azure 管理ポータルにサインインします。</li>
<li>左側で、[Active Directory] をクリックします。</li>
<li>[ディレクトリ] の下で、連絡方法の再提供を必要とするユーザーのディレクトリをクリックします。</li>
<li>上部で、[ユーザー] をクリックします。</li>
<li>ページの下部で、[多要素認証の管理] をクリックします。これにより、Multi-Factor Authentication ページが開きます。<li>管理するユーザーを見つけ、その名前の横にあるチェック ボックスをオンにします。上部でビューを変更することが必要になる場合があります。</li> <li>これにより、右側に **[ユーザー設定の管理]** リンクが表示されます。このボタンをクリックします。</li> <li>**[選択したユーザーについて連絡方法の再指定を必須にする]** チェック ボックスをオンにします。</li>

![連絡方法を提供する](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)

<li>[保存] をクリックします。</li>
<li>[閉じる] をクリックします。</li>

## Delete users existing app passwords

ユーザーが作成したすべてのアプリ パスワードを削除します。これらのアプリ パスワードに関連付けられているブラウザー以外のアプリは、新しいアプリ パスワードが作成されるまで機能しなくなります。

### ユーザーの既存のアプリ パスワードを削除する方法

<ol>
<li>Azure 管理ポータルにサインインします。</li>
<li>左側で、[Active Directory] をクリックします。</li>
<li>[ディレクトリ] の下で、アプリ パスワードを削除するユーザーのディレクトリをクリックします。</li>
<li>上部で、[ユーザー] をクリックします。</li>
<li>ページの下部で、[多要素認証の管理] をクリックします。これにより、Multi-Factor Authentication ページが開きます。<li>管理するユーザーを見つけ、その名前の横にあるチェック ボックスをオンにします。上部でビューを変更することが必要になる場合があります。</li> <li>これにより、右側に **[ユーザー設定の管理]** リンクが表示されます。このボタンをクリックします。</li> <li>**[選択したユーザーが生成したすべての既存のアプリ パスワードを削除する]** チェック ボックスをオンにします。</li> ![アプリ パスワードを削除する](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png) <li>[保存] をクリックします。</li> <li>[閉じる] をクリックします。</li>





## Restore MFA on all suspended devices for a user

管理者は、デバイスやブラウザーで Multi-Factor Authentication をリセットすることができます。これを行うには、ユーザーのデバイスやブラウザーについて Multi-Factor Authentication を復元します。このとき、ユーザーのすべてのデバイスやブラウザーから中断が解除されます。

### ユーザーの中断されたすべてのデバイスで MFA を復元する方法

<ol>
<li>Azure 管理ポータルにサインインします。</li>
<li>左側で、[Active Directory] をクリックします。</li>
<li>[ディレクトリ] の下で、MFA を復元するユーザーのディレクトリをクリックします。</li>
<li>上部で、[ユーザー] をクリックします。</li>
<li>ページの下部で、[多要素認証の管理] をクリックします。これにより、Multi-Factor Authentication ページが開きます。<li>管理するユーザーを見つけ、その名前の横にあるチェック ボックスをオンにします。上部でビューを変更することが必要になる場合があります。</li> <li>これにより、右側に **[ユーザー設定の管理]** リンクが表示されます。このボタンをクリックします。</li> <li>[中断しているすべてのデバイスに Multi-Factor Authentication を復元] チェック ボックスをオンにします。</li> ![アプリ パスワードを削除する](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png) <li>[保存] をクリックします。</li> <li>[閉じる] をクリックします。</li>

<!---HONumber=July15_HO2-->