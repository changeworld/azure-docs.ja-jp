---
title: Azure SQL Database Managed Instance を構成する方法 | Microsoft Docs
description: Azure SQL Database Managed Instance を構成して管理する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 10a33ac09b5cfa588bef88e6c1587d67036b1aef
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439519"
---
# <a name="how-to-use-managed-instance"></a>Managed Instance を使用する方法

このセクションでは、お客様の Azure SQL データベース (Managed Instance) を管理して構成するのに役立つさまざまなガイド、スクリプト、説明をご紹介します。

## <a name="migration"></a>移行

- [Managed Instance への移行](sql-database-managed-instance-migrate.md) – Managed Instance への移行に関して推奨される移行プロセスとツールについて説明します。

- [Managed Instance への TDE 証明書の移行](sql-database-managed-instance-migrate-tde-certificate.md) – お客様の SQL Server データベースが Transparent Data Encryption (TDE) を使用して保護される場合は、お客様が Azure に格納したいバックアップを解読するために Managed Instance によって使用される証明書を移行する必要があります。

## <a name="network-configuration"></a>ネットワーク構成

- [Managed Instance サブネットのサイズの決定](sql-database-managed-instance-determine-size-vnet-subnet.md) – Managed Instance は、お客様が一度内部にリソースを追加するとサイズ変更できない専用サブネットに配置されます。 そのため、お客様がサブネット内にデプロイしたいインスタンスの数と種類に従って、サブネットに必要となるアドレスの IP 範囲を計算する必要があります。
- [Managed Instance 用の新しい VNet とサブネットの作成](sql-database-managed-instance-create-vnet-subnet.md) – お客様がご自分のマネージド インスタンスのデプロイ先にする Azure VNet およびサブネットは、[こちらで説明されているネットワーク要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)に従って構成しなければなりません。 このガイドでは、マネージド インスタンス用に正しく構成された新しい VNet とサブネットを作成する最も簡単な方法を確認できます。
- [Managed Instance 用の既存の VNet とサブネットの構成](sql-database-managed-instance-configure-vnet-subnet.md) – お客様の既存の VNet とサブネットを構成して内部にマネージド インスタンスをデプロイしたい場合は、[ネットワーク要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)をチェックし、それに従ってお客様のサブネットを構成するためのスクリプトをこちらで確認できます。
- [カスタム DNS の構成](sql-database-managed-instance-custom-dns.md) – お客様のマネージド インスタンスからデータベース メール プロファイルのリンク サーバー経由でカスタム ドメイン上の外部リソースにアクセスしたい場合は、カスタム DNS を構成する必要があります。
- [ネットワーク構成の同期](sql-database-managed-instance-sync-network-configuration.md) - 場合によっては、[ご自分のアプリを Azure 仮想ネットワークと統合した](../app-service/web-sites-integrate-with-vnet.md)にもかかわらず、Managed Instance への接続を確立できないことがあります。 その場合の対処の 1 つとして、使用するサービス プラン用にネットワーク構成を更新するという対処方法があります。
- [管理エンドポイント IP アドレスの検索](sql-database-managed-instance-find-management-endpoint-ip-address.md) – Managed Instance では、管理目的にのみパブリック エンドポイントが使用されます。 こちらで説明されているスクリプトを使用して、管理エンドポイントの IP アドレスを特定することができます。
- [組み込みのファイアウォール保護の確認](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – Managed Instance は、必要なポートに対してのみトラフィックを許可する組み込みのファイアウォールによって保護されます。 このガイドで説明されているスクリプトを使用して、組み込みのファイアウォール規則をチェックして確認できます。
- [アプリケーションの接続](sql-database-managed-instance-connect-app.md) – マネージド インスタンスは、プライベート IP アドレスを備えたお客様独自のプライベート Azure VNet に配置されます。 アプリケーションをお客様のマネージド インスタンスに接続するさまざまなパターンについて説明します。

## <a name="feature-configuration"></a>機能の構成

- [トランザクション レプリケーション](replication-with-sql-database-managed-instance.md)では、マネージド インスタンス間でお客様のデータをレプリケートできるほか、オンプレミスの SQL Server から Managed Instance、またはその逆にお客様のデータをレプリケートできます。 トランザクション レプリケーションの使用方法と構成方法の詳細については、このガイドを参照してください。
- [脅威検出の構成](sql-database-managed-instance-threat-detection.md) – [脅威検出](sql-database-threat-detection-overview.md)は、SQL インジェクションや疑わしい場所からのアクセスなど、さまざまな潜在的な攻撃を検出する組み込みの Azure SQL Database 機能です。 このガイドでは、Managed Instance 用に[脅威検出](sql-database-threat-detection-overview.md)を有効にして構成する方法を学習できます。

## <a name="next-steps"></a>次の手順
- [Single Database の攻略ガイド](sql-database-howto-single-database.md)について詳しく確認する