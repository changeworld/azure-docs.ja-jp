---
title: 指定の API のアクセス許可を選択する方法 | Microsoft Docs
description: Azure AD で開発中または登録中のカスタム アプリケーションの認証エンドポイントを見つける方法。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: dcb3a8f735b72b2408e28d2f0dc61b2c634baf96
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366635"
---
# <a name="how-to-select-permissions-for-a-given-api"></a>指定の API のアクセス許可を選択する方法

アプリケーションの認証エンドポイントは [Azure Portal](https://portal.azure.com) で見つけることができます。

-   [Azure Portal](https://portal.azure.com) に移動します。

-   左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。

-   **[アプリの登録]** をクリックして、**[エンドポイント]** を選択します。

-   **[エンドポイント]** ページが開き、テナントのすべての認証エンドポイントが一覧表示されます。

-   使用する認証プロトコルに固有のエンドポイントをアプリケーション ID と共に使用して、アプリケーション固有の認証要求を作成します。

## <a name="next-steps"></a>次の手順
[Azure Active Directory 開発者ガイド](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
