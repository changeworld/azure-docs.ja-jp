---
title: Azure Stack Edge Pro GPU デバイス上のローカル Azure Resource Manager とは
description: Azure Stack Edge デバイス上のローカル Azure Resource Manager の概要を示します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 8d200195540f92c88ae3dc93737ea09a461e6c26
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113114561"
---
# <a name="what-is-local-azure-resource-manager-on-azure-stack-edge"></a>Azure Stack Edge 上のローカル Azure Resource Manager とは

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager には、Azure サブスクリプションのリソースを作成、更新、削除できる管理レイヤーが用意されています。 Azure Stack Edge デバイスにより、ローカル サブスクリプションで VM を作成、更新、削除する同じ Azure Resource Manager API がサポートされています。 このサポートにより、クラウドと一貫性のある方法でデバイスを管理できるようになります。 

この記事では、Azure Stack Edge デバイス上のローカル API に接続するために使用できるローカル Azure Resource Manager の概要を示します。

## <a name="about-local-azure-resource-manager"></a>ローカル Azure Resource Manager について

ローカル Azure Resource Manager では、Resource Manager テンプレートを使用して、Azure Stack Edge デバイスへのすべての呼び出しに対して一貫した管理レイヤーが提供されます。 以下のセクションでは、ローカル Azure Resource Manager の利点について説明します。

#### <a name="consistent-management-layer"></a>一貫性のある管理レイヤー

ローカル Azure Resource Manager には、Azure Stack Edge デバイス API を呼び出したり、VM の作成、更新、削除などの操作を実行したりするための一貫した管理レイヤーが用意されています。 

1. REST API または SDK から要求を送信すると、デバイス上のローカル Azure Resource Manager で要求が受信されます。 
1. ローカル Azure Resource Manager では、セキュリティ トークン サービス (STS) を使用して要求の認証と承認が行われます。 STS は、セキュリティ トークンの作成、検証、更新、および取り消しを担当します。 STS では、アクセス トークンと更新トークンの両方の種類のセキュリティ トークンが作成されます。 これらのトークンは、デバイスと、ローカル Azure Resource Manager 経由でデバイスにアクセスするクライアントとの間の継続的な通信に使用されます。
1. 次に、Resource Manager によって要求がリソース プロバイダーに送信され、要求されたアクションが実行されます。   

    Azure Stack Edge に事前登録されているリソース プロバイダーは次のとおりです。

    - **コンピューティング**: `Microsoft.Compute` (コンピューティング リソース プロバイダー) を使用すると、Azure Stack Edge に VM をデプロイできます。 コンピューティング リソース プロバイダーには、VM と VM 拡張機能を作成する機能があります。 

    - **ネットワーク リソース プロバイダー**: `Microsoft.Network` (ネットワーク リソース プロバイダー) を使用すると、ネットワーク インターフェイスや仮想ネットワークなどのリソースを作成できます。

    - **ストレージ リソース プロバイダー**: `Microsoft.Storage` (ストレージ リソース プロバイダー) によって、Azure 整合 BLOB ストレージ サービスと Key Vault のアカウント管理が提供され、シークレット (パスワードや証明書など) の管理と監査が提供されます。  
    
    - **ディスク リソース プロバイダー**: `Microsoft.Disks` (ディスク リソース プロバイダー) を使用すると、VM の作成に使用できるマネージド ディスクを作成できます。

    リソースは管理可能な項目であり、Azure Stack Edge を介して利用でき、リソース プロバイダーがリソースの提供を担います。 例えば、仮想マシン、ストレージ アカウント、仮想ネットワークなどがあります。 コンピューティング リソース プロバイダーは、仮想マシン リソースを提供します。    

すべての要求は同じ API を介して処理されるため、すべての異なるツールで一貫した結果と機能が得られます。

次の図は、すべての API 要求を処理するメカニズムと、ローカル Azure Resource Manager がそれらの要求を処理するための一貫した管理レイヤーを提供する上で果たす役割を示しています。

![Azure Resource Manager の図。](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


#### <a name="use-of-resource-manager-templates"></a>Resource Manager テンプレートの使用

Azure Resource Manager のもう 1 つの主な利点は、Resource Manager テンプレートを使用できることです。 これらは、リソースを一貫して繰り返しデプロイするために使用できる宣言構文の JavaScript Object Notation (JSON) ファイルです。 宣言型構文を使用すると、一連のプログラミング コマンドを記述しなくても、"作成しようとしているもの" を明確に宣言できます。 たとえば、これらの宣言型構文テンプレートを使用して、Azure Stack Edge デバイスに仮想マシンをデプロイできます。 詳細については、[テンプレートを使用した Azure Stack Edge デバイスへの仮想マシンのデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)に関するページを参照してください。

## <a name="connect-to-the-local-azure-resource-manager"></a>ローカル Azure Resource Manager に接続する

Azure Stack Edge デバイスに仮想マシン、共有、またはストレージ アカウントを作成するには、対応するリソースを作成する必要があります。 たとえば、仮想マシンの場合、VM 上に、ネットワーク、ディスク、およびストレージのリソース プロバイダーからのリソース (ネットワーク インターフェイス、OS、データ ディスクなど) が必要になります。 

リソース プロバイダーからのリソースの作成を要求するには、最初にローカル Azure Resource Manager に接続する必要があります。 詳細な手順については、「[Azure Stack Edge デバイスで Azure Resource Manager に接続する](azure-stack-edge-gpu-connect-resource-manager.md)」を参照してください。

Azure Resource Manager に初めて接続するときは、パスワードをリセットする必要もあります。 詳細な手順については、[Azure Resource Manager パスワードのリセット](azure-stack-edge-gpu-set-azure-resource-manager-password.md)に関するページを参照してください。


## <a name="azure-resource-manager-endpoints"></a>Azure Resource Manager エンドポイント

ローカル Azure Resource Manager および STS サービスはデバイス上で実行され、これらには特定のエンドポイントでアクセスできます。 次の表は、これらのサービスによってデバイスで公開されているさまざまなエンドポイント、サポートされているプロトコル、およびそれらのエンドポイントにアクセスするためのポートをまとめたものです。 

| # | エンドポイント | サポートされるプロトコル | 使用されるポート | 使用目的 |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | オートメーションのために Azure Resource Manager に接続する |
| 2. | セキュリティ トークン サービス | https | 443 | アクセス トークンと更新トークンを使用して認証する |


## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro GPU デバイス上のローカル Azure Resource Manager に接続します](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)。
