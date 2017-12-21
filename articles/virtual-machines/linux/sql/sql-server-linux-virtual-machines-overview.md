---
title: "Azure Linux 仮想マシンにおける SQL Server の概要 | Microsoft Docs"
description: "Azure の Linux 仮想マシンで各 SQL Server エディションを実行する方法について説明します。 すべての Linux SQL Server VM イメージと関連コンテンツへのダイレクト リンクが記載されています。"
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: f044fcba92eb7aa7f4ac44608571fab3db0e03f5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure 仮想マシン (Linux) における SQL Server の概要

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

このトピックでは、Azure 仮想マシン (VM) で SQL Server を実行するための選択肢を、[ポータル イメージへのリンク](#option-1-create-a-sql-vm-with-per-minute-licensing)と併せて紹介します。

> [!NOTE]
> 既に SQL Server を使い慣れており、SQL Server Linux VM をデプロイする方法を確認するだけの場合は、[Azure での Linux SQL Server VM のプロビジョニング](provision-sql-server-linux-virtual-machine.md)に関するページを参照してください。 SQL Server がインストールされた Windows VM を作成したい場合は、[Azure での Windows SQL Server VM のプロビジョニング](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)に関するページを参照してください。

データベースの管理者または開発者の場合、Azure VM には、オンプレミスの SQL Server ワークロードとアプリケーションをクラウドに移動する方法が用意されています。

## <a name="scenarios"></a>シナリオ

データを Azure でホストするにはさまざまな理由があります。 Azure でアプリケーションを開発したり、アプリケーションを Azure に移行したりすると、Azure のバックエンド データの検索パフォーマンスも高まります。 グローバルな展開と障害復旧のために、自動的に複数のデータ センターにアクセスできるようになります。 データは、高度なセキュリティで保護され、耐久性に優れています。

Azure VM で実行されている SQL Server は、リレーショナル データを Azure に格納するための 1 つのオプションです。 Azure SQL Database サービスを使用するオプションもあります。 仮想マシンに SQL Server をインストールするか、Azure SQL Database を使用するかの選択に関する詳細については、「[クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)」を参照してください。

## <a id="create"></a> 新しい SQL VM の作成

新しい SQL VM を作成する詳細な手順を示したチュートリアルは、[Azure での Linux SQL Server VM のプロビジョニング](provision-sql-server-linux-virtual-machine.md)に関するページを参照してください。

次の表には、仮想マシン ギャラリーの最新の SQL Server イメージのマトリックスが示されています。 指定したバージョン、エディション、およびオペレーティング システムで新しい SQL VM の作成を開始するには、いずれかのリンクをクリックします。

> [!TIP]
> これらのイメージの VM と SQL の料金については、[Linux SQL Server VM の料金に関するページ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。

| バージョン | オペレーティング システム | エディション |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> 利用できる Windows SQL Server 仮想マシン イメージを確認するには、「[Azure Virtual Machines における SQL Server の概要 (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

## <a id="packages"></a> インストール済みパッケージ

Linux に SQL Server を構成するときには、データベース エンジン パッケージをインストールしてから、要件に応じてオプションのパッケージをインストールします。 ほとんどのパッケージは、SQL Server の Linux 仮想マシン イメージにより自動でインストールされます。 以下の表は、各ディストリビューションでインストールされているパッケージを示したものです。

| ディストリビューション | [データベース エンジン](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [ツール](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server エージェント](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [フルテキスト検索](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA アドオン](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![×](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![×](./media/sql-server-linux-virtual-machines-overview/no.png) | ![×](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![○](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>次のステップ

Linux で SQL Server を構成および使用する方法の詳細については、[Linux における SQL Server の概要](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)に関するページを参照してください。
