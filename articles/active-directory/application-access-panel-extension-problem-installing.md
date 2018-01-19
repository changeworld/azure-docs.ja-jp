---
title: "アプリケーション アクセス パネルのブラウザー拡張機能のインストールに関する問題 | Microsoft Docs"
description: "アクセス パネルのブラウザー拡張機能のインストール時に発生する一般的なエラーの修正方法"
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
ms.openlocfilehash: 26dc5d5ffce84206450123132c0633c2aa323e9f
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2018
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>アプリケーション アクセス パネルのブラウザー拡張機能のインストールに関する問題

アクセス パネルは Web ベースのポータルで、ここから Azure Active Directory (Azure AD) の職場または学校アカウントを持つユーザーが、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動することができます。 Azure AD のエディションを持つユーザーは、アクセス パネルを介してセルフサービスのグループおよびアプリ管理機能を利用することもできます。 アクセス パネルは Azure Portal から独立していて、ユーザーが Azure サブスクリプションを持っている必要はありません。

アクセス パネルでパスワード ベースのシングル サインオン (SSO) を使用するためには、ユーザーのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 このアクセス パネルの拡張機能は、ユーザーがパスワード ベースの SSO 用に構成されているアプリケーションを選択したときに、自動的にダウンロードされます。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>アクセス パネルのブラウザーの要件を満たす

アクセス パネルには、JavaScript をサポートする CSS 対応のブラウザーが必要です。 アクセス パネルでパスワード ベースのシングル サインオン (SSO) を使用するためには、ユーザーのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 このアクセス パネルの拡張機能は、ユーザーがパスワード ベースの SSO 用に構成されているアプリケーションを選択したときに、自動的にダウンロードされます。

パスワードベースの SSO の場合、エンド ユーザーのブラウザーには次のいずれかを使用できます。

-   Edge - Windows 10 Anniversary Edition 以降 

-   Chrome - Windows 7 以降、MacOS X 以降

-   Firefox 26.0 以降 - Windows XP SP2 以降、Mac os X 10.6 以降

-   Internet Explorer 8、9、10、11 - Windows 7 以降 (制限付きサポート)
## <a name="how-to-install-the-access-panel-browser-extension"></a>アクセス パネルのブラウザー拡張機能をインストールする方法

アクセス パネルのブラウザー拡張機能をインストールするには、次の手順に従います。

1.  サポートされている任意のブラウザーで、[アクセス パネル](https://myapps.microsoft.com)を開き、Azure AD の**ユーザー**としてサインインします。

2.  アクセス パネルで、**パスワード SSO アプリケーション**をクリックします。

3.  ソフトウェアのインストールを確認するプロンプトで、**[今すぐインストール]** を選択します。

4.  お使いのブラウザーに基づいて、ダウンロード リンクが表示されます。 お使いのブラウザーに拡張機能を**追加**します。

5.  お使いのブラウザーに確認メッセージが表示されたら、拡張機能を **[有効にする]** または **[許可する]** のいずれかを選択します。

6.  インストールが完了したら、ブラウザー セッションを**再起動**します。

7.  アクセス パネルにサインインし、パスワード SSO アプリケーションを**起動**できるかどうかを確認します。

以下のダイレクト リンクから、Chrome および Edge 対応の拡張機能をダウンロードすることもできます。

-   [Chrome アクセス パネル拡張機能](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Edge アクセス パネル拡張機能](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能を使用する方法
拡張機能用のマイ アプリの既定の URL の変更

https://myapps.microsoft.com 以外のマイ アプリ URL を使用している場合は、次の手順に従って既定の URL を構成する必要があります。
1. 拡張機能にサインインしていないときに、拡張機能のアイコンを**右クリック**します。
2. メニューで **[Select My Apps URL]/(マイ アプリ URL の選択/)** をクリックします。
3. 既定の URL を**選択**します。
4. 拡張機能のアイコンをクリックします。
5. **[開始するにはサインインしてください]** を選択して拡張機能にサインインします。

ブラウザーからアプリに直接サインインする
1. 拡張機能のインストール後に、**[開始するにはサインインしてください]** を選択して拡張機能にサインインします。
2. サインインするアプリの **[サインオン URL]** に移動します。これは通常、ログイン フォームを表示するアプリの URL です。
3. 拡張機能の状態が変わり、パスワードが使用可能であることを示します。**拡張機能のアイコン**をクリックしてサインインします。

拡張機能からアプリを起動する
1. 拡張機能のインストール後に、**[開始するにはサインインしてください]** を選択して拡張機能にサインインします。
2. 拡張機能のアイコンをクリックして、その**メニュー**を開きます。
3. MyApps ポータルで使用可能なアプリを**検索**します。
4. **検索結果**からアプリをクリックして起動します。
5. 最後に起動された 3 つのアプリも **[最近の使用]** ショートカットの一覧に表示されます

> [!NOTE]
> これらのオプションは、Edge、Chrome、Firefox でのみ使用できます。

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Internet Explorer のグループ ポリシーの設定

Internet Explorer 用のアクセス パネル拡張機能をユーザーのコンピューターにリモートでインストールできるようにグループ ポリシーを設定できます。

前提条件は次のとおりです。

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) を設定し、ユーザーのコンピューターをドメインに参加させている必要があります。

-   グループ ポリシー オブジェクト (GPO) を編集するには、"設定の編集" アクセス許可が必要です。 既定では、Domain Administrators、Enterprise Administrators、Group Policy Creator Owners の各セキュリティ グループ メンバーにはこのアクセス許可があります。 [詳細情報](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

グループ ポリシーを構成し、ユーザーにデプロイする方法の詳しい手順については、「[グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする方法](active-directory-saas-ie-group-policy.md)」チュートリアルを参照してください。

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Internet Explorer でアクセス パネル拡張機能のトラブルシューティングを行う

診断ツールへのアクセスと IE 対応の拡張機能を構成する方法の詳しい手順については、「[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](active-directory-saas-ie-troubleshooting.md)」ガイドを参照してください。

> [!NOTE]
> IE は制限付きサポートとなっており、新しいソフトウェア更新プログラムは提供されなくなりました。 Edge が推奨されるブラウザーです。

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>これらのトラブルシューティング手順で問題が解決しない場合

使用可能な場合は、次の情報を含むサポート チケットを開きます。

-   関連エラー ID

-   UPN (ユーザーの電子メール アドレス)

-   TenantId

-   ブラウザーの種類

-   タイム ゾーンとエラーが発生した時刻/タイムフレーム

-   Fiddler のトレース

## <a name="next-steps"></a>次の手順
[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
