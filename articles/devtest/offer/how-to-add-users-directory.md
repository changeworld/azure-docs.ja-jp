---
title: Azure Dev/Test 開発者ディレクトリ テナントにユーザーを追加する
description: Azure クレジット サブスクリプションにユーザーを追加し、ロールベースのコントロールを使用してアクセスを管理するための方法ガイド。
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 14ded977ab78e8fbf6baf70d1c59e4aa632927b1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089989"
---
# <a name="add-users-to-your-azure-credit-subscription"></a>Azure クレジット サブスクリプションにユーザーを追加する  

サブスクリプション リソースを管理してアクセスするには、ディレクトリ内のユーザーである必要があります。 サブスクリプション自体では、階層はテナント (Azure Active Directory) Azure ADによって制御されます。 Azure ADは、アカウント名とパスワードを格納する軽量ディレクトリ プロトコル (LDAP) です。  

ユーザーを追加する前に、ビジネス階層と、サブスクリプション内で必要なアクセスレベルを決定する必要があります。  

## <a name="why-do-i-need-to-add-users"></a>ユーザーを追加する必要がある理由

手順 1 では、サブスクリプションに新しいユーザーを追加する必要があるかどうかを判断します。 新しいユーザーを追加する必要がある理由の例を次に示します。  

- プロジェクトで作業し、IT にセキュリティを監視するアクセス権を与える必要がある  
- 組織の別のメンバーにとって有益な可能性がある新しい API プロトコルに取り組む  
- すべてのリソース グループにアクセスできるよう、サブスクリプション レベルでアクセス権を与える必要があります  
- ビジネスレベルでは、1つのリソースグループが1台のコンピュータにサインインする必要がありますが、サブスクリプションへのアクセスは必要ありません  
- 作業しているプロジェクトの可視性と透明性を向上しますが、必要に応じて特定の作業部分を分離します  
- コンサルタントをユーザーとして、または投稿するグループ内に追加する  
- 誰かと共同作業して実稼働前のテストと監視を行いたい  

## <a name="where-do-i-add-users-and-their-roles-within-my-subscription"></a>サブスクリプション内でユーザーとそのロールを追加する場所

このAzure AD管理は重要な機能です。 [Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)\(Azure RBAC \) は、Azure リソースのきめ細かなアクセス管理を提供する承認システムです。  

ユーザーを追加する必要があると判断したら、どこにユーザーを追加するのか、どのリソースにアクセスする必要があるのかを理解する必要があります。 ユーザーがアクセスできるリソースのセットは、スコープと呼ばれます。  

作業しているプロジェクトとタスクを IT で監視してセキュリティ プロトコルを有効にする必要がある場合は、そのユーザーがサブスクリプション内でフル アクセスとアクセス許可を持つには、管理グループ内の管理者ロールを持っている必要があります。  

![Azure のアクセス レベルのスクリーンショット。](media/how-to-add-users-directory/access-management.png "Azure での管理グループを使用したロールの管理。")

ただし、別の開発者や同僚と共同作業している場合は、リソースレベルまたはリソース グループ レベルでのみアクセスが必要な場合があります。  

Azure RBAC の [概要に関するページを参照](../../role-based-access-control/overview.md) して、Azure RBAC のしくみとサブスクリプション内で機能する目的をよりよく理解してください。  

を使用してユーザーを追加する方法またはユーザーを削除するAzure Active Directory  

- [ユーザーの追加または削除 - Azure Active Directory | Microsoft Docs](../../active-directory/fundamentals/add-users-azure-active-directory.md)  

- [Azure ロールを割り当てる手順 - Azure RBAC |Microsoft Docs](../../role-based-access-control/role-assignments-steps.md)  
