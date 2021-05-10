---
title: 読み取りレプリカを管理する - Azure portal - Azure Database for PostgreSQL - Hyperscale (Citus)
description: Azure portal から Azure Database for PostgreSQL - Hyperscale (Citus) の読み取りレプリカを管理する方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023893"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Azure portal から Azure Database for PostgreSQL - Hyperscale (Citus) の読み取りレプリカを作成および管理する

> [!IMPORTANT]
> Hyperscale (Citus) の読み取りレプリカは、現在プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> その他の新機能については、[Hyperscale (Citus) のプレビュー機能](hyperscale-preview-features.md)に関するページで全一覧をご覧いただけます。

この記事では、Azure Portal から Hyperscale (Citus) の読み取りレプリカを作成および管理する方法について説明します。 読み取りレプリカの詳細については、[概要](concepts-hyperscale-read-replicas.md)を参照してください。


## <a name="prerequisites"></a>前提条件

プライマリにする [Hyperscale (Citus) サーバー グループ](quickstart-create-hyperscale-portal.md)。

## <a name="create-a-read-replica"></a>読み取りレプリカを作成します

読み取りレプリカを作成するには、次の手順に従います。

1. プライマリとして使用する既存の Azure Database for PostgreSQL サーバー グループを選択します。 

2. サーバー グループ サイドバーの **[サーバー グループの管理]** で、 **[レプリケーション]** を選択します。

3. **[レプリカの追加]** を選択します。

4. 読み取りレプリカの名前を入力します。 

5. **[OK]** を選択して、レプリカの作成を確認します。

読み取りレプリカが作成されたら、それを **[レプリケーション]** ウィンドウから表示できます。

> [!IMPORTANT]
>
> [読み取りレプリカの概要に関するページの考慮事項セクション](concepts-hyperscale-read-replicas.md#considerations)を確認してください。
>
> プライマリ サーバー グループの設定が新しい値に更新される前に、レプリカの設定をそれと同等以上の値に更新します。 このアクションは、レプリカがマスターに対するあらゆる変更に追従できるようにするのに役立ちます。

## <a name="delete-a-primary-server-group"></a>プライマリ サーバー グループを削除する

プライマリ サーバー グループを削除するには、スタンドアロンの Hyperscale (Citus) サーバー グループの削除と同じ手順を使用します。 

> [!IMPORTANT]
>
> プライマリ サーバー グループを削除すると、すべての読み取りレプリカへのレプリケーションが停止されます。 読み取りレプリカは、読み取りと書き込みの両方をサポートするようになったスタンドアロン サーバー グループになります。

Azure Portal からサーバー グループを削除するには、次の手順に従います。

1. Azure portal で、ご利用の プライマリ Azure Database for PostgreSQL サーバー グループを選択します。

2. サーバー グループの **[概要]** ページを開きます。 **[削除]** を選択します。
 
3. 削除するプライマリ サーバー グループの名前を入力します。 **[削除]** を選択して、プライマリ サーバー グループの削除を確認します。
 

## <a name="delete-a-replica"></a>レプリカの削除

プライマリ サーバー グループの削除と同様の方法で読み取りレプリカを削除できます。

- Azure Portal で、読み取りレプリカの **[概要]** ページを開きます。 **[削除]** を選択します。
 
次の手順に従って、 **[レプリケーション]** ウィンドウから読み取りレプリカを削除することもできます。

1. Azure portal で、プライマリ Hyperscale (Citus) サーバー グループを選択します。

2. サーバー グループ メニューの **[サーバー グループの管理]** で、 **[レプリケーション]** を選択します。

3. 削除する読み取りレプリカを選択します。
 
4. **[レプリカの削除]** を選択します。
 
5. 削除するレプリカの名前を入力します。 **[削除]** を選択して、レプリカの削除を確認します。

## <a name="next-steps"></a>次のステップ

* [Azure Database for PostgreSQL の読み取りレプリカ - Hyperscale (Citus)](concepts-hyperscale-read-replicas.md) について確認してください。
