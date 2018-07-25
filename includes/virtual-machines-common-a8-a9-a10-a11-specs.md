---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep
ms.custom: include file
ms.openlocfilehash: 296e92d803bb69376f286aa60cfb4a955b08010f
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "34669348"
---
## <a name="deployment-considerations"></a>デプロイに関する考慮事項
* **Azure サブスクリプション** – 多数のコンピューティング集中型インスタンスをデプロイするには、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

* 
  **価格と可用性** - これらの VM サイズは、Standard 価格レベルでのみ提供されます。 各 Azure リージョンで利用できるかどうかについては、[リージョン別の利用可能な製品] (https://azure.microsoft.com/regions/services/)) を参照してください。 
* **コア クォータ** – 場合によっては、Azure サブスクリプションのコア クォータを既定値から増やす必要があります。 サブスクリプションによっては、H シリーズを含む特定の VM サイズ ファミリにデプロイできるコア数が制限されることがあります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../articles/azure-supportability/how-to-create-azure-support-request.md) (無料) してください。 (既定の制限は、サブスクリプション カテゴリによって異なる場合があります。)
  
  > [!NOTE]
  > 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。 Azure のクォータは容量保証ではなくクレジット制限です。 クォータに関係なく、使用したコアに対してのみ課金されます。
  > 
  > 
* **仮想ネットワーク** – コンピューティング集中型インスタンスを使用するために、Azure [Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) は不要です。 ただし、多くのデプロイでは、少なくともクラウド ベースの Azure Virtual Network またはサイト間接続 (オンプレミス リソースへのアクセスが必要な場合) が必要になります。 必要に応じて、新しい仮想ネットワークを作成して、インスタンスをデプロイします。 アフィニティ グループ内の仮想ネットワークにコンピューティング集中型の VM を追加することはできません。
* **サイズ変更** – 特殊なハードウェアが使用されるため、コンピューティング集中型インスタンスのサイズ変更は同じサイズ ファミリ (H シリーズまたはコンピューティング集中型 A シリーズ) 内でのみ可能です。 たとえば、H シリーズの VM は H シリーズのあるサイズから別のサイズにのみ変更できます。 さらに、コンピューティング集中型以外のサイズからコンピューティング集中型サイズへのサイズ変更はサポートされていません。  

## <a name="rdma-capable-instances"></a>RDMA 対応のインスタンス
コンピューティング集中型インスタンス (H16r、H16mr、A8、A9) のサブセットには、リモート ダイレクト メモリ アクセス (RDMA) 接続のためのネットワーク インターフェイスが備わっています。 (NC24r のように 'r' を使用して指定された N シリーズの選択されたサイズでも、RDMA に対応できます)。このインターフェイスは、標準の Azure ネットワーク インターフェイスと同様に、他の VM サイズでも利用可能です。 
  
このインターフェイスにより、RDMA 対応インスタンスは InfiniBand (IB) ネットワークを介して通信することができ、H16r、H16mr、および RDMA 対応 N シリーズの仮想マシンでは FDR のレートで、A8 と A9 の仮想マシンでは QDR のレートで動作します。 これらの RDMA 機能により、特定の Message Passing Interface (MPI) アプリケーションのスケーラビリティとパフォーマンスが向上します。

> [!NOTE]
> Azure では、IB 上の IP はサポートされません。 RDMA over IB のみがサポートされます。
>

RDMA 対応の HPC VM は、同じ可用性セットまたは VM スケール セット (Azure Resource Manager デプロイ モデルを使用している場合) または同じクラウド サービス (クラシック デプロイ モデルを使用している場合) 内にデプロイします。 VM スケール セットを使用する場合は必ず、デプロイを 1 つの配置グループに制限してください。たとえば、Resource Manager テンプレートで、*singlePlacementGroup* プロパティを *true* に設定します。 RDMA 対応の HPC VM が Azure RDMA ネットワークにアクセスするための追加の要件があります。