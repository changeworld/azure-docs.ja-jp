---
title: "Azure MFA のアクセスおよび使用状況レポート | Microsoft Docs"
description: "ここでは、Azure Multi-Factor Authentication 機能のレポートを使用する方法について説明します。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.reviewer: alexwe
ms.openlocfilehash: 77d6742faadfaf3d7afccfbe888b910c80278737
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication のレポート

Azure Multi-Factor Authentication は、個人や組織が使用できるいくつかのレポートを提供します。 これらのレポートは、Multi-factor Authentication 管理ポータルからアクセスできます。 次の表は使用できるレポートの一覧です。

| レポート | 説明 |
|:--- |:--- |
| 使用法 |使用状況レポートは、全体的な使用状況、ユーザーの概要およびユーザーの詳細に関する情報を示します。 |
| サーバーの状態 |このレポートは、アカウントに関連付けられている Multi-Factor Authentication Server の状態を示します。 |
| ユーザーのブロックの履歴 |これらのレポートは、ユーザーのブロックまたはブロック解除の要求の履歴を示します。 |
| ユーザーの認証バイパスの履歴 |ユーザーの電話番号について、Multi-Factor Authentication をバイパスする要求の履歴を示します。 |
| 不正アクセスのアラート |指定した日付範囲で送信された不正アクセスのアラートの履歴を示します。 |
| キューに登録済み |処理するためにキューに追加されたリストとその状態を一覧表示します。 レポートが完成すると、そのレポートのダウンロードまたは表示を行うためのリンクが提供されます。 |

## <a name="view-reports"></a>レポートを表示する

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側で、[Active Directory] を選択します。
3. 認証プロバイダーを使用するかどうかに応じて、次の 2 つのオプションのいずれかに従います。
   * **オプション 1**: [多要素認証プロバイダー] タブをクリックします。MFA プロバイダーを選択し、下部にある **[管理]** ボタンをクリックします。
   * **オプション 2**: ディレクトリを選択し、**[構成]** タブをクリックします。[多要素認証] セクションで、 **[サービス設定の管理]**を選択します。 [MFA サービス設定] ページの下部にある [ポータルに移動する] リンクをクリックします。
4. Azure Multi-Factor Authentication 管理ポータルで、左側のナビゲーションの **[レポートの表示]** セクションから、表示するレポートの種類を選択します。

<center>![クラウド](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>Powershell のレポート

以下の PowerShell を使用して、MFA に登録しているユーザーを識別します。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

以下の PowerShell を使用して、MFA に登録されていないユーザーを識別します。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**その他のリソース**

* [ユーザー向け](end-user/multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication (MSDN)](https://msdn.microsoft.com/library/azure/dn249471.aspx)
