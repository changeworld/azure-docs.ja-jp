---
title: "Azure AD Connect: シームレス シングル サインオンのトラブルシューティング | Microsoft Docs"
description: "このトピックでは、Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) のトラブルシューティング方法について説明します。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: a543be452abbbe3057a5e275612968cd52c8b7aa
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオンのトラブルシューティング方法

## <a name="known-issues"></a>既知の問題

- Azure AD Connect を使用して 30 を超える AD フォレストを同期している場合、シームレス SSO のセットアップに使用するウィザードが正しく機能しません。 この問題を回避するには、テナントでシームレス SSO 機能を[手動で有効](#manual-reset-of-azure-ad-seamless-sso)にします。
- Azure AD サービスの URL (https://autologon.microsoftazuread-sso.com、https://aadg.windows.net.nsatc.net) が "ローカル イントラネット" ゾーンではなく "信頼済みサイト" ゾーンに追加されます。

## <a name="troubleshooting-checklist"></a>トラブルシューティングのチェックリスト

Azure AD シームレス SSO のトラブルシューティングを行う場合は、次のチェックリストを使用します。

1. Azure AD Connect ツールで、シームレス SSO 機能がテナントで有効になっているかどうかを確認します。 (ポートのブロックなどが原因で) この機能を有効にすることができない場合は、すべての[前提条件](active-directory-aadconnect-sso.md#pre-requisites)を満たしていることを確認します。 機能の有効化に関する問題が引き続き発生する場合は、Microsoft サポートに連絡してください。
2. 両方のサービス URL (https://autologon.microsoftazuread-sso.com と https://aadg.windows.net.nsatc.net) がイントラネット ゾーン設定の一部として定義されていることを確認します。
3. 会社のデスクトップが AD ドメインに参加していることを確認します。
4. ユーザーが AD ドメイン アカウントを使用してデスクトップにログオンしていることを確認します。
5. ユーザーのアカウントが、シームレス SSO が設定されている AD フォレストからのものであることを確認します。
6. デスクトップが企業ネットワークに接続されていることを確認します。
7. デスクトップの時刻が Active Directory の時刻およびドメイン コントローラーの時刻と同期されており、時刻のずれが 5 分以内であることを確認します。
8. デスクトップから既存の Kerberos チケットを消去します。 これを行うには、コマンド プロンプトで **klist purge** コマンドを実行します。 ユーザーの Kerberos チケットは通常は 12 時間有効ですが、Active Directory で異なる設定をしている可能性があるので注意してください。
9. 潜在的な問題の特定に役立つ、ブラウザーのコンソール ログ ([開発者ツール] の下) を確認します。
10. [ドメイン コント ローラーのログ](#domain-controller-logs)も確認します。

### <a name="domain-controller-logs"></a>ドメイン コントローラーのログ

ドメイン コントローラーで成功の監査が有効になっている場合、ユーザーがシームレス SSO を使用してサインインするたびに、セキュリティ エントリ (コンピューター アカウント **AzureADSSOAcc$** に関連付けられたイベント 4769) がイベント ログに記録されます。 これらのセキュリティ イベントは、次のクエリを使用して検索できます。

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>Azure AD シームレス SSO の手動リセット

トラブルシューティングを行っても改善しない場合は、次の手順を実行して、テナントでシームレス SSO 機能を手動でリセットまたは有効化します。

### <a name="1-import-the-seamless-sso-powershell-module"></a>1.Seamless SSO PowerShell モジュールのインポート

- 最初に、 [Microsoft Online Services サインイン アシスタント](http://go.microsoft.com/fwlink/?LinkID=286152)をダウンロードしてインストールします。
- 次に、 [64-bit Azure Active Directory Module for Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297)をダウンロードしてインストールします。
- `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーに移動します。
- 以下のコマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。`Import-Module .\AzureADSSO.psd1`

### <a name="2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2.シームレス SSO が有効になっている AD フォレストのリストの取得

- PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 Azure AD テナント管理者の資格情報を入力するポップアップが表示されます。
- `Get-AzureADSSOStatus` を呼び出します。 この機能が有効になっている AD フォレストのリスト ("ドメイン" リストを参照) が表示されます。

### <a name="3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3.機能が有効に設定されている各 AD フォレストのシームレス SSO を無効にします。

- PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 Azure AD テナント管理者の資格情報を入力するポップアップが表示されます。
- `$creds = Get-Credential` を呼び出します。 目的の AD フォレストのドメイン管理者の資格情報を入力するポップアップが表示されます。
- `Disable-AzureADSSOForest -OnPremCredentials $creds` を呼び出します。 AZUREADSSOACCT コンピューター アカウントがオンプレミスの DC から削除され、さらに、この AD フォレストについて、この機能が無効になります。
- 機能が有効に設定されている AD フォレストごとに、上記の手順を繰り返します。

### <a name="4-enable-seamless-sso-for-each-ad-forest"></a>4.各 AD フォレストのシームレス SSO を有効にする

- `New-AzureADSSOAuthenticationContext` を呼び出します。 Azure AD テナント管理者の資格情報を入力するポップアップが表示されます。
- `Enable-AzureADSSOForest` を呼び出します。 目的の AD フォレストのドメイン管理者の資格情報を入力するポップアップが表示されます。
- 機能を有効に設定する AD フォレストごとに、上記の手順を繰り返します。

### <a name="5-enable-seamless-sso-on-your-tenant"></a>5.テナントでのシームレス SSO の有効化

- `New-AzureADSSOAuthenticationContext` を呼び出します。 Azure AD テナント管理者の資格情報を入力するポップアップが表示されます。
- `Enable-AzureADSSO` を呼び出し、`Enable: ` プロンプトで「true」と入力して、テナントでこの機能を有効にします。

