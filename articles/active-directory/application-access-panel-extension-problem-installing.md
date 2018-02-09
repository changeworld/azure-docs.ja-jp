---
title: "アプリケーション アクセス パネルのブラウザー拡張機能のインストール - Azure | Microsoft Docs"
description: "アクセス パネルのブラウザー拡張機能のインストール時に発生する一般的なエラーを修正します。"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: c49cfad5f362f4402be476066f0e8c0158f20d73
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="install-the-access-panel-browser-extension"></a>アクセス パネルのブラウザー拡張機能のインストール

アクセス パネルは Web ベースのポータルです。 Azure Active Directory (Azure AD) の職場または学校アカウントがある場合は、アクセス パネルを使用して、Azure AD 管理者がアクセスを許可したクラウドベースのアプリケーションを表示および起動できます。 

Azure AD のエディションを使用している場合は、アクセス パネルを介してセルフサービスのグループおよびアプリ管理機能を利用することもできます。 

アクセス パネルは Azure Portal とは別のものです。 Azure サブスクリプションを持っている必要はありません。

## <a name="web-browser-requirements"></a>Web ブラウザーの要件

アクセス パネルには、少なくとも、JavaScript をサポートする CSS 対応のブラウザーが必要です。 アクセス パネルでパスワードベースの SSO を使ってアプリケーションにサインインするには、ブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 この拡張機能は、パスワードベースの SSO 用に構成されているアプリケーションを選ぶと、自動的にダウンロードされます。

パスワードベースの SSO には、次のブラウザーを使用できます。

- **Edge**: Windows 10 Anniversary Edition 以降。 
- **Chrome**: Windows 7 以降、MacOS X 以降。
- **Firefox 26.0 以降**: Windows XP SP2 以降、Mac OS X 10.6 以降。
- **Internet Explorer 8、9、10、11**: Windows 7 以降 (制限付きサポート)。

## <a name="install-the-access-panel-browser-extension"></a>アクセス パネルのブラウザー拡張機能のインストール

アクセス パネルのブラウザー拡張機能をインストールするには、次の手順を実行します。

1.  サポートされている任意のブラウザーで、[アクセス パネル](https://myapps.microsoft.com)を開き、Azure AD アカウントのユーザーとしてサインインします。

2.  パスワードベースの SSO アプリケーションを選択します。

3.  メッセージが表示されたら、**[今すぐインストール]** を選択します。  
    選択したブラウザーのダウンロード リンクにリダイレクトされます。 
    
4.  **[追加]**を選択します。

5.  メッセージが表示されたら、拡張機能を**有効にする**か、**許可**します。

6.  インストール完了後、ブラウザーを再起動します。

7.  アクセス パネルにサインインし、パスワードベースの SSO アプリケーションを起動できるかどうかを確認します。

次のサイトから Chrome と Edge 対応の拡張機能を直接ダウンロードすることもできます。

- [Chrome 拡張機能](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Edge 拡張機能](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能の使用
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
> こうしたオプションは、Edge、Chrome、および Firefox でのみ使用できます。

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Internet Explorer のグループ ポリシーの設定

Internet Explorer 用のアクセス パネル拡張機能をユーザーのコンピューターにリモートでインストールできるようにグループ ポリシーを設定できます。

グループ ポリシーを設定する前に、以下のことを確認します。

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) を設定し、ユーザーのコンピューターをドメインに参加させている必要があります。

-   グループ ポリシー オブジェクト (GPO) を編集するには、"*設定の編集*" アクセス許可が必要です。 既定では、このアクセス許可は、ドメイン管理者、エンタープライズ管理者、グループ ポリシー作成所有者の各セキュリティ グループ メンバーに付与されます。

グループ ポリシーを構成し、ユーザーに展開するための順を追った説明については、[グループ ポリシーを使用した Internet Explorer 用アクセス パネル拡張機能の展開](active-directory-saas-ie-group-policy.md)に関するページを参照してください。

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Internet Explorer でのアクセス パネル拡張機能のトラブルシューティング

診断ツールへのアクセス、および Internet Explorer 対応の拡張機能の構成方法の詳細については、[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](active-directory-saas-ie-troubleshooting.md)に関するページをご覧ください。

> [!NOTE]
> Internet Explorer は制限付きサポートとなっており、新しいソフトウェア更新プログラムは提供されなくなりました。 Edge が推奨されるブラウザーです。

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>以上の手順で問題を解決できない場合

使用可能な場合は、次の情報を含むサポート チケットを開きます。

-   関連エラー ID
-   UPN (ユーザーの電子メール アドレス)
-   TenantId
-   ブラウザーの種類
-   タイム ゾーンと、エラーが発生したときの時刻または時間帯
-   Fiddler のトレース

## <a name="next-steps"></a>次の手順
[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
