---
title: "Linux Azure Virtual Machines における SQL Server に関する FAQ | Microsoft Docs"
description: "この記事では、Linux Azure VM で SQL Server を実行することに関してよく寄せられる質問に対する回答を示します。"
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Linux Azure Virtual Machines における SQL Server についてよく寄せられる質問

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

このトピックでは、[Linux Azure Virtual Machines における SQL Server](sql-server-linux-virtual-machines-overview.md) に関して特に多く寄せられる質問への回答を示します。

> [!NOTE]
> このトピックでは、Linux VM 上の SQL Server に固有の問題について説明します。 Windows VM で SQL Server を実行している場合は、[Windows での FAQ](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md) に関する記事をご覧ください。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. **SQL Server で Linux Azure 仮想マシンを作成するにはどうするのですか?**

   最も簡単な解決方法は、SQL Server を含む Linux 仮想マシンを作成することです。 Azure にサインアップして、ポータルから SQL VM を作成するチュートリアルについては、「[Azure Portal での Linux SQL Server 仮想マシンのプロビジョニング](provision-sql-server-linux-virtual-machine.md)」をご覧ください。 無料ライセンス版 (Developer または Express) を利用するかオンプレミスのライセンスを再利用して、SQL Server を VM に手動でインストールすることもできます。 現在所有しているライセンスを使用する場合は、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility)が必要です。

1. **Azure VM で SQL Server の新しいバージョン/エディションにアップグレードするにはどうすればよいですか?**

   現在、Azure VM で実行している SQL Server のインプレース アップグレードはありません。 目的の SQL Server バージョン/エディションで新しい Azure 仮想マシンを作成し、[標準のデータ移行方法](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview)を使用して、データベースを新しいサーバーに移行します。

1. **Azure VM に SQL Server のライセンス版をインストールするにはどうすればよいですか?**

   まず、Linux OS 専用の仮想マシンを作成します。 次に、Linux ディストリビューション用の [SQL Server のインストール手順](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms)を実行します。 SQL Server の無料ライセンス版のいずれかをインストールしていない限り、SQL Server ライセンスと [Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)も必要です。

1. **SQL Server のライセンス持ち込み (BYOL) Linux 仮想マシンのイメージはありますか?**

   現時点では、SQL Server 用 BYOL Linux 仮想マシンのイメージはありません。 ただし、前の質問で説明したように、SQL Server を Linux 専用の VM に手動でインストールできます。

1. **従量課金のギャラリー イメージから作成した VM を、現在所有している SQL Server ライセンスを使用するように変更できますか。**

   いいえ。 分単位のライセンスから、所有しているライセンスへの使用に切り替えることはできません。 新しい Linux VM を作成し、SQL Server をインストールし、データを移行する必要があります。 ライセンス持ち込みについて詳しくは、前の質問をご覧ください。

1. **どのような関連 SQL Server パッケージがインストールされますか?**

   SQL Server Linux VM に既定でインストールされる SQL Server パッケージを確認するには、「[インストール済みパッケージ](sql-server-linux-virtual-machines-overview.md#packages)」をご覧ください。

1. **Azure VM で SQL Server 高可用性ソリューションはサポートされますか?**

   現時点ではありません。 Always On 可用性グループとフェールオーバー クラスタリングの両方で、Pacemaker などの Linux のクラスタリング ソリューションが必要です。 SQL Server でサポートされている Linux ディストリビューションは、クラウドの高可用性アドオンをサポートしていません。

1. **RHEL または SLES SQL Server VM を使用制限のある Azure サブスクリプションでプロビジョニングできないのはなぜですか?**

   RHEL および SLES 仮想マシンでは、使用制限のないサブスクリプションと、サブスクリプションに関連付けられている検証済みの支払い方法 (通常はクレジット カード) が必要です。 使用制限を削除せずに RHEL または SLES VM をプロビジョニングした場合、サブスクリプションは無効になり、すべての VM/サービスが停止します。 この状態になった場合、サブスクリプションをもう一度有効にするには、[使用制限を削除](https://account.windowsazure.com/subscriptions)します。 現在の請求サイクルの残りのクレジットは復元されますが、再起動して実行を続けることを選択した場合、クレジット カードに対して RHEL または SLES VM イメージの追加料金が請求されます。

## <a name="resources"></a>リソース

**Linux VM**:

* [Linux VM における SQL Server の概要](sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM のプロビジョニング](provision-sql-server-linux-virtual-machine.md)
* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows VM**:

* [Windows VM における SQL Server の概要](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM のプロビジョニング](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [FAQ (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)