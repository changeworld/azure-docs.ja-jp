---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef0ad097f0dff99de71096cb24eba511a21bf06a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122260959"
---
## <a name="protect-your-access-keys"></a>アクセス キーの保護

ストレージ アカウントのアクセス キーは、ストレージ アカウントの root パスワードに似ています。 アクセス キーは常に慎重に保護してください。 キーを安全に管理およびローテーションするには、Azure Key Vault を使用します。 アクセス キーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーンテキストで保存したりしないでください。 キーが侵害された可能性があると思われる場合は、キーをローテーションしてください。

> [!NOTE]
> BLOB およびキューのデータに対する要求を承認するには、共有キーではなく、可能であれば Azure Active Directory (Azure AD) を使用することをお勧めします。 Azure AD では、共有キーよりも優れたセキュリティと使いやすさが実現されます。 Azure AD を使用したデータへのアクセスの承認の詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../articles/storage/blobs/authorize-access-azure-active-directory.md)」を参照してください。