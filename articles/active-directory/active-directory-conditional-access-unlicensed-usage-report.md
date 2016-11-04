---
title: 許可されていない使用に関するレポート | Microsoft Docs
description: 許可されていない使用に関するレポートを使用すると、有料の Azure AD 機能を使用しているライセンスのないユーザーを特定できます。
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: markvi

---
# 許可されていない使用に関するレポート
許可されていない使用に関するレポートを使用すると、有料の Azure AD 機能を使用しているライセンスのないユーザーを特定できます。これにより、購入したライセンスの使用をより適切に管理できるほか、ライセンスを追加する必要があるタイミングがわかります。

レポートには、過去 30 日間にわたって有料機能のアクティブな使用状況が示されます。

## レポートの構造
| 列名 | 説明 |
|:--- |:--- |
| ライセンスのないユーザー |ユーザーの名前 |
| 機能 |機能名。例: 条件付きアクセス |
| アクセスしたアプリケーション |機能によってアクセスされているアプリケーションの名前。例: Office 365 SharePoint Online |

> [!NOTE]
> ユーザー アカウントが削除されている場合、[ライセンスのないユーザー] 列には 1003000090D8B285 のような ID が示されます。
> 
> 

## 条件付きアクセス機能
条件付きアクセス ポリシーが適用されているサービスに Azure AD Premium ライセンスを持たないユーザーがアクセスすると、ライセンスを持たないユーザーであることを示すフラグが設定されます。

これは、Intune を使用するデバイス ポリシーに加えて、MFA ポリシーや場所ポリシーにも適用されます。

## 関連項目
* [Office 365 およびその他の Azure Active Directory 接続アプリでの条件付きアクセスの使用](active-directory-conditional-access.md)
* [Azure AD への条件付きアクセスの基本](active-directory-conditional-access-azuread-connected-apps.md)

<!---HONumber=AcomDC_0727_2016-->