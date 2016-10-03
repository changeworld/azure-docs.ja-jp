<properties
	pageTitle="SQL Server 仮想マシンへの接続 (リソース マネージャー) | Microsoft Azure"
	description="Azure の仮想マシンで実行されている SQL Server に接続する方法について説明します。このトピックでは、クラシック デプロイメント モデルを使用します。シナリオは、ネットワーク構成とクライアントの場所によって異なります。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"    
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/21/2016"
	ms.author="jroth" />

# Azure での SQL Server 仮想マシンへの接続 (リソース マネージャー)

> [AZURE.SELECTOR]
- [リソース マネージャー](virtual-machines-windows-sql-connect.md)
- [クラシック](virtual-machines-windows-classic-sql-connect.md)

## Overview

このトピックでは、Azure 仮想マシンで実行されている SQL Server インスタンスへ接続する方法について説明します。ここでは、[一般的な接続のシナリオ](#connection-scenarios)をいくつか紹介してから、[Azure VM での SQL Server への接続を構成する手順の詳細](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)について説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]
クラシック デプロイメント モデル。この記事のクラシック バージョンについては、[Azure クラシックでの SQL Server 仮想マシンへの接続](virtual-machines-windows-classic-sql-connect.md)に関する記事を参照してください。

プロビジョニングと接続の両方に関する完全なチュートリアルについては、「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。

## 接続のシナリオ

クライアントから仮想マシンで実行されている SQL Server に接続する方法は、クライアントの場所と、マシンやネットワークの構成によって異なります。これらのシナリオは、次のとおりです。

- [インターネット経由で SQL Server に接続する方法](#connect-to-sql-server-over-the-internet)
- [同一仮想ネットワーク内で SQL Server に接続する方法](#connect-to-sql-server-in-the-same-virtual-network)

### インターネット経由で SQL Server に接続する方法

インターネットから SQL Server データベース エンジンに接続する場合は、ファイアウォールの構成、SQL 認証の有効化、ポート 1433 で TCP トラフィックを許可するネットワーク セキュリティ グループ規則の構成など、いくつかの手順が必要です。

ポータルで Resource Manager を使用して SQL Server 仮想マシン イメージをプロビジョニングする場合は、[SQL Connectivity ]\(SQL 接続) オプションで **[パブリック]** を選ぶとこれらの手順は自動的に行われます。

![プロビジョニング中のパブリック SQL 接続オプション](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

または、プロビジョニング時以外であれば、[この記事の手動接続構成手順](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)に従って手動で SQL Server と仮想マシンを構成することもできます。

>[AZURE.NOTE] SQL Server Express エディション用の仮想マシン イメージでは、TCP/IP プロトコルは自動では有効になりません。Express エディションでは、VM の作成後に、SQL Server 構成マネージャーを使用して [TCP/IP プロトコルを手動で有効にする](#configure-sql-server-to-listen-on-the-tcp-protocol)必要があります。

それが済むと、インターネットにアクセスできるクライアントは、仮想マシンのパブリック IP アドレスまたはその IP アドレスに割り当てられている DNS ラベルを指定することによって、SQL Server インスタンスに接続できます。SQL Server のポートが 1433 である場合は、接続文字列でそれを指定する必要はありません。

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

これでクライアントからインターネット経由での接続は有効になりますが、すべてのユーザーが SQL Server に接続できるわけではありません。外部のクライアントは、ユーザー名とパスワードを修正する必要があります。セキュリティを強化するには、既知のポート 1433 を使用しないこともできます。たとえば、ポート 1500 でリッスンするように SQL Server を構成し、適切なファイアウォール規則とネットワーク セキュリティ グループ規則を設定すると、次の例のようにサーバー名にポート番号を追加して接続できます。

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] この手法を使用して SQL Server と通信する場合、Azure データセンターから送信されるすべてのデータには、通常の[送信データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/)が適用されることに注意してください。

### 同一仮想ネットワーク内で SQL Server に接続する方法

[Virtual Network](../virtual-network/virtual-networks-overview.md) では、さらにシナリオが追加されます。同じ仮想ネットワーク内の VM が異なるリソース グループに存在する場合でも、それらの VM に接続できます。また[サイト間 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) を使うと、VM をオンプレミスのネットワークおよびマシンと接続するハイブリッド アーキテクチャを作成できます。

仮想ネットワークを使うと、Azure VM をドメインに参加させることもできます。これは、SQL Server に Windows 認証を使用する唯一の方法です。その他の接続シナリオでは、ユーザー名とパスワードによる SQL 認証が必要です。

Resource Manager を使用してポータルで SQL Server 仮想マシン イメージをプロビジョニングする場合、[SQL の接続] オプションで **[プライベート]** を選択すると、仮想ネットワークでの通信に適したファイアウォール規則が設定されます。または、プロビジョニング時以外であれば、[こちらの記事の接続の手動構成手順](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)に従って手動で SQL Server と仮想マシンを構成することもできます。ただし、ドメイン環境と Windows 認証を構成する場合は、この記事の手順を使用して SQL 認証とログインを構成する必要はありません。また、インターネット経由でのアクセスのためにネットワーク セキュリティ グループの規則を構成する必要もありません。

>[AZURE.NOTE] SQL Server Express エディション用の仮想マシン イメージでは、TCP/IP プロトコルは自動では有効になりません。Express エディションでは、VM の作成後に、SQL Server 構成マネージャーを使用して [TCP/IP プロトコルを手動で有効にする](#configure-sql-server-to-listen-on-the-tcp-protocol)必要があります。

仮想ネットワークで DNS を構成してあると、接続文字列で SQL Server VM コンピューターの名前を指定することによって、SQL Server インスタンスに接続できます。次の例でも、Windows 認証も構成されていることと、ユーザーが SQL Server インスタンスへのアクセスを許可されていることを前提としています。

	"Server=mysqlvm;Integrated Security=true"

このシナリオでは、VM の IP アドレスも指定できます。

## Azure VM で SQL Server への接続を手動構成する手順

次の手順では、SQL Server Management Studio (SSMS) を使用して、SQL Server インスタンスへの接続を手動で設定し、必要に応じてインターネット経由で接続する方法を説明します。次の手順の多くは、ポータルで適切な SQL Server 接続オプションを選択すると自動的に行われることに注意してください。

別の VM またはインターネットから SQL Server インスタンスに接続するには、次のタスクを完了している必要があります。詳細については、この後のセクションで説明します。

- [Windows ファイアウォールで TCP ポートを開く](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [TCP プロトコルでリッスンするように SQL Server を構成する](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [混合モード認証用に SQL Server を構成する](#configure-sql-server-for-mixed-mode-authentication)
- [SQL Server 認証ログインを作成する](#create-sql-server-authentication-logins)
- [ネットワーク セキュリティ グループの受信規則を構成する](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [パブリック IP アドレスの DNS ラベルを構成する](#configure-a-dns-label-for-the-public-ip-address)
- [別のコンピューターからデータベース エンジンに接続する](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [VM 内で SQL Server に接続する](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [VM リソース マネージャーで SQL Server に接続する](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [VM リソース マネージャーで SQL Server に接続する](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## 次のステップ

これらの接続の手順とプロビジョニングの手順を確認するには、「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。

Azure 仮想マシン上の SQL Server の[ラーニング パスを調べます](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)。

Azure VM での SQL Server の実行に関するその他のトピックについては、「[Azure Virtual Machines における SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->