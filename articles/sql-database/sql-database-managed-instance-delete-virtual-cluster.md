---
title: Azure SQL Database Managed Instance の削除後に VNet を削除する | Microsoft Docs
description: Azure SQL Database Managed Instance の削除後に VNet を削除する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: douglas, carlrab, sstein
manager: digimobile
origin.date: 05/07/2019
ms.date: 05/20/2019
ms.openlocfilehash: 95d1681c9ff9981990d873a58a2d01833d690e0f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412401"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance の削除後にサブネットを削除する

この記事では、サブネットに存在する最後の Azure SQL Database Managed Instance を削除した後で、そのサブネットを手動で削除する方法についてのガイドラインを紹介します。

削除したマネージド インスタンスが格納されていた[仮想クラスター](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)は、インスタンスの削除から 12 時間維持されます。 仮想クラスターが維持されるのは、同じサブネットにマネージド インスタンスをすぐに作成できるようにするための意図的な設計です。 この期間中は、仮想クラスターに関連付けられたサブネットを削除できません。

空の仮想クラスターによって使用されているサブネットは、仮想クラスターを手動で削除すれば、直ちに解放することができます。 Azure portal や仮想クラスター API を使用して、仮想クラスターを削除することはできません。

> [!NOTE]
> 仮想クラスターを正常に削除するためには、その仮想クラスターにマネージド インスタンスが存在していないことが必要です。

## <a name="delete-virtual-cluster-from-azure-portal"></a>Azure portal から仮想クラスターを削除する

Azure portal を使用して仮想クラスターを削除するには、ビルトインの検索機能を使用して仮想クラスターのリソースを検索します。

![仮想クラスターを検索する](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

削除する仮想クラスターが見つかったら、そのリソースを選択し、[削除] オプションを選択します。 仮想クラスターを削除してよいか確認するメッセージが表示されます。

![仮想クラスターを削除する](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure portal の通知に、仮想クラスターが削除されたことの確認が表示されます。 仮想クラスターが正常に削除されると、サブネットが直ちに解放されて再利用できる状態になります。

## <a name="delete-virtual-cluster-using-api"></a>API を使用して仮想クラスターを削除する

API を使用して仮想クラスターを削除するには、[仮想クラスターの削除メソッド](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)に関するページに記載されている URI パラメーターを使用します。

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- [Managed Instance の接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)について確認します。
- [Managed Instance の既存の仮想ネットワークを変更する](sql-database-managed-instance-configure-vnet-subnet.md)方法を確認します。
- 仮想ネットワークを作成し、マネージド インスタンスを作成して、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。
