---
title: マネージド インスタンスのカスタム DNS
description: このトピックでは、Azure SQL Database マネージド インスタンスのカスタム DNS の構成オプションについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 1b27d316114b84434a84fc8226eba9328a803dc8
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774377"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance のカスタム DNS の構成

Azure SQL Database Managed Instance は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 プライベート ホスト名をマネージド インスタンスから解決する必要のあるシナリオ (データベース メール、クラウドまたはハイブリッド環境内の他の SQL インスタンスにリンクされたサーバーなど) がいくつか存在します。 この場合は、Azure の内部でカスタム DNS を構成する必要があります。 

Managed Instance の内部の処理にも同じ DNS が使用されるため、パブリック ドメイン名を解決できるようにカスタム DNS サーバーを構成します。

> [!IMPORTANT]
> プライベート DNS ゾーン内にある場合でも、メール サーバー、SQL Server インスタンス、およびその他のサービスには、常に完全修飾ドメイン名 (FQDN) を使用します。 たとえば、`smtp` は正しく解決されないため、メール サーバーには `smtp.contoso.com` を使用します。 同じ仮想ネットワーク内の SQL VM を参照するリンク サーバーまたはレプリケーションを作成する場合も、FQDN と既定の DNS サフィックスが必要です。 たとえば、「 `SQLVM.internal.cloudapp.net` 」のように入力します。 詳細については、「[独自の DNS サーバーを使用する名前解決](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)」を参照してください。

> [!IMPORTANT]
> 仮想ネットワークの DNS サーバーを更新しても、すぐには Managed Instance に影響しません。 Managed Instance の DNS 構成が更新されるのは、DHCP リースの有効期限が切れた後、またはプラットフォームのアップグレード後のどちらか先に発生したときになります。 **ユーザーには、最初の Managed Instance を作成する前に、仮想ネットワークの DNS 構成を設定しておくことをお勧めします。**

## <a name="next-steps"></a>次のステップ

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- マネージド インスタンスの VNet を構成する方法については、[マネージド インスタンスの VNet 構成](sql-database-managed-instance-connectivity-architecture.md)に関するページを参照してください。
