---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7d9f08f3795d96a5bfc4a318dc90a4711be2e3ce
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510921"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
