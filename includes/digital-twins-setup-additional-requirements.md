---
author: baanders
description: Azure Digital Twins 設定での考えられる追加の要件のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 230304677b78f00b2d1288c846f8bf704cd8a497
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407471"
---
アプリ登録を正常に設定する (それにより、使用可能な Azure Digital Twins インスタンスの設定を完了する) には、組織にサブスクリプションの所有者からの追加のアクションが必要になる可能性があります。 必要な手順は、組織の具体的な設定によって異なることがあります。

所有者による実行が必要になる可能性がある一般的な潜在的なアクティビティのいくつかを次に示します。 これらの操作は、Azure portal の [*Azure AD アプリ登録*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)のページから実行できます。
* アプリ登録に対する管理者の同意を付与する。 組織では、サブスクリプション内のすべてのアプリ登録について、Azure AD で *[管理者の同意が必要]* がグローバルに有効になっている可能性があります。 その場合は、有効にするアプリ登録について、アプリ登録の *[API のアクセス許可]* ページで所有者がユーザーの会社に対してこのボタンを選択する必要があります。

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text=" [API のアクセス許可] にある [管理者の同意の付与] ボタンのポータル ビュー":::
  - 同意が正常に付与された場合は、Azure Digital Twins のエントリに _[ **(ユーザーの会社)** に付与されました]_ の *[状態]* 値が表示されます。
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text=" [API のアクセス許可] で会社に付与された管理者の同意のポータル ビュー":::
* パブリック クライアント アクセスをアクティブ化する
* Web およびデスクトップへのアクセスに特定の応答 URL を設定する
* 暗黙の OAuth2 認証フローを許可する

アプリ登録とそのさまざまな設定オプションの詳細については、「[*Microsoft ID プラットフォームにアプリケーションを登録する*](https://docs.microsoft.com/graph/auth-register-app-v2)」を参照してください。

これで Azure Digital Twins インスタンスの準備が完了し、それを管理するためのアクセス許可が割り当てられ、それにアクセスするためのクライアント アプリのアクセス許可が設定されました。