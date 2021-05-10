---
title: Azure Migrate での評価のためにサーバーをグループ化する | Microsoft Docs
description: Azure Migrate サービスで評価を実行する前にサーバーをグループ化する方法について説明します。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780856"
---
# <a name="create-a-group-for-assessment"></a>評価用のグループを作成する

この記事では、評価用のサーバーのグループを Azure Migrate: Discovery and Assessment で作成する方法について説明します。

[Azure Migrate](migrate-services-overview.md) は、Azure への移行に役立ちます。 Azure Migrate には、オンプレミス インフラストラクチャ、アプリケーション、およびデータの検出、評価、および Azure への移行を追跡するための一元的なハブが用意されています。 このハブには、評価および移行のための Azure ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。 

## <a name="grouping-servers"></a>サーバーのグループ化

サーバーをグループにまとめて、Azure への移行に適しているかどうかを評価し、それらの Azure のサイズ設定とコスト見積もりを取得できるようにします。 グループを作成するにはいくつかの方法があります。

- 一緒に移行する必要があるサーバーがわかっている場合は、手動で Azure Migrate でグループを作成できます。
- 一緒にグループ化する必要があるサーバーがよくわからない場合は、Azure Migrate の依存関係視覚化機能を使用してグループを作成できます。 

> [!NOTE]
> 依存関係可視化機能は、Azure Government では使用できません。

## <a name="create-a-group-manually"></a>グループを手動で作成する

[評価の作成](how-to-create-assessment.md)と同時にグループを作成することができます。

評価の作成以外で手動でグループを作成する場合は、次の手順を実行します。

1. Azure Migrate プロジェクトの **[概要]** で、 **[サーバーの評価と移行]** をクリックします。 **[Azure Migrate: Discovery and Assessment]** で、 **[グループ]** をクリックします
    - まだ Azure Migrate: Discovery and Assessment ツールを追加していない場合は、クリックして追加します。 [詳細については、こちらを参照してください](how-to-assess.md)。
    - Azure Migrate プロジェクトをまだ作成していない場合は、[詳細を参照してください](./create-manage-projects.md)。

    ![グループを選択する](./media/how-to-create-a-group/select-groups.png)

2. **[グループ]** アイコンをクリックします。
3. **[グループの作成]** でグループ名を指定し、 **[アプライアンス名]** でサーバーの検出に使用している Azure Migrate アプライアンスを選択します。
4. サーバー一覧から、グループに追加するサーバー > **[作成]** の順に選択します。

    ![グループを作成する](./media/how-to-create-a-group/create-group.png)

これで、[Azure VM の評価](how-to-create-assessment.md)、[Azure VMware Solution (AVS) の評価](how-to-create-azure-vmware-solution-assessment.md)、[Azure SQL の評価](how-to-create-azure-sql-assessment.md) を作成するときに、このグループを使用できます。

## <a name="refine-a-group-with-dependency-mapping"></a>依存関係マッピングを使用してグループを絞り込む

依存関係マッピングを使用すると、サーバー全体の依存関係を視覚化できます。 一般的に依存関係マッピングは、より高い信頼度でサーバー グループを評価したいときに使用します。
- 評価を実行する前に、サーバーの依存関係をクロスチェックする場合に役立ちます。 
- また、何も残さず、移行中の予期しない停止を回避するように Azure への移行を効果的に計画する場合にも役立ちます。
- 同時に移行する必要のある、相互依存しているシステムを検出し、稼働中のシステムがまだユーザーにサービス提供しているのか、あるいは移行せず使用中止する対象となるのかを特定することができます。

[依存関係マッピング](how-to-create-group-machine-dependencies.md)を既に設定していて、既存のグループを絞り込む場合は、次の手順を実行します。

1. **[サーバー]** タブの **[Azure Migrate: Discovery and Assessment]** タイルで、 **[グループ]** をクリックします。
2. 絞り込むグループをクリックします。
    - 依存関係マッピングをまだ設定していない場合は、 **[依存関係]** 列に **[要インストール]** 状態が表示されます。 依存関係を視覚化するサーバーごとに、 **[要インストール]** をクリックします。 サーバーの依存関係をマップする前に、各サーバーに 2 つのエージェントをインストールします。 [詳細については、こちらを参照してください](how-to-create-group-machine-dependencies.md)。

        ![依存関係マッピングを追加する](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 依存関係マッピングを既に設定している場合は、グループ ページで **[依存関係の表示]** をクリックし、グループの依存関係マップを開きます。

3. **[依存関係の表示]** をクリックすると、グループの依存関係マップには以下が表示されます。

    - 依存関係エージェントがインストールされているグループ内のすべてのサーバー間でやり取りされる受信 (クライアント) および送信 (サーバー) TCP 接続。
    - 依存関係エージェントがインストールされていない依存するサーバーは、ポート番号でグループ化されます。
    - 依存関係エージェントがインストールされている依存するサーバーは、別のボックスとして表示されます。
    - サーバー内で実行されているプロセス。 各サーバーのボックスを展開すると、プロセスが表示されます。
    - サーバーのプロパティ (FQDN、オペレーティング システム、MAC アドレスなど)。 各サーバーのボックスをクリックすると、詳細が表示されます。

4. 選択した期間の依存関係を表示するには、開始日と終了日、または期間を指定して時間の範囲 (既定では 1 時間) を変更します。

    > [!NOTE]
    > 時間の範囲は最大 1 時間です。 それより長い範囲が必要な場合は、長い期間に対して [Azure Monitor を使って依存データのクエリを実行](how-to-create-group-machine-dependencies.md)します。

5. グループに追加する、またはグループから削除する依存関係を特定したら、グループを変更できます。 Ctrl キーを押しながらクリックして、グループのサーバーの追加または削除を行います。

    - 追加できるのは、検出済みのサーバーだけです。
    - サーバーを追加または削除すると、グループの過去の評価は無効になります。
    - 必要に応じて、グループを変更したときに新しいアセスメントを作成することもできます。


## <a name="next-steps"></a>次のステップ

[依存関係マッピング](how-to-create-group-machine-dependencies.md)を設定し、使用して、信頼度の高いグループを作成する方法を確認します。