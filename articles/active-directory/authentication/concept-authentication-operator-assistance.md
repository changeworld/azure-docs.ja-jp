---
title: Azure Active Directory でのオペレーター アシスタンス
description: Azure Active Directory のオペレーター アシスタンス機能の非推奨化について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: justinha
author: rckyplln
manager: daveba
ms.reviewer: ripull
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c00fb3c68955a888bbc383fe59aef8f7ed75835
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270059"
---
# <a name="how-to-enable-and-disable-operator-assistance"></a>オペレーター アシスタンスを有効または無効にする方法

オペレーター アシスタンスは、自動転送ではなくオペレーターが通話を手動で転送できるようにする Azure AD 内の機能です。 この設定を有効にした場合、会社の電話番号がダイヤルされて応答すると、オペレーターは指定された内線番号に呼び出しを転送するように求められます。

オペレーター アシスタンスは、テナント全体または個々のユーザーに対して有効にすることができます。 この設定が **[オン]** の場合は、テナント全体でオペレーター アシスタンスが有効になります。 既定の方法として **[電話]** を選択し、会社の電話番号の一部として内線番号 (**x** で示されます) が指定された場合、オペレーターは電話を手動で転送できます。

たとえば、米国の顧客の会社の電話番号が 425-555-1234x5678 であるとします。 オペレーター アシスタンスが有効になっている場合は、システムによって 425-555-1234 にダイヤルされます。 応答すると、顧客 (オペレーターとも呼ばれます) は、呼び出しを内線番号 5678 に転送するように求められます。 転送されて応答すると、システムによって通常の MFA プロンプトが表示され、承認を待機します。

この設定が **[オフ]** の場合は、電話番号の一部として内線番号に自動的にダイヤルされます。 管理者は、内線番号の前に '@' を付けることによって、オペレーター アシスタンスを有効にする必要がある個々のユーザーを指定できます。 たとえば、425-555-1234x@5678 では、設定が **[オフ]** の場合でも、オペレーター アシスタンスを使用する必要があることを示します。

独自のテナントのこの機能の状態を確認するには、[Azure AD ポータル](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade)に移動し、左側のペインで **[セキュリティ]**  >  **[MFA]**  >  **[電話の設定]** をクリックします。 設定が **[オン]** または **[オフ]** のいずれであるかを確認するには、 **[内線への転送に必要なオペレーター]** をオンにします。 

![オペレーター アシスタンスの設定のスクリーンショット](./media/concept-authentication-operator-assistance/settings.png)

次のガイダンスを使用すると、信頼性とセキュリティを向上させ、シームレスな MFA エクスペリエンスを作成することができます。

- Multi-Factor Authentication またはセルフサービス パスワード リセット (有効にした場合) に使用する[直通電話番号](https://aka.ms/mfasetup) (内線番号を含まない) または[その他の方法](concept-authentication-methods.md)を登録します。 
- 管理者は、[Multi-Factor Authentication](howto-mfa-userdevicesettings.md#add-authentication-methods-for-a-user) または[セルフサービス パスワード リセット](tutorial-enable-sspr.md) (有効にした場合) に使用する直通電話番号 (内線番号を含まない) をユーザーの代わりに登録します。 
- 電話システムで、自動アテンダント機能がサポートされるようにします。 
 
