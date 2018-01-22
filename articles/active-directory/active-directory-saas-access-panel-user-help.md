---
title: "Azure Active Directory で MyApps ポータルの操作についてお困りの場合 |Microsoft Docs"
description: "アクセス パネルを使用する場合は、一般的なタスクを実行する手順を入手してください。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 9bec51e1d49308baecc76143ec80902d2da418e8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>MyApps ポータルの操作についてお困りの場合

このページでは、MyApps ポータルの使用中に問題が発生した場合のトラブルシューティングをお手伝いします。 ヘルプ デスクまたは管理者に連絡して問題の解決を依頼する必要がある場合もありますが、以下に、最初に役立つことがあるいくつかのトラブルシューティングに関するトピックを示します。

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>MyApps ポータルにサインインできません

確認すべき一般的な問題:

- ユーザーが正しい URL ([https://myapps.microsoft.com](https://myapps.microsoft.com)) にサインインしようとしていることを確認します。

- ブラウザーの信頼済みサイトに、その URL を追加してみます。

- パスワードが期限切れになったり、忘れられたりしていないことを確認します。 パスワードを更新する方法の詳細については、[ここ](active-directory-passwords-update-your-own-password.md)を参照してください。

- 認証の連絡先情報が最新で、アクセスをブロックしていないかどうかを確認します。 認証情報を設定する方法の詳細については、[ここ](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user)を参照してください。

- ブラウザーの Cookie を削除してから、再度サインインしてみます。

サインインを試みたときにまだ問題が発生する場合は、管理者に連絡してサポートを依頼してください。


## <a name="how-do-i-update-my-password"></a>パスワードの更新方法

パスワードを忘れた場合、IT スタッフからパスワードを受け取っていない場合、アカウントからロックアウトされた場合の詳細については、「[Azure AD パスワードを忘れた場合](active-directory-passwords-update-your-own-password.md)」を参照してください。

## <a name="how-do-i-register-for-password-reset"></a>パスワードのリセット登録を行う方法

エンド ユーザーは、セルフサービスによるパスワードのリセット (SSPR) を使用してパスワードのリセットやアカウントのロック解除を自分自身で実行できます。 この機能を利用する前に、認証方法を登録するか、管理者によって設定されている定義済みの認証方法を確認する必要があります。 詳細については、「[セルフサービスのパスワード リセットのための登録](active-directory-passwords-reset-register.md)」を参照してください。


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能を正しくインストールできません

ブラウザーの要件を満たしているかどうかを確認します。

- このポータルには、JavaScript をサポートする CSS 対応のブラウザーが必要です。 パスワードベースのシングル サインオン アプリを使用する場合は、付属の拡張機能もインストールする必要があります。 この拡張機能は、パスワードベースのシングル サインオン アプリ向けに構成されているアプリケーションを起動すると自動的にダウンロードされます。

- 拡張機能を利用するためのブラウザーの要件は以下の通りです。
    - Edge - Windows 10 Anniversary Edition 以降
    - Chrome - Windows 7 以降、MacOS X 以降
    - Firefox 26.0 以降 - Windows XP SP2 以降、Mac os X 10.6 以降
    - Internet Explorer 8、9、10、11 - Windows 7 以降 (制限付きサポート)

以下のダイレクト リンクから、Chrome と Edge 対応の拡張機能をダウンロードすることもできます。

- [Chrome 拡張機能](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Edge 拡張機能](https://www.microsoft.com/store/apps/9pc9sckkzk84)

インストール後に問題が発生する場合は、次の手順を試みます。

- ブラウザー拡張機能の設定で、拡張機能が有効になっていることを確認します。

- ブラウザーを再起動し、マイ アプリ ポータルにサインインしてみます。

- Cookie を削除して、マイ アプリ ポータルにサインインしてみます。
- 診断ツールへのアクセスと IE 対応の拡張機能を構成する方法の詳しい手順については、「[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)」ガイドを参照してください。

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能はどのように使用すればよいですか
拡張機能に対するマイ アプリの既定の URL を変更

https://myapps.microsoft.com 以外のマイ アプリの URL を使用している場合は、次の手順に従って既定の URL を構成する必要があります。
1. 拡張機能にサインインしていないときに、拡張機能のアイコンを**右クリック**します。
2. メニューから **[Select My Apps URL]/(マイ アプリの URL の選択/)** を選択します。
3. 既定の URL を**選択**します。
4. 拡張機能のアイコンをクリックします。
5. **[開始するにはサインインしてください]** を選択して拡張機能にサインインします。

ブラウザーからアプリに直接サインイン
1. 拡張機能のインストール後に、**[開始するにはサインインしてください]** を選択して拡張機能にサインインします。
2. サインインするアプリの**[サインイン URL]** に移動します。これは通常、ログイン フォームを表示するアプリの URL です。
3. 拡張機能は状態を変更し、パスワードが使用可能であることを通知します。**拡張機能のアイコン**をクリックしてサインインします

拡張機能からのアプリの起動
1. 拡張機能のインストール後に、**[開始するにはサインインしてください]** を選択して拡張機能にサインインします。
2. 拡張機能のアイコンをクリックして、そのメニューを開きます。
3. My Apps ポータルで使用可能なアプリを**検索**します。
4. **検索結果**からアプリをクリックして起動します。
5. 最後に起動された 3 つのアプリも**[最近の使用]** ショートカットの一覧に表示されます

> [!NOTE]
> これらのオプションは、Edge、Chrome、Firefox でのみ使用可能です。

## <a name="how-do-i-add-a-new-app"></a>新しいアプリの追加方法

1.  **[アプリ]** ページで **[アプリの追加]** をクリックします。

2.  追加するアプリを検索して、**[追加]** をクリックします。

**解説:**

- このオプションには、管理者がアカウントに対して、このオプションを有効にしている場合にのみアクセスできます。

- アプリのアクセス許可が必要な場合は、管理者の承認を待機する必要があります。


## <a name="how-do-i-manage-my-group-memberships"></a>グループ メンバーシップの管理方法

1. **[グループ]** タイルをクリックします。 
2. グループを作成するには、[所有しているグループ] で **[グループの作成]** をクリックし、指示に従います。
3. グループに参加するには、[Groups I'm in]\(登録しているグループ\) で **[グループに参加]** をクリックし、指示に従います。

**解説:**

- このオプションには、管理者がアカウントに対して、このオプションを有効にしている場合にのみアクセスできます。

- メンバーになっているグループでは、詳細を表示したり、グループを抜けたりすることができます。

- 所有者であるグループでは、詳細を表示したり、メンバーを追加または削除したり、グループを抜けたりすることができます。


## <a name="next-steps"></a>次の手順

トラブルシューティングに関連した情報については、「[アプリケーション アクセス パネルの Web サイトまたはモバイル アプリケーションの使用に関する問題](active-directory-application-access-panel-content-map.md)」を参照してください。

