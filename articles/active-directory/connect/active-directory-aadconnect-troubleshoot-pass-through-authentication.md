---
title: "Azure AD Connect: パススルー認証のトラブルシューティング | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) のパススルー認証のトラブルシューティングを行う方法について説明します。"
services: active-directory
keywords: "Azure AD Connect パススルー認証のトラブルシューティング, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: aceba2cfeac03eae78e89ef46926b2a388663f09
ms.contentlocale: ja-jp
ms.lasthandoff: 05/13/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-pass-through-authentication"></a>Azure Active Directory パススルー認証のトラブルシューティング方法

この記事では、パススルー認証コネクタ (Azure AD Connect を使用またはスタンドアロン) のインストール時、登録時、またはアンインストール時に発生する一般的な問題に関するトラブルシューティング情報を提供します。 ご利用のテナントで Azure Active Directory (Azure AD) のパススルー認証機能を有効にするときや、運用するときのトラブルシューティングについても取り上げます。

## <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>コネクタ (Azure AD Connect を使用またはスタンドアロン) のインストール時の問題

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Azure AD アプリケーション プロキシ コネクタが既に存在する

パススルー認証コネクタを [Azure AD アプリケーション プロキシ](../../active-directory/active-directory-application-proxy-get-started.md) コネクタと同じサーバーにインストールすることはできません。 パススルー認証コネクタは、別のサーバーにインストールする必要があります。

### <a name="an-unexpected-error-occured"></a>予期しないエラーが発生する

サーバーから[コネクタのログを収集](#collecting-pass-through-authentication-connector-logs)し、問題について Microsoft サポートに連絡してください。

## <a name="issues-during-registration-of-connectors"></a>コネクタの登録時の問題

### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>ポートがブロックされているため、コネクタを登録できない

コネクタがインストールされているサーバーが、[こちら](active-directory-aadconnect-pass-through-authentication.md#prerequisites)に記載されているサービス URL およびポートと通信できることを確認します。

### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>トークンまたはアカウント認証エラーのため、コネクタの登録に失敗した

すべての Azure AD Connect またはスタンドアロンコネクタのインストールおよび登録操作に、クラウド専用グローバル管理者アカウントを使用していることを確認します。 MFA 対応グローバル管理者アカウントには既知の問題があります。回避策として、MFA を一時的にオフにします (操作を完了するためのみ)。

### <a name="an-unexpected-error-occurred"></a>予期しないエラーが発生する

サーバーから[コネクタのログを収集](#collecting-pass-through-authentication-connector-logs)し、問題について Microsoft サポートに連絡してください。

## <a name="issues-during-uninstallation-of-connectors"></a>コネクタのアンインストール時の問題

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Azure AD Connect のアンインストール時に警告メッセージが表示される

テナントでパススルー認証を有効にしている場合に、Azure AD Connect をアンインストールしようとすると、"Users will not be able to sign-in to Azure AD unless you have other pass-through authentication agents installed on other servers. (他のサーバーに他のパススルー認証エージェントがインストールされていない場合、ユーザーは Azure AD にサインインできなくなります。)" という警告メッセージ表示されます。

Azure AD Connect をアンインストールする前に、[高可用性](active-directory-aadconnect-pass-through-authentication.md)を適切にセットアップして、ユーザー サインインが中断されないようにする必要があります。

## <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>パススルー認証機能の有効化に関する問題

### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>使用できるコネクタがないため、機能を有効にすることができない

テナントでパススルー認証を有効にするには、アクティブなコネクタが少なくとも 1 つは必要です。 コネクタをインストールするには、Azure AD Connect またはスタンドアロン コネクタをインストールします。

### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>ポートがブロックされているため、機能を有効にすることができない

Azure AD Connect がインストールされているサーバーが、[こちら](active-directory-aadconnect-pass-through-authentication.md#prerequisites)に記載されているサービス URL およびポートと通信できることを確認します。

### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>トークンまたはアカウント認証エラーのため、機能の有効化に失敗した

機能を有効にする場合は、クラウド専用グローバル管理者アカウントを使用するようにします。 Multi-Factor Authentication (MFA) 対応グローバル管理者アカウントには既知の問題があります。回避策として、MFA を一時的にオフにします (操作を完了するためのみ)。

## <a name="issues-while-operating-the-pass-through-authentication-feature"></a>パススルー認証機能の動作中の問題

### <a name="user-facing-sign-in-errors"></a>ユーザーに表示されるサインイン エラー

Azure AD サインイン画面でユーザーに表示されるエラーがあります。 これらのエラーの詳細と適切な解決手順を以下に示します。

|エラー|Description|解決策
| --- | --- | ---
|AADSTS80001|Unable to connect to Active Directory (Active Directory に接続できません)|コネクタ サーバーが、パスワードを検証する必要のあるユーザーと同じ AD フォレストのメンバーであり、Active Directory に接続できることを確認します。  
|AADSTS8002|A timeout occurred connecting to Active Directory (Active Directory への接続中にタイムアウトが発生しました)|Active Directory が使用可能であり、コネクタからの要求に応答していることを確認します。
|AADSTS80004|The username passed to the connector was not valid (コネクタに渡されたユーザー名が無効です)|サインインしようとしているユーザーのユーザー名が正しいことを確認してください。
|AADSTS80005|Validation encountered unpredictable WebException (検証で予測外の WebException が発生しました)|これは一時的なエラーの可能性があります。 要求をやり直してください。 引き続きエラーが発生する場合は、Microsoft サポートに連絡してください。
|AADSTS80007|An error occurred communicating with Active Directory (Active Directory との通信中にエラーが発生しました)|Check the connector logs for more information and verify that Active Directory is operating as expected. (コネクタ ログで詳細を確認し、Active Directory が期待通りに動作していることを確認してください)

## <a name="collecting-pass-through-authentication-connector-logs"></a>パススルー認証コネクタのログの収集

発生する問題の種類に応じて、異なる場所にあるパススルー認証コネクタのログを確認する必要があります。

### <a name="connector-event-logs"></a>コネクタ イベント ログ

コネクタに関するエラーの場合、サーバーでイベント ビューアー アプリケーションを開き、**Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin** にあるログを確認します。

詳細な分析ログとデバッグ ログが必要な場合は、"セッション" ログを有効にすることができます。 通常の操作中に、このログを有効にした状態でコネクタを実行しないでください。これはトラブルシューティングにのみ使用します。 ログの内容は、ログを再度無効にした後にのみ表示されます。

### <a name="detailed-trace-logs"></a>詳細なトレース ログ

ユーザー サインイン エラーのトラブルシューティングを行うときは、**C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace** にあるトレース ログを確認します。 これらのログには、パススルー認証機能を使用した特定のユーザー サインインが失敗した原因が記録されています。 ログ エントリの例を次に示します。

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

コマンド プロンプトを開き、次のコマンドを実行することで、エラー (上記の例では "1328") の詳細を取得できます。 注: "1328" は、ログに表示されている実際のエラー番号に置き換える必要があります。

`Net helpmsg 1328`

結果は次のようになります。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>ドメイン コントローラーのログ

監査ログが有効になっている場合は、ドメイン コントローラーのセキュリティ ログで追加情報を確認できます。 パススルー認証コネクタから送信されたサインイン要求を簡単に照会する方法を次に示します。

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

