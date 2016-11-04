---
title: Azure サブスクリプションにサインインして管理することができない | Microsoft Docs
description: Azure サブスクリプションのログインに関するいくつかの一般的な問題について、トラブルシューティング情報を掲載しています。
services: ''
documentationcenter: ''
author: genlin
manager: msmbaldwin
editor: ''
tags: billing

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: genli

---
# Azure サブスクリプションにサインインして管理することができない
この記事では、ログインに関する問題の最も一般的な解決方法を紹介します。

> [!NOTE]
> この記事についてさらにヘルプが必要な場合は、問題を迅速に解決できるよう[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
> 
> 

## ページの読み込み中にハングする
お使いのインターネット ブラウザーの問題が原因である可能性があります。

この問題を解決するには、次の手順を記載された順番で実行してみてください。各手順の実行後に、ポータルのサインイン ページに再接続してみてください。

* ページを更新します。
* 別のインターネット ブラウザーを使用します。
* Microsoft Internet Explorer を使用している場合は、InPrivate ブラウズ モードを使用して Azure Portal にアクセスします。そのためには、次の手順に従います。
  
  A.**[ツール]** (![ツール ボタン](./media/billing-cannot-login-subscription/Toolsbutton.png)) > **[セーフティ]** > **[InPrivate ブラウズ]** の順にクリックします。
  
  B.[Azure Portal](https://portal.azure.com) にアクセスして、サインインします。

## "サブスクリプションが見つかりません" エラー メッセージが表示される
この問題は、アカウントに適切なユーザー権限が無い場合に発生する可能性があります。アカウント管理者は[アカウント センター](https://account.windowsazure.com/)のみにアクセスできるのに対し、サービス管理者 (SA) と共同管理者 (CA) がアクセスできるのは [Azure Portal](https://portal.azure.com) のみです。

**シナリオ 1: [Azure Portal](https://portal.azure.com) でこのエラー メッセージが表示された場合**

この問題を解決するには、アカウントに[共同管理者または所有者のロールを追加します](billing-add-change-azure-subscription-administrator.md)。

**シナリオ 2: [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)でこのエラー メッセージが表示された場合**

使用しているアカウントがアカウント管理者であるかどうかを確認します。どのアカウントがアカウント管理者であるかを確認するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ハブ メニューで、**[サブスクリプション]** を選択します。
3. 確認するサブスクリプションを選択し、**[設定]** を選択します。
4. **[プロパティ]** を選択します。サブスクリプションのアカウント管理者が、**[アカウント管理者]** ボックスに表示されます。

## サインイン時に自動で別のユーザーになる
この問題は、インターネット ブラウザーで複数のユーザー アカウントを使用している場合に発生する可能性があります。

この問題を解決するには、次の方法のいずれかを試してください。

* ポータルからサインアウトし、目的のアカウントでもう一度サインインする。
* キャッシュをクリアし、インターネット Cookie を削除する。Internet Explorer でこれを行うには、**[ツール]** (![ツール ボタン](./media/billing-cannot-login-subscription/Toolsbutton.png)) > **[インターネット オプション]** > **[削除]** の順にクリックし、一時ファイル、Cookie、パスワード、および閲覧履歴の各チェックボックスをオンにして [削除] をクリックします。
* Internet Explorer の設定をリセットして、構成済みの個人設定を元に戻す。これを行うには、**[ツール]** (![ツール ボタン](./media/billing-cannot-login-subscription/Toolsbutton.png)) > **[インターネット オプション]** > **[詳細設定]** > **[リセット]** の順にクリックし、**[個人設定を削除する]** チェックボックスをオンにして [リセット] をクリックします。
* InPrivate ブラウズ モードで Azure Portal にアクセスする。これを行うには、**[ツール]** (![ツール ボタン](./media/billing-cannot-login-subscription/Toolsbutton.png)) > **[セーフティ]** > **[InPrivate ブラウズ]** の順にクリックします。

## 組織のアカウントにサインインする必要があります
Microsoft アカウントとはお使いの電子メール アドレスとそのパスワードです。これを使用して、Windows Live プログラムまたはサービス (Outlook、Hotmail、MSN、OneDrive など) にサインインします。Microsoft アカウントを設定するには、会社の電子メールなど、所有する任意の電子メール アドレスを使用します。詳しくは、[www.microsoft.com/account](http://www.microsoft.com/account) をご覧ください。

Azure Portal の既定のサインイン ページは、Microsoft アカウント用です。アカウントを組織アカウントに関連付けた場合、適切なログイン オプション次のように選択してください。組織アカウントの使用方法について詳しくは、「[Azure への組織としてのサインアップ](active-directory/sign-up-organization.md)」を参照してください。

![サインイン ページ](./media/billing-cannot-login-subscription/signin.png)

> [!NOTE]
> まだ他に質問がある場合は、問題を迅速に解決できるよう[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
> 
> 

<!---HONumber=AcomDC_0928_2016-->