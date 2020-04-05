---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361271"
---
### <a name="identity-tier"></a>ID 層 

Microsoft と Oracle のパートナーシップにより、Azure、OCI、Oracle アプリケーションの垣根を越えて統一された ID の設定が可能となっています。 JD Edwards EnterpriseOne または PeopleSoft アプリケーション スイートで、Azure AD と Oracle IDCS との間にシングル サインオンを設定するには、Oracle HTTP Server (OHS) のインスタンスが必要となります。

OHS は、アプリケーション層へのリバース プロキシとして機能します。つまり、エンド アプリケーションに対するすべての要求が OHS を経由します。 Oracle Access Manager WebGate は、エンド アプリケーションに向かうすべての要求をインターセプトする OHS Web サーバー プラグインです。 アクセスしようとしているリソースが保護されている (認証済みのセッションが必要である) 場合、WebGate がユーザーのブラウザーを介して Identity Cloud Service との OIDC 認証フローを開始します。 OpenID Connect WebGate でサポートされるフローの詳細については、[Oracle Access Manager のドキュメント](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)を参照してください。

この構成では、既に Azure AD にログインしているユーザーが Oracle Identity Cloud Service を介し、再ログインなしで、JD Edwards EnterpriseOne または PeopleSoft アプリケーションに移動することができます。 このソリューションをデプロイすることによって、お客様はシングル サインオンの利点を得ることができます (資格情報が 1 セットで済む、サインオン エクスペリエンスとセキュリティが向上する、ヘルプデスク コストが軽減されるなど)。

Azure AD で JD Edwards EnterpriseOne または PeopleSoft のシングル サインオンを設定する方法について詳しくは、関連する [Oracle のホワイトペーパー](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)を参照してください。