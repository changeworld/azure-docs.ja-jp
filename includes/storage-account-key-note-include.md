---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460563"
---
## <a name="protect-your-access-keys"></a>アクセス キーの保護

ストレージ アカウントのアクセス キーは、ストレージ アカウントの root パスワードに似ています。 アクセス キーは常に慎重に保護してください。 キーを安全に管理およびローテーションするには、Azure Key Vault を使用します。 アクセス キーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーンテキストで保存したりしないでください。 キーが侵害された可能性があると思われる場合は、キーをローテーションしてください。

可能であれば、Blob Storage および Queue storage に対する要求の承認には、共有キーではなく Azure Active Directory (Azure AD) を使用します。 Azure AD では、共有キーよりも優れたセキュリティと使いやすさが実現されます。 Azure AD を使用したデータへのアクセスの承認の詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../articles/storage/common/storage-auth-aad.md)」を参照してください。
