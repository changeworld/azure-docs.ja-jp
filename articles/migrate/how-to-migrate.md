---
title: Azure Migrate に移行ツールを追加する
description: Azure Migrate ハブ内で移行ツールを追加する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810242"
---
# <a name="add-migration-tools"></a>移行ツールの追加

この記事では、[Azure Migrate](migrate-overview.md) 内で移行ツールを追加する方法について説明します。

Azure Migrate には、評価と Azure への移行のためのツールのハブが用意されています。 これには、ネイティブなツール、他の Azure サービスによって提供されるツール、およびサードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが含まれます。

移行ツールの追加を希望し、まだ Azure Migrate プロジェクトを設定していない場合は、この[記事](how-to-add-tool-first-time.md)に従ってください。



## <a name="selecting-an-isv-tool"></a>ISV ツールの選択

移行のために [ISV ツール](migrate-services-overview.md#isv-integration)を選択する場合は、ISV のポリシーに従って、ライセンスを取得するか、または無料試用版にサインアップすることから開始できます。 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールをデプロイし、ツールの手順やドキュメントに従って、ツール ワークスペースを Azure Migrate に接続します。 

## <a name="select-a-migration-scenario"></a>移行シナリオの選択

1. Azure Migrate プロジェクトで、 **[概要]** をクリックします。
2. 使用する移行シナリオを選択します。

    - Azure にマシンやワークロードを移行するには、 **[サーバーの評価と移行]** を選択します。
    - オンプレミスの SQL マシンを移行するには、 **[データベースの評価と移行]** を選択します。
    - オンプレミスの Web アプリを移行するには、 **[Web アプリの評価と移行]** を選択します。
    - オンプレミスの大量のデータを Azure にオフライン モードで移行するには、 **[Data Box の注文]** を選択します。

    ![評価シナリオ](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>サーバー移行ツールの選択

1. **[サーバーの評価と移行]** をクリックします。
2. **[Azure Migrate - Servers]\(Azure Migrate - サーバー\)** で、移行ツールをまだ追加していない場合は、 **[移行ツール]** の下の **[Click here to add a migration tool]\(移行ツールを追加するにはここをクリックしてください\)** を選択します。 移行ツールを既に追加している場合は、 **[移行ツールをさらに追加しますか]** で **[変更]** を選択します。

    > [!NOTE]
    > 別のプロジェクトに移動する必要がある場合は、 **[Azure Migrate - Servers]\(Azure Migrate - サーバー\)** の **[別の移行プロジェクトの詳細を確認しますか]** の横にある **[ここをクリック]** をクリックします。

3. **[Azure Migrate]** で、使用する移行ツールを選択します。
    - Azure Migrate Server Migration を使用する場合は、Azure Migrate プロジェクト内で直接、移行を設定して実行できます。
    - サードパーティ評価ツールを使用する場合は、ISV 用に用意されているリンクに移動し、提供されている指示に従って移行を実行します。

## <a name="select-a-database-migration-tool"></a>データベース移行ツールの選択

1. **[データベースの評価と移行]** をクリックします
2. **[データベース]** で、 **[ツールの追加]** をクリックします。
3. [ツールの追加] > **[移行ツールの選択]** で、データベースの移行に使用するツールを選択します。

## <a name="select-a-web-app-migration-tool"></a>Web アプリ移行ツールの選択

1. **[Web アプリの評価と移行]** をクリックします。
2. Azure App Service の移行ツールへのリンクに従います。 次の目的で移行ツールを使用します。

    - **オンラインでアプリを評価する**:Azure App Service Migration Assistant を使用して、オンラインでパブリック URL を使用してアプリを評価し、移行することができます。
    - **.NET/PHP**:内部 .NET および PHP アプリの場合は、Migration Assistant をダウンロードして実行できます。

## <a name="order-an-azure-data-box"></a>Azure Data Box の注文

大量のデータを Azure に移行するには、オフライン データ転送用に Azure DAta Box を注文できます。

1. **[Data Box の注文]** をクリックします。
2. **[Azure Data Box の選択]** で、ご自身のサブスクリプションを指定します。 
3. 転送は、Azure へのインポートになります。 データ ソースと、データの Azure リージョンの宛先を指定します。

## <a name="next-steps"></a>次の手順

Azure Migrate Server Migration を使用して、[Hyper-V](tutorial-migrate-hyper-v.md) または [VMware](tutorial-migrate-vmware.md) VM の移行を試します。
