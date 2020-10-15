---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 468b2f73e07d48081c9ed67ca6720e57ac578570
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859577"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL サーバーに対して Bring Your Own Key データ保護を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |Azure Database for MySQL データベース サーバーの保存データの暗号化にカスタマー マネージド キーを使用すると、キーとデータの管理における職務の分離を実装できます。 カスタマー マネージド キーを構成すると、データを暗号化するキーへのアクセスを保護および制御するために、そのキーが使用されます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 カスタマー マネージド キーの使用は、コンプライアンスのために必須になる場合があります。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[MySQL サーバーは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |仮想ネットワーク ベースのファイアウォール規則を使用すると、特定のサブネットから Azure Database for MySQL へのトラフィックを有効にしながら、トラフィックを Azure の境界内に留まるようすることができます。 このポリシーでは、使用されている仮想ネットワーク サービス エンドポイントが Azure Database for MySQL にあるかどうかを監査する方法が提供されます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |プライベート エンドポイント接続は、Azure Database for MySQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[MySQL サーバーでは、公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |パブリック ネットワーク アクセス プロパティを無効にすると、Azure Database for MySQL へのアクセスがプライベート エンドポイントのみに制限されるため、セキュリティが強化されます。 この構成は、Azure IP 範囲外のパブリック アドレス空間からのアクセスを厳密に無効にし、IP または仮想ネットワークベースのファイアウォール規則に一致するすべてのログインを拒否します。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
