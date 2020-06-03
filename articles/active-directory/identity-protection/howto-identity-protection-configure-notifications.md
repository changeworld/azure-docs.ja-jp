---
title: Azure Active Directory Identity Protection の通知
description: 通知が調査作業にどのように役立つのかを説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 200ede6b4c5565a8eab95b0398abaa1c056c612f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853126"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection の通知

Azure AD Identity Protection では、ユーザーのリスクとリスク検出の管理に役立つ 2 種類の自動通知電子メールが送信されます。

- 危険な状態のユーザーが検出された電子メール
- 週間ダイジェスト電子メール

この記事では、両方の通知メールの概要を説明します。

## <a name="users-at-risk-detected-email"></a>危険な状態のユーザーが検出された電子メール

危険な状態のアカウントが検出されると、Azure AD Identity Protection は **[Users at risk detected]\(危険な状態のユーザーが検出されました\)** という件名のアラート メールを生成します。 このメールには、 **[リスクのフラグ付きユーザー](../reports-monitoring/concept-user-at-risk.md)** レポートへのリンクが含まれます。 ベスト プラクティスとして、危険な状態のユーザーをすぐに調べる必要があります。

このアラートを構成すると、アラートを生成するユーザー リスク レベルを指定できます。 指定したユーザーのリスク レベルに達すると、メールが生成されます。ただし、このユーザー リスク レベルに移行した後のユーザーについては、リスクが検出されたユーザーの新しいメール アラートは受信しません。 たとえば、中ユーザー リスクの場合に警告するようにポリシーを設定していて、ユーザー John が中リスクに移行した場合、John についてリスクが検出されたユーザーのメールを受信します。 ただし、その後に John が高リスクに移行した場合、または追加のリスク検出が発生した場合は、リスクが検出されたユーザーの 2 つ目のアラートは送信されません。

![危険な状態のユーザーが検出された電子メール](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>"リスクのあるユーザーが検出された警告" を構成する

管理者は以下を設定することができます。

- **このメールの生成をトリガーするユーザー リスク レベル** - 既定では、リスク レベルは "高" リスクに設定されます。
- **このメールの受信者** - 既定では、受信者にはすべてのグローバル管理者が含まれます。 グローバル管理者は、受信者として他のグローバル管理者、セキュリティ管理者、セキュリティ閲覧者を追加することもできます。
   - 必要に応じて、**アラート通知を受信する電子メールをさらに追加**することができます。この機能はプレビューであり、定義されているユーザーがリンク レポートを Azure portal で表示するには適切なアクセス許可が必要です。

"危険な状態のユーザー" メールは、**Azure portal** の **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[Identity Protection]**  >  **[リスクのあるユーザーが検出された警告]** で構成します。

## <a name="weekly-digest-email"></a>週間ダイジェスト電子メール

週間ダイジェスト電子メールには、新しいリスク検出の概要が含まれます。  
次の情報が含まれます。

- 新しい危険なユーザーが検出されました
- 新しい危険なサインインが検出されました (リアルタイムで)
- Identity Protection の関連するレポートへのリンク

![週間ダイジェスト電子メール](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

既定では、受信者にはすべてのグローバル管理者が含まれます。 グローバル管理者は、受信者として他のグローバル管理者、セキュリティ管理者、セキュリティ閲覧者を追加することもできます。

### <a name="configure-weekly-digest-email"></a>週刊ダイジェストの電子メールを構成する

管理者は、週刊ダイジェストの電子メールの送信を有効にしたり無効にしたりできるほか、メールの受信者として割り当てるユーザーを選択することができます。

週刊ダイジェストの電子メールは、**Azure portal** の **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[Identity Protection]**  >  **[週刊ダイジェスト]** で構成します。

## <a name="see-also"></a>関連項目

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
