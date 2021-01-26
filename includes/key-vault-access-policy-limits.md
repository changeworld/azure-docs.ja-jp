---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934538"
---
キー コンテナーでは、最大 1,024 個のアクセス ポリシー エントリがサポートされており、各エントリでは、特定のセキュリティ プリンシパルに個別のアクセス許可セットを付与します。 このような制限があるため、可能な場合は、アクセス ポリシーを個別のユーザーではなく、ユーザーのグループに割り当てることをお勧めします。 グループを使用すると、組織内の複数のユーザーに対するアクセス許可を管理しやすくなります。 詳細については、「[Azure Active Directory グループを使用してアプリとリソースへのアクセスを管理する](../articles/active-directory/fundamentals/active-directory-manage-groups.md)」を参照してください。

Key Vault のアクセス制御の詳細については、「[Azure Key Vault セキュリティ:ID 管理とアクセス管理](../articles/key-vault/general/security-overview.md#identity-management)」を参照してください。