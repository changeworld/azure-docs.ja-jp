---
title: SQL Managed Instance の削除後のサブネットの削除
description: Azure SQL Managed Instance の削除後に Azure 仮想ネットワークを削除する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: d61a3f5a3dac8cfb6215bdfd6ff1c457c4e14150
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214416"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Azure SQL Managed Instance の削除後にサブネットを削除する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、サブネットに存在する最後の Azure SQL Managed Instance を削除した後で、そのサブネットを手動で削除する方法についてのガイドラインを紹介します。

SQL Managed Instance は、[仮想クラスター](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)にデプロイされます。 個々の仮想クラスターは、最初のインスタンスの作成と共にサブネットに関連付けられてデプロイされます。 同様に、最後のインスタンスの削除と共に仮想クラスターが自動的に削除され、サブネットは空になり、いつでも削除できる状態になります。 サブネットを解放するために、仮想クラスターに対して手動で操作を行う必要はありません。 最後の仮想クラスターが削除されたら、サブネットを削除することができます。

まれな状況ではありますが、作成操作に失敗し、空の仮想クラスターがデプロイされてしまうことがあります。 また、インスタンスの作成は[キャンセルできる](management-operations-cancel.md)ため、デプロイされた仮想クラスターにインスタンスがエラー状態で残ってしまう可能性もあります。 このような状況では、仮想クラスターの削除が自動的に開始され、バックグラウンドで削除されます。

> [!NOTE]
> 空の仮想クラスターや作成に失敗したインスタンスが残っていても料金は発生しません。

> [!IMPORTANT]
> - 仮想クラスターを正常に削除するためには、その仮想クラスターに SQL Managed Instance が存在していないことが必要です。 これには、作成に失敗したインスタンスは含まれません。 
> - 仮想クラスターの削除は、約 1.5 時間続く実行時間の長い操作です (最も新しい仮想クラスターの削除時間については、[SQL Managed Instance の管理操作](management-operations-overview.md)に関するページを参照してください)。 このプロセスが完了するまで、仮想クラスターはポータル上に引き続き表示されます。
> - 仮想クラスターに対して実行できる削除操作は 1 回だけです。 それ以降は、削除操作が既に進行中であるため、お客様が開始した削除要求はすべてエラーになります。

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Azure portal から仮想クラスターを削除する

> [!IMPORTANT]
> 2021 年 9 月 1 日以降、 クラスター内の最後のインスタンスが削除されると、すべての仮想クラスターが自動的に削除されます。 以前のように仮想クラスターを手動で削除する必要はなくなりました。

Azure portal を使用して仮想クラスターを削除するには、仮想クラスターのリソースを検索します。

> [!div class="mx-imgBorder"]
> ![検索ボックスを強調表示した Azure portal のスクリーンショット](./media/virtual-cluster-delete/virtual-clusters-search.png)

削除する仮想クラスターが見つかったら、このリソースを選択して **[削除]** を選択します。 仮想クラスターを削除してよいか確認するメッセージが表示されます。

> [!div class="mx-imgBorder"]
> ![削除オプションが強調された、Azure portal の仮想クラスター ダッシュボードのスクリーンショット](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure portal の通知によって、仮想クラスターの削除要求が正常に送信されたことの確認が表示されます。 削除操作自体には約 1.5 時間かかり、その間、仮想クラスターはポータル上に引き続き表示されます。 プロセスが完了すると、仮想クラスターは表示されなくなり、それに関連付けられていたサブネットは解放されて再利用できるようになります。

> [!TIP]
> SQL Managed Instance が仮想クラスターに表示されておらず、仮想クラスターを削除できない場合、継続的なインスタンスのデプロイが進行中でないことを確認してください。 これには、開始後にキャンセルされたがまだ進行中であるデプロイが含まれます。 これは、これらの操作によって仮想クラスターがまだ使用されていて、削除されないようロックされているためです。 インスタンスがデプロイされたリソース グループの **[デプロイ]** タブを確認すると、進行中のデプロイが表示されます。 この場合、デプロイの完了を待って、SQL Managed Instance を削除してください。 仮想クラスターは、インスタンスの削除の一環として同期的に削除されます。

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>API を使用して仮想クラスターを削除する

API を使用して仮想クラスターを削除するには、[仮想クラスターの削除メソッド](/rest/api/sql/virtualclusters/delete)に関するページに記載されている URI パラメーターを使用します。

## <a name="next-steps"></a>次のステップ

- 概要については、「[Azure SQL Managed Instance とは](sql-managed-instance-paas-overview.md)」を参照してください。
- [SQL Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)について確認します。
- [SQL Managed Instance 用に既存の仮想ネットワークを変更する](vnet-existing-add-subnet.md)方法を確認します。
- 仮想ネットワークを作成し、Azure SQL Managed Instance を作成し、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[Azure SQL Managed Instance の作成 (ポータル) ](instance-create-quickstart.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](custom-dns-configure.md)に関するページを参照してください。
