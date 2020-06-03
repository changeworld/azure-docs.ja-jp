---
title: Azure Linux 仮想マシンにおける SQL Server の概要 | Microsoft Docs
description: Azure の Linux 仮想マシンで各 SQL Server エディションを実行する方法について説明します。 すべての Linux SQL Server VM イメージと関連コンテンツへのダイレクト リンクが記載されています。
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 61b8982868bf14a7b5a5441049cb7fa21cdd9d6d
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266034"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure 仮想マシン (Linux) における SQL Server の概要
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Azure 仮想マシン上の SQL Server では、オンプレミスのハードウェアを一切管理することなく全バージョンの SQL Server をクラウドで利用することができます。 また、SQL Server VM によって従量課金制のライセンス料が単純化されます。

Azure 仮想マシンは、世界中のさまざまな[リージョン](https://azure.microsoft.com/regions/)で実行されます。 また、[マシンのサイズ](../../../virtual-machines/windows/sizes.md)も各種用意されています。 適切なバージョン、エディション、オペレーティング システムの SQL Server VM を仮想マシン イメージ ギャラリーで作成することができます。 さまざまな SQL Server ワークロードに合った最適な仮想マシンを選ぶことができます。 

## <a name="get-started-with-sql-vms"></a><a id="create"></a>SQL VM を使ってみる

まず、必要なバージョン、エディション、オペレーティング システムの SQL Server 仮想マシン イメージを選んでください。 以下のセクションでは、SQL Server 仮想マシン ギャラリー イメージに関する、Azure Portal へのリンクを記載しています。

> [!TIP]
> SQL イメージの価格の詳細については、[Linux SQL Server VM の価格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)に関するページを参照してください。

| Version | オペレーティング システム | Edition |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> 利用できる Windows SQL Server 仮想マシン イメージを確認するには、「[Azure Virtual Machines における SQL Server の概要 (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)」を参照してください。

## <a name="installed-packages"></a><a id="packages"></a> インストール済みパッケージ

Linux に SQL Server を構成するときには、データベース エンジン パッケージをインストールしてから、要件に応じてオプションのパッケージをインストールします。 ほとんどのパッケージは、SQL Server の Linux 仮想マシン イメージにより自動でインストールされます。 以下の表は、各ディストリビューションでインストールされているパッケージを示したものです。

| Distribution | [データベース エンジン](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [ツール](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server エージェント](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [フルテキスト検索](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA アドオン](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![no](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| SLES | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![no](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![no](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| Ubuntu | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![はい](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>関連製品およびサービス

### <a name="linux-virtual-machines"></a>Linux Virtual Machines

* [Virtual Machines の概要](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>ストレージ

* [Microsoft Azure Storage の概要](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>ネットワーク

* [仮想ネットワークの概要](../../../virtual-network/virtual-networks-overview.md)
* [Azure 内の IP アドレス](../../../virtual-network/public-ip-addresses.md)
* [Azure Portal での完全修飾ドメイン名の作成](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux)
* [Azure SQL Database の比較](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>次のステップ

Azure Linux 仮想マシン上の SQL Server の概要:

* [Azure Portal での SQL Server VM の作成](sql-vm-create-portal-quickstart.md)

Linux 上の SQL VM についてよく寄せられる質問とその回答:

* [Azure Linux Virtual Machines における SQL Server に関する FAQ](frequently-asked-questions-faq.md)
