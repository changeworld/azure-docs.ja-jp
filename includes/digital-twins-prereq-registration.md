---
author: baanders
description: Azure Digital Twins のインクルード ファイル - アプリの登録を設定するための前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: a700f45e90334033cfc4463043017166d5f7ffd9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111993489"
---
この記事で使用されているすべてのリソースを認証するには、**Azure Active Directory (Azure AD)[ の](../articles/active-directory/fundamentals/active-directory-whatis.md)アプリの登録** を設定する必要があります。 "[AutoRest を使用して アプリの登録の作成方法](../articles/digital-twins/how-to-create-app-registration-portal.md)に関するページでの指示に従って設定します。 

アプリの登録が完了したら、登録の **アプリケーション (クライアント) ID**、**ディレクトリ (テナント) ID**、**クライアント シークレット** が必要になります ([Azure portal](../articles/digital-twins/how-to-create-app-registration-portal.md#collect-important-values) で確認してください)。 これらの値をメモしておき、後で Azure Digital Twins API へのアクセス許可を付与するために使用します。