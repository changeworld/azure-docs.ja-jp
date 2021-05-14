---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b739b84cbe1afcf69c3fe4627d35000ea4772348
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108792362"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
