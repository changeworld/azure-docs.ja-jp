---
author: baanders
description: Azure Digital Twins のインクルード ファイル - アプリの登録を設定するための前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 128aa82762b80958f02cf22e9e7017bb334fde0c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437849"
---
この記事で使用されているすべてのリソースを認証するには、**Azure Active Directory (Azure AD)[ の](../articles/active-directory/fundamentals/active-directory-whatis.md)アプリの登録** を設定する必要があります。 [アプリの登録の作成方法](../articles/digital-twins/how-to-create-app-registration-portal.md)に関するページに記載の指示に従って設定します。 

アプリの登録が完了したら、登録の **アプリケーション (クライアント) ID**、**ディレクトリ (テナント) ID**、**クライアント シークレット** が必要になります ([Azure portal](../articles/digital-twins/how-to-create-app-registration-portal.md#collect-important-values) で確認してください)。 これらの値をメモしておき、後で Azure Digital Twins API へのアクセス許可を付与するために使用します。