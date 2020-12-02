---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026660"
---
キー コンテナーでは、最大 1,024 個のアクセス ポリシー エントリがサポートされており、各エントリでは、特定のセキュリティ プリンシパルに個別のアクセス許可セットを付与します。 このような制限があるため、可能な場合は、アクセス ポリシーを個別のユーザーではなく、ユーザーのグループに割り当てることをお勧めします。 グループを使用すると、組織内の複数のユーザーに対するアクセス許可を管理しやすくなります。 詳細については、「[Azure Active Directory グループを使用してアプリとリソースへのアクセスを管理する](../articles/active-directory/fundamentals/active-directory-manage-groups.md)」を参照してください。

Key Vault のアクセス制御の詳細については、「[Azure Key Vault セキュリティ:ID 管理とアクセス管理](../articles/key-vault/general/overview-security.md#identity-and-access-management)」を参照してください。