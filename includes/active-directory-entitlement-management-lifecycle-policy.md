---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 88e11adadcc06875964146ad2046828267258038
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389376"
---
## <a name="lifecycle"></a>ライフサイクル

**[ライフサイクル]** タブでは、アクセス パッケージに対するユーザーの割り当ての有効期限を指定します。 ユーザーが割り当てを延長できるかどうかを指定することもできます。

1. **[有効期限]** セクションで、 **[Access package assignments expires]\(アクセス パッケージ割り当ての有効期限\)** を **[日付]** 、 **[日数]** 、または **[無期限]** に設定します。

    **[日付]** の場合、将来の有効期限の日付を選択します。

    **[日数]** の場合、0 日から 3660 日までの数値を指定します。

    選択内容に基づき、アクセス パッケージに対するユーザーの割り当ては、特定の日付または承認された後の特定の日数に期限切れになるか、または期限切れになりません。

1. **[Show advanced expiration settings]\(高度な有効期限設定の表示\)** をクリックして、追加設定を表示します。

    ![アクセス パッケージ - ライフサイクル有効期限の設定](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. ユーザーが割り当てを延長できるようにするには、 **[Allow users to extend access]\(ユーザーがアクセスを延長できるようにする\)** を **[はい]** に設定します。

    ポリシーで延長が許可されている場合、アクセス パッケージの割り当ての有効期限が切れる 14 日前と 1 日前にユーザーに電子メールが送信され、割り当ての延長を求めるメッセージが表示されます。

    ユーザーのアクセスが延長された場合、そのユーザーは、指定された延長日付 (ポリシーを作成したユーザーのタイムゾーンで設定された日付) より後にアクセス パッケージを要求することはできません。

1. 延長を許可するための承認を要求するには、 **[Require approval to grant extension]\(延長許可の承認を要求する\)** を **[はい]** に設定します。

    **[要求]** タブに指定されたのと同じ承認設定が使用されます。

1. **[次へ]** または **[更新]** をクリックします。
