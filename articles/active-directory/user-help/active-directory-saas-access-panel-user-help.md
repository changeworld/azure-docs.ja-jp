---
title: Azure Active Directory のマイ アプリ ポータルのアクセスと使用に関するヘルプ | Microsoft Docs
description: アクセス パネルへのサインイン、およびアクセス パネルでの一般的なタスクの実行に関するヘルプです。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: japere
ms.openlocfilehash: a6ac780625f6a109797cbdab78dc555e21e18bee
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343612"
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>マイ アプリ ポータルのアクセスと使用に関する問題のトラブルシューティング

マイ アプリ ポータルにサインインするとき、またはそのポータルの使用中に問題が発生した場合は、ヘルプデスクや管理者に連絡する前に、ここで示すトラブルシューティングのヒントをお試しください。

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>MyApps ポータルにサインインできません

次の一般的なヒントをお試しください。

- まず、ユーザーが正しい URL ([https://myapps.microsoft.com](https://myapps.microsoft.com)) を使用しているかどうかを確認します。
- ブラウザーの信頼済みサイトに、その URL を追加してみます。
- パスワードが正しいこと、また有効期限が切れていないことを確認します。 詳細については、「[職場または学校のパスワードをリセットする](active-directory-passwords-update-your-own-password.md)」を参照してください。
- 認証の連絡先情報が最新で、アクセスをブロックしていないことを確認します。 詳細については、「[Azure Multi-Factor Authentication とは何ですか](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user)」を参照してください。
- ブラウザーの Cookie を削除してから、再度サインインしてみます。

サインインを試みたときにまだ問題が発生する場合は、管理者にお問い合わせください。

## <a name="i-seem-to-be-having-password-issues"></a>パスワードに問題があるようです

パスワードを忘れた場合、IT スタッフからパスワードを受け取っていない場合、アカウントからロックアウトされた場合、またはパスワードを変更したい場合は、[Azure AD パスワードを忘れたときの対処方法](active-directory-passwords-update-your-own-password.md)に関するページをご覧ください。

## <a name="i-need-to-register-for-password-reset"></a>パスワードのリセット登録を登録する必要があります

パスワードのリセットやアカウントのロック解除を自分自身で実行するには、セルフ サービスによるパスワードのリセット (SSPR) を使用します。 この機能を利用する前に、認証方法を登録するか、管理者が求める定義済みの認証方法を確認する必要があります。 詳細については、「[セルフサービスによるパスワードのリセットを登録する](active-directory-passwords-reset-register.md)」を参照してください。

## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能を正しくインストールできません

マイ アプリ ポータルには、JavaScript をサポートする CSS 対応のブラウザーが必要です。 パスワードベースのシングル サインオン アプリを使用する場合は、付属の拡張機能もインストールする必要があります。 この拡張機能は、パスワード ベースのシングル サインオン アプリ向けに構成されているアプリケーションを起動すると自動的にダウンロードされます。

次のブラウザーの要件を満たしていることを確認します。

- **Edge**: Windows 10 Anniversary Edition 以降。
- **Chrome**: Windows 7 以降、Mac OS X 以降。
- **Firefox 26.0 以降**: Windows XP SP2 以降、Mac OS X 10.6 以降。
- **Internet Explorer 11**: Windows 7 以降 (制限付きサポート)。

拡張は次のサイトから直接ダウンロードすることもできます。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

拡張機能をインストールしても、引き続き問題が発生する場合は、次をお試しください。

- ブラウザー拡張機能の設定で、拡張機能が有効になっていることを確認します。
- ブラウザーを再起動し、マイ アプリ ポータルにサインインします。
- ブラウザーの Cookie をクリアして、マイ アプリ ポータルにサインインします。
- 診断ツールへのアクセス、および Internet Explorer 対応の拡張機能の構成のに関する順を追った説明については、「[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)」を参照してください。

## <a name="use-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能を使用する
* `https://myapps.microsoft.com` 以外のマイ アプリの URL を使用している場合は、次の手順に従って既定の URL を構成します。
   1. 拡張機能にサインインして "*いない*" ときに、拡張機能のアイコンを右クリックします。
   2. メニューから **[My Apps URL]\(マイ アプリの URL)** を選択します。
   3. 既定の URL を選択します。
   4. 拡張機能のアイコンを選択します。
   5. 拡張機能にサインインするには、**[開始するにはサインインしてください]** を選択します。

* ブラウザーからアプリに直接サインインするには、以下の手順を実行します。
   1. 拡張機能のインストール後、**[開始するにはサインインしてください]** を選択して拡張機能にサインインします。
   2. サインオン URL を使用してアプリにサインインします。  
       サインオン URL は、通常、サインイン フォームを表示するアプリの URL です。
      拡張機能の状態が変わり、パスワードが使用可能であることが示されます。
   3. サインインするには、拡張機能のアイコンを選択します。

* 拡張機能からアプリを起動するには、以下の手順を実行します。
   1. 拡張機能のインストール後、**[開始するにはサインインしてください]** を選択して拡張機能にサインインします。
   2. 拡張機能のアイコンを選択して、そのメニューを開きます。
   3. マイ アプリ ポータルで使用可能なアプリを検索します。
   4. 検索結果の一覧で、アプリを選択します。  
       最後に使用した 3 つのアプリが **[最近の使用]** ショートカットの一覧に表示されます。

> [!NOTE]
> こうしたオプションは、Microsoft Edge、Chrome、および Firefox でのみ使用できます。

## <a name="how-do-i-add-a-new-app"></a>新しいアプリの追加方法

1.  **[アプリ]** ページで **[アプリの追加]** を選択します。
2.  追加するアプリを検索し、**[追加]** を選択します。

   > [!NOTE]
   > * このオプションには、管理者がアカウントに対してこれを有効にしている場合にのみアクセスできます。
   > * アプリにアクセス許可が必要な場合は、管理者による承認を待たなければならない可能性があります。

## <a name="how-do-i-manage-my-group-memberships"></a>グループ メンバーシップの管理方法

**[グループ]** タイルを選択し、次のいずれかの操作を行います。
* グループを作成するには、**[所有しているグループ]** で **[グループの作成]** を選択し、指示に従います。
* グループに参加するには、**[Groups I'm in]\(登録しているグループ\)** で **[グループに参加]** を選択し、指示に従います。

   > [!NOTE]
   > * このオプションには、管理者がアカウントに対してこれを有効にしている場合にのみアクセスできます。
   > * グループのメンバーは、詳細を表示したり、グループを抜けたりできます。
   > * グループの所有者は、詳細を表示したり、メンバーを追加または削除したり、グループを抜けたりできます。