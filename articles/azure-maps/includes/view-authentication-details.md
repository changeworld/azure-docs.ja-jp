---
title: Azure Maps の認証詳細を表示する
description: Azure portal を使用して Azure Maps の認証詳細を表示します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87126459"
---
Azure portal で Azure Maps アカウントの認証詳細を表示できます。 Azure portal の自分のアカウントで、 **[設定]** メニューの **[認証]** を選択します。

![認証の詳細](../media/how-to-manage-authentication/how-to-view-auth.png)

Azure Maps アカウントが作成されると、Azure portal の認証の詳細ページに Azure Maps の `x-ms-client-id` 値が示されます。 この値は、REST API 要求に使用されるアカウントを表します。 この値は、アプリケーション構成に格納し、Azure Maps で Azure AD 認証を使用するとき、http 要求を行う前に取得する必要があります。
