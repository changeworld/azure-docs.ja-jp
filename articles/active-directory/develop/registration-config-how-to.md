---
title: 指定の API のアクセス許可を選択する方法 | Microsoft Docs
description: Azure AD で開発中または登録中のカスタム アプリケーションの認証エンドポイントを見つける方法。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: d9db2182c20663cc048a5c9501df2586db25d2f3
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364933"
---
# <a name="how-to-configure-endpoints"></a>エンドポイントの構成方法

アプリケーションの認証エンドポイントは [Azure Portal](https://portal.azure.com) で見つけることができます。

-   [Azure Portal](https://portal.azure.com) に移動します。

-   左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。

-   **[アプリの登録]** をクリックして、**[エンドポイント]** を選択します。

-   **[エンドポイント]** ページが開き、テナントのすべての認証エンドポイントが一覧表示されます。

-   使用する認証プロトコルに固有のエンドポイントをアプリケーション ID と共に使用して、アプリケーション固有の認証要求を作成します。

## <a name="next-steps"></a>次の手順
[Azure Active Directory 開発者ガイド](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
