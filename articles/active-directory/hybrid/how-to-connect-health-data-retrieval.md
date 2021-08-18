---
title: データ取得のための Azure AD Connect Health 命令 | Microsoft Docs
description: このページでは、Azure AD Connect Health からデータを取得する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435cc73d9c2a91a9c72bf07258a15dced581d580
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110063"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>データ取得のための Azure AD Connect Health 命令

このドキュメントでは、Azure AD Connect を使用して Azure AD Connect Health からデータを取得する方法について説明します。

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>正常性アラートが構成されているユーザーのすべての電子メール アドレスを取得する。

アラートを受信するように Azure AD Connect Health で構成されているすべてのユーザーの電子メール アドレスを取得するには、次の手順に従います。

1.  [Azure Active Directory Connect Health] ブレードにアクセスして、左側のナビゲーション バーから **[同期サービス]** を選択します。
 ![同期サービス](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  **[アラート]** タイルをクリックします。</br>
 ![Alert](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  **[通知設定]** をクリックします。
 ![通知](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  **[通知設定]** ブレードに、正常性アラート通知の受信者として有効になっている電子メール アドレスの一覧が表示されます。
 ![電子メール](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>AD FS での不正なパスワードの試行でフラグが設定されたアカウントを取得する

AD FS での不正なパスワードの試行でフラグが設定されたアカウントを取得するには、次の手順を使用します。

1.  [Azure Active Directory Health] ブレードにアクセスして、 **[同期エラー]** を選択します。
 ![同期エラー](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  **[同期エラー]** ブレードで、 **[エクスポート]** をクリックします。 これにより、記録された同期エラーの一覧がエクスポートされます。
 ![エクスポート](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>次の手順
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health エージェントのインストール](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health の操作](how-to-connect-health-operations.md)
* [Azure AD Connect Health の FAQ](reference-connect-health-faq.yml)
* [Azure AD Connect Health のバージョンの履歴](reference-connect-health-version-history.md)