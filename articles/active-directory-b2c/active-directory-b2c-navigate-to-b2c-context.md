---
title: "Azure Active Directory B2C: B2C テナントへの切り替え | Microsoft Docs"
description: "Active Directory B2C テナントのコンテキストに切り替える方法"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 0eb1b198-44d3-4065-9fae-16591a8d3eae
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/13/2017
ms.author: parakhj
ms.openlocfilehash: 6c1fd08c52f33a062d06e0593cbbe00346bb44f1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントへの切り替え

Azure AD B2C を構成するには、Azure AD B2C テナントのコンテキストに移動する必要があります。

## <a name="log-into-azure-ad-b2c-tenant"></a>Azure AD B2C テナントへのログイン

Azure AD B2C テナントに移動するには、Azure AD B2C テナントのグローバル管理者として Azure Portal にログインする必要があります。

1. [Azure Portal](http://portal.azure.com) にサインインします。
1. テナントを切り替えるには、右上隅にある電子メール アドレスまたは画像をクリックします。
1. 表示された `Directory` の一覧で、管理する Azure AD B2C テナントを選択します。

Azure Portal の表示が更新されます。  これで、Azure AD B2C テナントのコンテキストで Azure Portal にサインインしました。

## <a name="navigate-to-the-b2c-features-blade"></a>B2C 機能ブレードに移動する

1. 左側のナビゲーションで **[参照]** をクリックします。
1. **[> その他のサービス]** をクリックし、左側のナビゲーション ウィンドウで `Azure AD B2C` を検索します  (左側のスタート画面にピン留めするには、Azure AD B2C の左側にある星をクリックします)。
1. **[Azure AD B2C]** をクリックして B2C 機能ブレードにアクセスします。
   
    ![Screen shot of Browse to B2C features blade](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> B2C 機能ブレードにアクセスするには、B2Cテナントのグローバル管理者である必要があります。 他のテナントのグローバル管理者やテナントのユーザーはアクセスできません。  Azure Portal の右上隅にあるテナント スイッチャーを使用して、B2C テナントに切り替えることができます。
