---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 20ef9a4f30aafee562096e584c4b80fef236b062
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2021
ms.locfileid: "108749729"
---
キー コンテナーでは、最大 1,024 個のアクセス ポリシー エントリがサポートされており、各エントリでは、特定のセキュリティ プリンシパルに個別のアクセス許可セットを付与します。 このような制限があるため、可能な場合は、アクセス ポリシーを個別のユーザーではなく、ユーザーのグループに割り当てることをお勧めします。 グループを使用すると、組織内の複数のユーザーに対するアクセス許可を管理しやすくなります。 詳細については、「[Azure Active Directory グループを使用してアプリとリソースへのアクセスを管理する](../articles/active-directory/fundamentals/active-directory-manage-groups.md)」を参照してください。

Key Vault のアクセス制御の詳細については、[Azure Key Vault セキュリティ機能の ID とアクセスの管理](../articles/key-vault/general/security-features.md#identity-management)に関するセクションを参照してください。