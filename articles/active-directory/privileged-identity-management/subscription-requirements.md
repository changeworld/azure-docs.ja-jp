---
title: Privileged Identity Management を使用するためのライセンス要件 - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) を使用するためのライセンスの要件について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895112"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Privileged Identity Management を使用するためのライセンスの要件

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用するには、有効なライセンスがディレクトリになければいけません。 さらに、管理者と関連ユーザーにライセンスが割り当てられている必要があります。 この記事では、Privileged Identity Management を使用するためのライセンス要件について説明します。

## <a name="prerequisites"></a>前提条件

Privileged Identity Management を使用するには、お客様のディレクトリに次のいずれかの有料ライセンスまたは試用版ライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

詳細については、「[Azure Active Directory とは](../fundamentals/active-directory-whatis.md)」を参照してください。

## <a name="which-users-must-have-licenses"></a>ライセンスが必要なユーザー

Privileged Identity Management を操作する管理者または PIM の恩恵を受けるユーザーには、それぞれライセンスが必要です。 たとえば、次のようになります。

- PIM を使用して管理された Azure AD ロールを持つ管理者
- PIM を使用して管理された Azure リソース ロールを持つ管理者
- 特権ロール管理者のロールに割り当てられた管理者
- PIM を使用して管理された Azure AD ロールに対象として割り当てられたユーザー
- PIM で要求を承認または却下できるユーザー
- Just-In-Time 割り当てまたは直接割り当て (時間ベース) を使用して Azure リソース ロールに割り当てられたユーザー  
- アクセス レビューに割り当てられたユーザー
- アクセス レビューを実行するユーザー

お客様のユーザーにライセンスを割り当てる方法については、「[Azure Active Directory ポータルを使用したライセンスの割り当てと削除](../fundamentals/license-users-groups.md)」を参照してください。

## <a name="what-happens-when-a-license-expires"></a>ライセンスの有効期限が切れた場合に発生すること

Azure AD Premium P2、EMS E5、または試用版ライセンスの有効期限が切れた場合、Privileged Identity Management の機能はお使いのディレクトリで利用できなくなります。

- Azure AD ロールへの永続的なロールの割り当てには影響しません。
- 特権ロールのアクティブ化、特権アクセスの管理、または特権ロールのアクセス レビューの実行のために、Azure portal の Privileged Identity Management サービスと、Privileged Identity Management の Graph API コマンドレットおよび PowerShell インターフェイスを利用することはできなくなります。
- ユーザーが今後特権ロールをアクティブ化できなくなるため、Azure AD ロールの候補ロールの割り当ては削除されます。
- Azure AD ロールのすべての実行中のアクセス レビューが終了し、Privileged Identity Management の構成設定が削除されます。
- ロールの割り当てを変更しても、Privileged Identity Management から電子メールが送信されなくなります。

## <a name="next-steps"></a>次の手順

- [Privileged Identity Management をデプロイする](pim-deployment-plan.md)
- [Privileged Identity Management の使用を開始する](pim-getting-started.md)
- [Privileged Identity Management で管理できないロール](pim-roles.md)
