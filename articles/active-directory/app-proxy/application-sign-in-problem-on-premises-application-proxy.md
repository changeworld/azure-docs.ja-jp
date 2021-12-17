---
title: Azure Active Directory アプリケーション プロキシを使用してオンプレミス アプリにサインインできない
description: 統合しているオンプレミスのアプリケーションに Azure Active Directory アプリケーション プロキシでサインインできない場合の一般的な問題のトラブルシューティング
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: b51bd97f4ee79358230e85f94351abc937bf5225
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988451"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシを使用してオンプレミスのアプリケーションにサインインできない

オンプレミスのアプリケーションへのサインインに関する問題が発生した場合は、次の手順で問題を解決できます。

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>アプリケーションを読み込むことができるが、ページ上の何かが壊れているように見える

次のドキュメントは、このカテゴリ内の最も一般的な問題のいくつかを解決するために役立ちます。

  * [アプリケーションにアクセスできるが、アプリケーションのページが正しく表示されない](application-proxy-page-appearance-broken-problem.md)
  * [アプリケーションにアクセスできるが、読み込みに時間がかかる](application-proxy-page-load-speed-problem.md)
  * [アプリケーションにアクセスできるが、アプリケーション ページへのリンクが動作しない](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>アプリケーションで接続に関する問題が発生する
  次のドキュメントは、このカテゴリ内の最も一般的な問題のいくつかを解決するために役立ちます。
  * [アプリケーションに対してどのポートを開くかがわからない](application-proxy-add-on-premises-application.md)
  * [アプリケーションのコネクタ グループで動作しているコネクタがないため問題が発生した](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>管理ポータルで Azure AD アプリケーション プロキシを構成するときに問題が発生する
  次のドキュメントは、このカテゴリ内の最も一般的な問題のいくつかを解決するために役立ちます。
  * [アプリケーション プロキシ アプリケーションを構成するときに問題が発生した](application-proxy-config-how-to.md)
  * [アプリケーション プロキシ アプリケーションへのシングル サインオンの構成方法がわからない](application-proxy-config-sso-how-to.md)
  * [管理ポータルでアプリケーションを作成するときに問題が発生した](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>アプリケーションに対するバックエンド認証を設定するときに問題が発生する
  次のドキュメントは、このカテゴリ内の最も一般的な問題のいくつかを解決するために役立ちます。
  * [Kerberos の制約付き委任の構成方法がわからない](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [PingAccess でのアプリケーションの構成方法がわからない](application-proxy-ping-access-publishing-guide.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>アプリケーションにサインインするときに問題が発生する
  次のドキュメントは、このカテゴリ内の最も一般的な問題のいくつかを解決するために役立ちます。
  * ["この企業アプリケーションにアクセスできない" というエラーが発生する](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>アプリケーション プロキシ エージェント コネクタで問題が発生する
  次のドキュメントは、このカテゴリ内の最も一般的な問題のいくつかを解決するために役立ちます。
  * [アプリケーション プロキシ エージェント コネクタのインストールで問題が発生する](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>次のステップ
[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](application-proxy.md)