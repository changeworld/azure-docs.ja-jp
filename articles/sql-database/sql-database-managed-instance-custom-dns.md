---
title: Azure SQL Database Managed Instance のカスタム DNS | Microsoft Docs
description: このトピックでは、Azure SQL Database マネージド インスタンスのカスタム DNS の構成オプションについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 07/17/2019
ms.openlocfilehash: 674c5d48dad5d3cfd138853d7ea38ae4a216c93d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309875"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance のカスタム DNS の構成

Azure SQL Database Managed Instance は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 プライベート ホスト名をマネージド インスタンスから解決する必要のあるシナリオ (データベース メール、クラウドまたはハイブリッド環境内の他の SQL インスタンスにリンクされたサーバーなど) がいくつか存在します。 この場合は、Azure の内部でカスタム DNS を構成する必要があります。 

Managed Instance の内部の処理にも同じ DNS が使用されるため、カスタム DNS サーバーは、パブリック ドメイン名を解決できるように構成する必要があります。

   > [!IMPORTANT]
   > プライベート DNS ゾーン内にある場合でも、メール サーバー、SQL サーバー、およびその他のサービスには必ず完全修飾ドメイン名 (FQDN) を使用します。 たとえば、単に `smtp` では正しく解決されないため、メール サーバーには `smtp.contoso.com` を使用します。

   > [!IMPORTANT]
   > 仮想ネットワークの DNS サーバーを更新しても、すぐには Managed Instance に影響しません。 Managed Instance の DNS 構成が更新されるのは、DHCP リースの有効期限が切れた後、またはプラットフォームのアップグレード後のどちらか先に到来した方となります。 **ユーザーには、最初の Managed Instance を作成する前に、仮想ネットワークの DNS 構成を設定しておくことをお勧めします。**

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- マネージド インスタンスの VNet を構成する方法については、[マネージド インスタンスの VNet 構成](sql-database-managed-instance-connectivity-architecture.md)に関するページを参照してください。
