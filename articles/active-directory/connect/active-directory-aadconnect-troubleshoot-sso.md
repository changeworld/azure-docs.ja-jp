---
title: 'Azure Active Directory Connect: シームレス シングル サインオンのトラブルシューティングを行う | Microsoft Docs'
description: このトピックでは、Azure Active Directory シームレス シングル サインオンのトラブルシューティング方法について説明します
services: active-directory
keywords: Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: 6e81ea9f98733b1b7e0c9bf7466ac844a37b6046
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオンのトラブルシューティングを行う

この記事は、Azure Active Directory (Azure AD) シームレス シングル サインオン (シームレス SSO) に関する一般的な問題のトラブルシューティング情報を見つけるうえで役立ちます。

## <a name="known-issues"></a>既知の問題

- 場合によっては、シームレス SSO の有効化に最大 30 分かかることがあります。
- テナントでシームレス SSO を無効にして再度有効にすると、キャッシュされた Kerberos チケットが期限切れになるまで (通常は 10 時間有効)、シングル サインオン機能は利用できません。
- Edge ブラウザーのサポートは使用できません。
- シームレス SSO が成功すると、ユーザーは **[サインインしたままにする]** を選択できません。 この動作により、SharePoint および OneDrive のマッピングのシナリオは機能しません。
- バージョン 16.0.8730.xxxx 以下の Office クライアントでは、シームレス SSO を使用した非対話型サインインがサポートされていません。 これらのクライアントでは、ユーザーはサインインするためにユーザー名を入力する必要があります。ただし、パスワードの入力は不要です。
- シームレス SSO は、Firefox のプライベート ブラウズ モードでは動作しません。
- シームレス SSO は、拡張保護モードがオンの場合は Internet Explorer で動作しません。
- シームレス SSO は、iOS および Android 上のモバイル ブラウザーでは動作しません。
- 30 以上の Active Directory フォレストを同期している場合は、Azure AD Connect によるシームレス SSO を有効にすることはできません。 この問題を回避するには、テナントでこの機能を[手動で有効](#manual-reset-of-azure-ad-seamless-sso)にします。
- Azure AD サービスの URL (https://autologon.microsoftazuread-sso.com) を、ローカル イントラネット ゾーンではなく信頼済みサイト ゾーンに追加すると、"*ユーザーのサインインがブロック*" されます。
- Active Directory の設定で Kerberos での暗号化の種類 **RC4_HMAC_MD5** の使用を無効にすると、シームレス SSO が中断されます。 グループ ポリシー管理エディター ツールで、**[コンピューターの構成]、[Windows 設定]、[セキュリティ設定]、[ローカル ポリシー]、[セキュリティ オプション]、[ネットワーク セキュリティ: Kerberos で許可する暗号化の種類を構成する]** の順に選択して、**[RC4_HMAC_MD5]** のポリシーの値が "有効" になっていることを確認してください。

## <a name="check-status-of-feature"></a>機能の状態の確認

テナントでシームレス SSO 機能が引き続き **[有効]** になっていることを確認します。 機能の状態は、[Azure Active Directory 管理センター](https://aad.portal.azure.com/)の **[Azure AD Connect]** ウィンドウで確認できます。

![Azure Active Directory 管理センター: [Azure AD Connect] ウィンドウ](./media/active-directory-aadconnect-sso/sso10.png)

クリックすると、シームレス SSO が有効になっているすべての AD フォレストが表示されます。

![Azure Active Directory 管理センター: シームレス SSO ウィンドウ](./media/active-directory-aadconnect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Azure Active Directory 管理センターでのサインイン失敗の理由 (Premium ライセンスが必要)

テナントに Azure AD Premium ライセンスが関連付けられている場合は、[Azure Active Directory 管理センター](https://aad.portal.azure.com/)で[サインイン アクティビティ レポート](../active-directory-reporting-activity-sign-ins.md)を参照することもできます。

![Azure Active Directory 管理センター: サインイン レポート](./media/active-directory-aadconnect-sso/sso9.png)

[Azure Active Directory 管理センター](https://aad.portal.azure.com/)で **[Azure Active Directory]** > **[サインイン]** に移動し、特定のユーザーのサインイン アクティビティを選択します。 **[サインインのエラー コード]** フィールドを探します。 次の表を使用して、そのフィールドの値を、失敗の理由と解決策にマップします。

|サインイン エラー コード|サインインが失敗した理由|解決策
| --- | --- | ---
| 81001 | ユーザーの Kerberos チケットが大きすぎます。 | ユーザーのグループ メンバーシップを減らしてやり直してください。
| 81002 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81003 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81004 | Kerberos 認証を試みましたが失敗しました。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81008 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81009 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81010 | シームレス SSO に失敗しました。ユーザーの Kerberos チケットが期限切れか無効です。 | ユーザーは、企業ネットワーク内のドメインに参加しているデバイスからサインインする必要があります。
| 81011 | ユーザーの Kerberos チケット内の情報では、ユーザー オブジェクトが見つかりません。 | Azure AD Connect を使用してユーザー情報を Azure AD と同期します。
| 81012 | Azure AD にサインインしようとしているユーザーは、デバイスにサインインしているユーザーと異なります。 | ユーザーは別のデバイスからサインインする必要があります。
| 81013 | ユーザーの Kerberos チケット内の情報では、ユーザー オブジェクトが見つかりません。 |Azure AD Connect を使用してユーザー情報を Azure AD と同期します。 

## <a name="troubleshooting-checklist"></a>トラブルシューティングのチェックリスト

シームレス SSO の問題のトラブルシューティングを行うには、次のチェックリストを使用します。

- Azure AD Connect でシームレス SSO 機能が有効になっていることを確認します。 (ポートのブロックなどが原因で) この機能を有効にできない場合は、すべての[前提条件](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites)が満たされていることを確認します。
- [Azure AD Join](../active-directory-azureadjoin-overview.md) とシームレス SSO の両方をテナントで有効にしている場合は、Azure AD Join で問題が発生していないことを確認してください。 デバイスが Azure AD に登録され、ドメインに参加している場合は、Azure AD Join の SSO がシームレス SSO よりも優先されます。 Azure AD の SSO を使用している場合、"Windows に接続済み" というサインイン タイルが表示されます。
- Azure AD の URL (https://autologon.microsoftazuread-sso.com) が、ユーザーのイントラネット ゾーンの設定に含まれていることを確認します。
- 会社のデバイスが Active Directory ドメインに参加していることを確認します。
- ユーザーが Active Directory ドメイン アカウントでデバイスにログオンしていることを確認します。
- ユーザーのアカウントが、シームレス SSO が設定されている Active Directory フォレストからのものであることを確認します。
- デバイスが企業ネットワークに接続されていることを確認します。
- デバイスの時刻が、Active Directory とドメイン コントローラーの両方の時刻と同期されており、時刻のずれが 5 分以内であることを確認します。
- コマンド プロンプトから `klist` コマンド使用して、デバイス上の既存の Kerberos チケットを一覧表示します。 `AZUREADSSOACCT` コンピューター アカウントに対して発行されたチケットが存在することを確認します。 ユーザーの Kerberos チケットは、通常は 10 時間有効です。 Active Directory で別の設定が行われていることがあります。
- テナントでシームレス SSO を無効にして再度有効にすると、キャッシュされた Kerberos チケットが期限切れになるまでシングル サインオン機能は利用できません。
- `klist purge` コマンドを使用してデバイスから既存の Kerberos チケットを消去し、やり直します。
- JavaScript に関連する問題があるかどうかを確認するために、ブラウザーのコンソール ログ (**[開発者ツール]** の下) を確認します。
- [ドメイン コントローラーのログ](#domain-controller-logs)を確認します。

### <a name="domain-controller-logs"></a>ドメイン コントローラーのログ

ドメイン コントローラーで成功の監査を有効にすると、ユーザーがシームレス SSO でサインインするたびに、セキュリティ エントリがイベント ログに記録されます。 こうしたセキュリティ イベントは、次のクエリを使用して検索できます  (コンピューター アカウント **AzureADSSOAcc$** に関連付けられているイベント **4769** を探します)。

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>機能の手動リセット

トラブルシューティングを行っても改善しなかった場合は、テナントでシームレス SSO 機能を手動でリセットできます。 Azure AD Connect が実行されているオンプレミス サーバーで次の手順を実行します。

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>手順 1: Seamless SSO PowerShell モジュールをインポートする

1. [Microsoft Online Services サインイン アシスタント](http://go.microsoft.com/fwlink/?LinkID=286152)をダウンロードして、インストールします。
2. [64-bit Azure Active Directory Module for Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297) をダウンロードして、インストールします。
3. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーを参照します。
4. `Import-Module .\AzureADSSO.psd1` コマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>手順 2: シームレス SSO が有効になっている Active Directory フォレストのリストを取得する

1. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 求められたら、テナントのグローバル管理者の資格情報を入力します。
2. `Get-AzureADSSOStatus` を呼び出します。 このコマンドでは、この機能が有効になっている Active Directory フォレストの一覧 ("ドメイン" リストを参照) が表示されます。

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>手順 3: 機能を設定した各 Active Directory フォレストのシームレス SSO を無効にする

1. `$creds = Get-Credential` を呼び出します。 求められたら、目的の Active Directory フォレストのドメイン管理者の資格情報を入力します。
2. `Disable-AzureADSSOForest -OnPremCredentials $creds` を呼び出します。 このコマンドは、この特定の Active Directory フォレスト用のオンプレミスのドメイン コントローラーから `AZUREADSSOACCT` コンピューター アカウントを削除します。
3. 機能を設定した Active Directory フォレストごとに、前の手順を繰り返します。

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>手順 4: 各 Active Directory フォレストのシームレス SSO を有効にする

1. `Enable-AzureADSSOForest` を呼び出します。 求められたら、目的の Active Directory フォレストのドメイン管理者の資格情報を入力します。
2. 機能を設定する Active Directory フォレストごとに、前の手順を繰り返します。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>手順 5. テナントで機能を有効にする

ご使用のテナントで機能をオンにするには、`Enable-AzureADSSO` を呼び出し、`Enable:` プロンプトで「**true**」と入力します。
