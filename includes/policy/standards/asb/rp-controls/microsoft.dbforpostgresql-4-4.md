---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/28/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 613385d0f757462a92dc021f87e5d62e8b7087c4
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108211914"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
