---
title: Azure Database for PostgreSQL の読み取りレプリカを Azure portal で管理する
description: この記事では、Azure portal での Azure Database for PostgreSQL の読み取りレプリカの管理について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 6c1a0a4a13a70daec157ede98f850f87150f8d93
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383865"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Azure portal で読み取りレプリカを作成および管理する方法
この記事では、Azure portal を使用して Azure Database for PostgreSQL サービスの読み取りレプリカを作成および管理する方法を学習します。 読み取りレプリカの詳細については、[概念のドキュメントを読んでください](concepts-read-replicas.md)。

## <a name="prerequisites"></a>前提条件
- マスター サーバーになる [Azure Database for PostgreSQL サーバー ](quickstart-create-server-database-portal.md)。

## <a name="prepare-the-master-server"></a>マスター サーバーの準備
このマスターの準備手順は、汎用サーバーとメモリ最適化サーバーのみに適用されます。

マスター サーバーでは、**azure.replication_support** パラメーターを REPLICA に設定する必要があります。 このパラメーターの変更を有効にするには、サーバーの再起動が必要です。

1. Azure portal で、マスターとして使用する既存の Azure Database for PostgreSQL サーバーを選択します。

2. 左側のメニューから **[サーバー パラメーター]** を選択します。

3. **azure.replication_support** を検索します。

   ![Azure Database for PostgreSQL - azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. **azure.replication_support** を REPLICA に設定します。 変更を**保存**します。

   ![Azure Database for PostgreSQL - REPLICA と保存](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. 保存が完了すると、通知が届きます。

   ![Azure Database for PostgreSQL - 保存の通知](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. 保存後、サーバーを再起動して変更を適用します。 サーバーの再起動方法については、[再起動に関するドキュメント](howto-restart-server-portal.md)を参照してください。

## <a name="create-a-read-replica"></a>読み取りレプリカを作成します
読み取りレプリカは、次の手順を使用して作成できます。
1.  マスターとして使用する既存の Azure Database for PostgreSQL サーバーを選択します。 

2.  [設定] のメニューから [レプリケーション] を選択します。

   汎用マスターまたはメモリ最適化マスターで **azure.replication_support** を REPLICA に設定してサーバーを再起動していない場合は、それを行うように指示するメッセージが表示されます。 作成を続行する前に、それを実行します。

3.  [レプリカの追加] を選択します。

   ![Azure Database for PostgreSQL - レプリカの追加](./media/howto-read-replicas-portal/add-replica.png)

4.  レプリカ サーバーの名前を入力し、[OK] を選択してレプリカの作成を確定します。

   ![Azure Database for PostgreSQL - レプリカの名前指定](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> マスターと同じサーバー構成で、読み取りレプリカが作成されます。 レプリカが作成されたら、マスター サーバーとは関係なく、価格レベル (Basic への変更および Basic からの変更を除く)、コンピューティング世代、仮想コア、ストレージ、バックアップ保持期間を変更できます。

> [!IMPORTANT]
> マスターのサーバー構成を新しい値に更新する前に、レプリカの構成をそれと同等以上の値に更新する必要があります。 そうしないと、実行しようとしたときにエラーが発生します。 これにより、レプリカはマスターに対して行われる変更に対応できます。 


レプリカ サーバーは、作成後、[レプリケーション] ウィンドウから確認できます。

![Azure Database for PostgreSQL - 新しいレプリカ](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>レプリケーションの停止

> [!IMPORTANT]
> サーバーへのレプリケーションの停止は、元に戻すことができません。 マスターとレプリカの間のレプリケーションを停止すると、取り消すことができません。 レプリカ サーバーはスタンドアロン サーバーになり、読み取りと書き込みをサポートするようになります。 このサーバーをもう一度レプリカにすることはできません。

Azure portal からマスターとレプリカの間のレプリケーションを停止するには、次の手順に従います。
1.  Azure portal で、ご利用のマスター Azure Database for PostgreSQL サーバーを選択します。

2.  [設定] のメニューから [レプリケーション] を選択します。

3.  レプリケーションを停止するレプリカ サーバーを選択します。

   ![Azure Database for PostgreSQL - レプリカの選択](./media/howto-read-replicas-portal/select-replica.png)
 
4.  [レプリケーションの停止] を選択します。

   ![Azure Database for PostgreSQL - [レプリケーションの停止] の選択](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  [OK] をクリックして、レプリケーションの停止を確定します。

   ![Azure Database for PostgreSQL - レプリケーションの停止の確定](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>マスターの削除

> [!IMPORTANT]
> マスター サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションが停止されます。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。
マスターを削除する場合は、スタンドアロンの Azure Database for PostgreSQL サーバーの場合と同じ手順に従います。 Azure portal からサーバーを削除するには、以下を実行します。

1.  Azure portal で、ご利用のマスター Azure Database for PostgreSQL サーバーを選択します。

2.  [概要] で [削除] を選択します。

   ![Azure Database for PostgreSQL - サーバーの削除](./media/howto-read-replicas-portal/delete-server.png)
 
3.  マスター サーバーの名前を入力し、[削除] を選択してマスター サーバーの削除を確定します。

   ![Azure Database for PostgreSQL - 削除の確定](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>レプリカの削除
読み取りレプリカを削除するには、上のマスター サーバーの削除と同じ手順に従います。 まず、レプリカの [概要] ページを開き、[削除] を選択します。

   ![Azure Database for PostgreSQL - レプリカの削除](./media/howto-read-replicas-portal/delete-replica.png)
 
または、[レプリケーション] ウィンドウから削除することもできます。
1.  Azure portal で、ご利用のマスター Azure Database for PostgreSQL サーバーを選択します。

2.  [設定] のメニューから [レプリケーション] を選択します。

3.  削除するレプリカ サーバーを選択します。 

   ![Azure Database for PostgreSQL - レプリカの選択](./media/howto-read-replicas-portal/select-replica.png)
 
4.  [レプリカの削除] を選択します。

   ![Azure Database for PostgreSQL - [レプリカの削除] の選択](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  レプリカの名前を入力し、[削除] を選択してレプリカの削除を確定します。

   ![Azure Database for PostgreSQL - レプリカの削除の確定](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>レプリカの監視
### <a name="max-lag-across-replicas"></a>Max Lag Across Replicas (レプリカ間の最大ラグ)
**[Max lag across replicas]\(レプリカ間の最大ラグ\)** メトリックは、マスターと最も遅れているレプリカの間の時間差を示します。 

1.  Azure portal で、**マスター** Azure Database for PostgreSQL サーバーを選択します。

2.  [メトリック] を選択します。 メトリック ウィンドウで、**[Max Lag Across Replicas]\(レプリカ間の最大ラグ\)** を選択します。

    ![Azure Database for PostgreSQL - レプリカ間の最大ラグの監視](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  集計として **[Max]\(最大\)** を選択します。 

### <a name="replica-lag"></a>Replica Lag (レプリカ ラグ)
**[Replica Lag]\(レプリカ ラグ\)** メトリックは、このレプリカで最後に再生されたトランザクションからの経過時間を示します。 マスターでトランザクションが発生していない場合、メトリックにはこのタイム ラグが反映されます。

1.  Azure portal で、**レプリカ**の Azure Database for PostgreSQL サーバーを選択します。

2.  [メトリック] を選択します。 メトリック ウィンドウで、**[Replica Lag]\(レプリカ ラグ\)** を選択します。

   ![Azure Database for PostgreSQL - レプリカ ラグの監視](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  集計として **[Max]\(最大\)** を選択します。 
 
## <a name="next-steps"></a>次の手順
- [Azure Database for PostgreSQL の読み取りレプリカ](concepts-read-replicas.md)について確認してください。