---
title: Azure アプリケーション ソリューション テンプレート プラン発行ガイド | Azure Marketplace
description: この記事では、Azure Marketplace でソリューション テンプレートを発行するための要件を説明します
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084857"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure アプリケーション:ソリューション テンプレート プランの発行要件

この記事では、Azure Marketplace で Azure アプリケーションのプランを発行するための 1 つの方法である、ソリューション テンプレート プラン タイプの要件について説明します。 ソリューション テンプレート プラン タイプでは、ソリューション インフラストラクチャを自動的にデプロイするために [Azure Resource Manager テンプレート (ARM テンプレート)](../azure-resource-manager/templates/overview.md) が必要になります。

次の条件が必須であるときは、Azure アプリケーション ソリューション テンプレート プラン タイプを使用します。

- ソリューションで、VM、ネットワーク、およびストレージ リソースの組み合わせなど、単一の VM を超える追加のデプロイと構成の自動化が必要となる。
- 顧客がソリューションを自分で管理する。

このプラン タイプについてユーザーに表示される行動喚起は、"今すぐ入手する" です。

## <a name="requirements-for-solution-template-offers"></a>ソリューション テンプレート プランの要件

| **必要条件** | **詳細**  |
| ---------------  | -----------  |
|請求/メータリング    |  ソリューション テンプレート プランは、トランザクション プランではありませんが、Microsoft のコマーシャル マーケットプレースを通じて課金される有料 VM プランをデプロイする目的に使用できます。 ソリューションの ARM テンプレートによってデプロイされるリソースは、顧客の Azure サブスクリプションでプロビジョニングされます。 従量課金制 (PAYGO) の仮想マシンが Microsoft 経由で顧客とトランザクション処理され、顧客の Azure サブスクリプションにより課金されます。<br/> ライセンス持ち込み (BYOL) の場合、Microsoft が顧客サブスクリプションで生じるインフラストラクチャ コストを請求し、ユーザーが顧客に対して直接ソフトウェア ライセンス料金を処理します。   |
|Azure と互換性がある仮想ハード ディスク (VHD)  |   VM は、Windows または Linux 上に構築されている必要があります。 詳細については、次を参照してください。 <ul> <li>[Azure アプリケーション プランを作成する](./partner-center-portal/create-new-azure-apps-offer.md) (Windows VHD 用)</li><li>[Azure で動作保証済みの Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (Linux VHD 用)。</li></ul> |
| 顧客の利用状況属性 | Azure Marketplace に発行されるすべてのソリューション テンプレートで、顧客の利用状況属性を有効にする必要があります。 顧客の利用状況属性とそれを有効にする方法の詳細については、「[Azure パートナーの顧客の利用状況の属性](./azure-partner-customer-usage-attribution.md)」をご覧ください。  |
| Managed Disks を使用する | [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) は、Azure における IaaS VM の永続化ディスクの既定のオプションです。 Managed Disks は、ソリューション テンプレートで使用する必要があります。 <br> <br> 1.Azure ARM テンプレートで Managed Disks を使用するための[ガイダンス](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)と[サンプル](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)に従って、ソリューション テンプレートを更新します。 <br> <br> 2.次の手順に従って、Managed Disks の基盤となる VHD をストレージ アカウントにインポートし、VHD をイメージとして Marketplace に発行します。 <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>次のステップ

- まだ行っていない場合は、Azure Marketplace について[学習](https://azuremarketplace.microsoft.com/sell)してください。
- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)して、プランを作成するか完成させます。
- 詳細については、「[Azure アプリケーション プランを作成する](./partner-center-portal/create-new-azure-apps-offer.md)」を参照してください。
