---
title: Azure AD のエンタープライズ アプリケーションのユーザー サインインを無効にする
description: Azure Active Directory で、ユーザーがサインインできないようにエンタープライズ アプリケーションを無効にする方法
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274091"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリのユーザー サインインを無効にする

Azure Active Directory (Azure AD) で、ユーザーがサインインできないようにエンタープライズ アプリケーションを簡単に無効にすることができます。 エンタープライズ アプリを管理するには、適切なアクセス許可が必要です。 また、ディレクトリの全体管理者である必要があります。

## <a name="how-do-i-disable-user-sign-ins"></a>ユーザー サインインを無効にする方法

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
1. **[Azure Active Directory** -  ***directoryname]*** ウィンドウ (管理対象のディレクトリの Azure AD ウィンドウ) で、 **[エンタープライズ アプリケーション]** を選択します。
1. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ウィンドウには、管理できるアプリのリストが表示されます。 アプリを選択します。
1. [***appname***] ウィンドウ (選択したアプリの名前がタイトルに含まれるページ) で、 **[プロパティ]** を選択します。
1. [***appname*** - **プロパティ**] ウィンドウで、 **[ユーザーのサインインが有効になっていますか?]** の **[いいえ]** を選択します。
1. **[保存]** をクリックします。

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD PowerShell を使用して、一覧にないアプリを無効にする

Enterprise アプリの一覧に表示されていないアプリの AppId が分かっている場合 (たとえば、アプリを削除したか、アプリが Microsoft によって事前承認されているためにサービスプリンシパルがまだ作成されていないため)、アプリのサービス プリンシパルを手動で作成してから、[AzureAD PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)を使用して無効にすることができます。

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>次のステップ

* [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)
