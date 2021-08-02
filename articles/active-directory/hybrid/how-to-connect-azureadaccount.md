---
title: Azure AD コネクタ アカウント パスワードの変更 | Microsoft Docs
description: このトピックでは、Azure AD コネクタ アカウントを復元する方法を説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5a16e571478428ef1eb068236369b6e9af9f19e1
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111853696"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Azure AD コネクタ アカウント パスワードの変更
Azure AD コネクタ アカウントは、無料のサービスであると想定されています。 その資格情報をリセットする必要がある場合、このトピックが役立ちます。 たとえば、グローバル管理者が PowerShell を使用してアカウントのパスワードを誤ってリセットしてしまった場合などです。

## <a name="reset-the-credentials"></a>資格情報をリセットする
Azure AD コネクタのアカウントが認証の問題のために Azure AD に接続できない場合、パスワードをリセットすることができます。

1. Azure AD Connect 同期サーバーにサインインし、PowerShell を開きます。
2. Azure AD グローバル管理者の資格情報を指定するには、`$credential = Get-Credential` を実行します。
3. コマンドレット `Add-ADSyncAADServiceAccount -AADCredential $credential` を実行します。

   コマンドレットが成功すると、PowerShell コマンド プロンプトが表示されます。 
   
このコマンドレットは、Azure AD と同期エンジンの両方で、サービス アカウントのパスワードをリセットして更新します。

## <a name="known-issues-these-steps-can-solve"></a>上記の手順で解決できる既知の問題
このセクションでは、ユーザーから報告された、Azure AD コネクタ アカウントの資格情報をリセットすることで修正されたエラーの一覧を示します。

---
Event 6900 The server encountered an unexpected error while processing a password change notification (イベント 6900 パスワード変更通知の処理中に、サーバーで予期しないエラーが発生しました):AADSTS70002: Error validating credentials. (資格情報の検証中にエラーが発生しました。) AADSTS50054: 認証に古いパスワードが使用されました。

---
イベント 659 Error while retrieving password policy sync configuration. (パスワード ポリシーの同期構成を取得しているときにエラーが発生しました。) Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:AADSTS70002: Error validating credentials. (資格情報の検証中にエラーが発生しました。) AADSTS50054: 認証に古いパスワードが使用されました。

## <a name="next-steps"></a>次のステップ
**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
