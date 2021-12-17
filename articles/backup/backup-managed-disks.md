---
title: Azure マネージド ディスクのバックアップ
description: Azure portal から Azure マネージド ディスクをバックアップする方法について学習します。
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 6da98c8229d626edb5f1872ede5380f9cc61aa0b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224382"
---
# <a name="back-up-azure-managed-disks"></a>Azure Managed Disks のバックアップ

この記事では、Azure portal から [Azure マネージド ディスク](../virtual-machines/managed-disks-overview.md)をバックアップする方法について説明します。

この記事では、次の方法について学習します。

- バックアップ コンテナーの作成

- バックアップ ポリシーの作成

- Azure ディスクのバックアップを構成する

- オンデマンド バックアップ ジョブを実行する

Azure ディスク バックアップの使用可能なリージョン、サポートされるシナリオ、制限事項については、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、Azure Database for PostgreSQL サーバーや Azure ディスクなど、Azure Backup によってサポートされるさまざまな新しいワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、強化されたバックアップ データの保護機能を提供します。

1. Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。
1. 検索ボックスに「**バックアップ センター**」と入力します。
1. **[サービス]** で **[バックアップ センター]** を選択します。
1. **[バックアップ センター]** ページで、 **[コンテナー]** を選択します。

   ![バックアップ センターで [コンテナー] を選択する](./media/backup-managed-disks/backup-center.png)

1. **[開始: コンテナーの作成]** 画面で、 **[バックアップ コンテナー]** 、 **[続行]** の順に選択します。

   ![開始:コンテナーの作成](./media/backup-managed-disks/initiate-create-vault.png)

1. **[基本]** タブで、サブスクリプション、リソース グループ、バックアップ コンテナー名、リージョン、バックアップ ストレージの冗長性を設定します。 **[確認と作成]** を選択して続行します。 詳細については、「[バックアップ コンテナーの作成](./backup-vault-overview.md#create-a-backup-vault)」を参照してください。

   ![コンテナーを確認して作成する](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>バックアップ ポリシーを作成する

1. 前の手順で作成した *DemoVault* **バックアップ コンテナー** で、 **[バックアップ ポリシー]** に移動して **[追加]** を選択します。

   ![バックアップ ポリシーを追加する](./media/backup-managed-disks/backup-policies.png)

1. **[基本]** タブで、ポリシー名を入力し、 **[データ ソースの種類]** として **[Azure ディスク]** を選択します。 コンテナーは事前に設定されており、選択したコンテナーのプロパティが表示されます。

   >[!NOTE]
   > 選択したコンテナーにグローバル冗長の設定が含まれる場合がありますが、Azure ディスク バックアップでは現在スナップショット データストアのみがサポートされています。 すべてのバックアップがサブスクリプションのリソース グループに格納され、バックアップ コンテナーのストレージにはコピーされません。

   ![データ ソースの種類を選択する](./media/backup-managed-disks/datasource-type.png)

1. **[バックアップ ポリシー]** タブで、バックアップ スケジュールの頻度を選択します。

   ![バックアップ スケジュールの頻度を選択する](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure ディスク バックアップでは、1 日に複数のバックアップを作成できます。 頻繁にバックアップを行う必要がある場合は、4、6、8、または 12 時間の間隔でバックアップを作成できる **[時間単位]** のバックアップ頻度を選択します。 **[時間]** で選択した間隔に基づいてバックアップがスケジュールされます。 たとえば、 **[4 時間ごと]** を選択した場合、ほぼ 4 時間ごとにバックアップが作成されるので、バックアップが 1 日のうちで均等に分散されます。 1 日 1 回のバックアップで十分な場合は、 **[日単位]** のバックアップ頻度を選択します。 日単位のバックアップ頻度では、バックアップを作成する時刻を指定できます。 この時刻は、バックアップが完了する時刻ではなく、バックアップの開始時刻を示していることに注意してください。 バックアップ操作を完了するために必要な時間は、ディスクのサイズや連続するバックアップ間のチャーン率など、さまざまな要因によって異なります。 ただし、Azure ディスク バックアップは[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md)を使用するエージェントレス バックアップであり、実稼働アプリのパフォーマンスには影響しません。

1. **[バックアップ ポリシー]** タブで、目標復旧時点 (RPO) の要件を満たすリテンション期間の設定を選択します。

   他の保持規則が存在しない場合は、既定の保持規則が適用されます。 既定の保持規則は、そのリテンション期間を変更できますが、削除できません。 新しい保持規則に追加するには、 **[保持規則の追加]** を選択します。

   ![保持規則を追加する](./media/backup-managed-disks/add-retention-rule.png)

   **最初に正常に作成された日単位または週単位のバックアップ** を選択して、特定のバックアップが削除される前に保持されるリテンション期間を指定できます。 このオプションは、日または週の特定のバックアップを長期間保持するのに便利です。 その他のバックアップについては、保持期間を短くすることができます。

   ![保有期間の設定](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >マネージド ディスクの Azure Backup では、ディスクあたりのスナップショット数が 200 に制限された増分スナップショットが使用されます。 スケジュールされたバックアップとは別にオンデマンド バックアップを作成するには、バックアップ ポリシーによって合計バックアップ数を 180 に制限します。 詳細については、マネージド ディスクの[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md#restrictions)に関するページをご覧ください。

1. **[確認と作成]** を選択してバックアップ ポリシーの作成を完了します。

## <a name="configure-backup"></a>バックアップの構成

- Azure ディスク バックアップでは、運用層のバックアップのみがサポートされています。 コンテナー ストレージ層へのバックアップのコピーは現在サポートされていません。 バックアップ コンテナーのストレージ冗長設定 (LRS または GRS) は、運用層に格納されているバックアップには適用されません。 
  増分スナップショットは、選択された親ディスクのストレージの種類に関係なく、Standard HDD ストレージに格納されます。 [ゾーン冗長ストレージ](../storage/common/storage-redundancy.md) (ZRS) をサポートするリージョンでは、信頼性を向上させるため、増分スナップショットが既定で ZRS に保存されます。

- Azure Disk バックアップでは、サブスクリプション間のバックアップをサポートしており、あるサブスクリプションではバックアップ コンテナーで、別のサブスクリプションではソース ディスクで復元がサポートされています。 したがって、リージョンの境界を越えるバックアップと復元はサポートされませんが、バックアップ コンテナーとバックアップ対象ディスクの場所は同じでも異なるサブスクリプションでもかまいません。 ただし、バックアップ コンテナーとバックアップ対象ディスクの両方が、同じリージョン内に存在する必要があります。

- ディスクのバックアップを構成した後に、バックアップ インスタンスに割り当てられているスナップショットのリソース グループを変更することはできません。

ディスクのバックアップを構成するには、次の手順に従います。

1. ディスクのバックアップの構成を開始するには、 **[バックアップ センター]**  ->  **[概要]** に移動し、 **[+ バックアップ]** をクリックします。

   :::image type="content" source="./media/backup-managed-disks/start-configuring-backup-of-disk-inline.png" alt-text="Azure ディスク バックアップを開始するためのオプションを示すスクリーンショット。" lightbox="./media/backup-managed-disks/start-configuring-backup-of-disk-expanded.png":::

1. **[データソースの種類]** ドロップダウン リストで **[Azure ディスク]** を選択し、 **[続行]** をクリックします。

   :::image type="content" source="./media/backup-managed-disks/select-azure-disks-as-datasource-type-inline.png" alt-text="データ保護の種類として Azure ディスクを選択するプロセスを示すスクリーンショット。" lightbox="./media/backup-managed-disks/select-azure-disks-as-datasource-type-expanded.png":::

1. バックアップ コンテナーを選択し、 **[次へ]** をクリックして続行します。

   >[!Note]
   >- バックアップ コンテナーとバックアップ対象ディスクの両方が同じ場所にあることを確認します。
   >- Azure Backup では、マネージド ディスクの "[_増分スナップショット_](../virtual-machines/disks-incremental-snapshots.md#restrictions)" が使用されます。これにより、親ディスクのストレージの種類に関係なく、最後のスナップショット時のディスクの差分変更のみが Standard HDD ストレージに保存されます。 ゾーン冗長ストレージ (ZRS) をサポートするリージョンでは、信頼性を向上させるため、増分スナップショットが既定で ZRS に保存されます。 Azure ディスク バックアップでは現在、バックアップ コンテナー ストレージにバックアップをコピーしないマネージド ディスクの運用バックアップがサポートされています。 そのため、バックアップ コンテナーのバックアップ ストレージの冗長性設定は回復ポイントに適用されません。

   :::image type="content" source="./media/backup-managed-disks/select-backup-vault-inline.png" alt-text="バックアップ コンテナーの選択プロセスを示すスクリーンショット。" lightbox="./media/backup-managed-disks/select-backup-vault-expanded.png":::

1. **[バックアップ ポリシー]** タブで、バックアップ ポリシーを選択します。

   :::image type="content" source="./media/backup-managed-disks/choose-backup-policy-inline.png" alt-text="バックアップ ポリシーの選択プロセスを示すスクリーンショット。" lightbox="./media/backup-managed-disks/choose-backup-policy-expanded.png":::

1. **[データソース]** タブの **[+ Add/Edit]\(+ 追加と編集\)** をクリックして、バックアップの構成対象となる Azure マネージド ディスクを少なくとも 1 つ選択します。

   :::image type="content" source="./media/backup-managed-disks/choose-azure-managed-disks-inline.png" alt-text="Azure マネージド ディスクの選択プロセスを示すスクリーンショット。" lightbox="./media/backup-managed-disks/choose-azure-managed-disks-expanded.png":::

   >[!Note]
   >ポータルでは、複数のディスクを選択してバックアップを構成できますが、各ディスクは個別のバックアップ インスタンスです。 Azure ディスク バックアップでは現在、個々のディスクのバックアップのみがサポートされています。 仮想マシンに接続されている複数のディスクの特定時点のバックアップはサポートされていません。
   >
   >Azure portal で選択できるのは、同じサブスクリプション内のディスクのみです。 バックアップするディスクが複数ある場合、またはディスクが異なるサブスクリプションに存在する場合は、スクリプト ([PowerShell](./backup-managed-disks-ps.md)/[CLI](./backup-managed-disks-cli.md)) を使用して自動化できます。 
   >
   >Azure ディスク バックアップの利用可能なリージョン、サポートされるシナリオ、制限事項の詳細については、[サポート マトリックス](./disk-backup-support-matrix.md)を参照してください。

1. **[Snapshot resource group]\(スナップショット リソース グループ\)** を選択し、 **[検証]** をクリックして前提条件のチェックを開始します。

   スナップショットを保存および管理するためのリソース グループの選択:

   - ソース ディスクと同じリソース グループを選択しないでください。
   
   - ガイドラインとして、Azure Backup サービスで使用されるスナップショット データストアとして専用のリソース グループを作成することをお勧めします。 専用のリソース グループを使用すると、リソース グループに対するアクセス許可を制限できるため、バックアップ データを安全かつ簡単に管理できます。

   - このリソース グループを使用して、バックアップする (またはその予定がある) 複数のディスクにスナップショットを格納できます。

   - 特定のディスクの増分スナップショットを、そのディスクのサブスクリプションの外部で作成することはできません。 そのため、ディスクのバックアップ場所と同じサブスクリプション内のリソース グループを選択してください。 マネージド ディスクの増分スナップショットについて[詳細を確認してください](../virtual-machines/disks-incremental-snapshots.md#restrictions)。

   - ディスクのバックアップを構成した後で、バックアップ インスタンスに割り当てられているスナップショットのリソース グループを変更することはできません。

   - バックアップ操作中、Azure Backup により、スナップショット リソース グループにストレージ アカウントが作成されます。 スナップショット リソース グループごとに作成されるストレージ アカウントは 1 つだけです。 このアカウントは、スナップショット リソース グループと同じリソース グループを使用する複数のディスク バックアップ インスタンス間で再利用されます。

     - このストレージ アカウントにスナップショットは格納されません。 マネージド ディスクの増分スナップショットは、ストレージ アカウントにではなく、リソース グループに作成される ARM リソースです。
     - ストレージ アカウントには、回復ポイントごとのメタデータが格納されます。 Azure Backup サービスによって、ディスクのバックアップ インスタンスごとに BLOB コンテナーが作成されます。 1 つの回復ポイントごとにブロック BLOB が作成されます。これは、(サブスクリプション、ディスク ID、ディスク属性などの) 小さな領域 (数 KiBs) を占める復旧ポイントを記述するメタデータを格納するためのものです。
     - リージョンでゾーン冗長がサポートされている場合、ストレージ アカウントは RA GZRS として作成されます。 リージョンでゾーン冗長がサポートされていない場合、ストレージ アカウントは RA GRS として作成されます。
       既存のポリシーを使用して、サブスクリプションまたはリソース グループの GRS 冗長性を持つストレージ アカウントの作成を停止した場合、ストレージ アカウントは LRS として作成されます。 作成されるストレージ アカウントは **General Purpose v2** で、その BLOB コンテナーのホット層にブロック BLOB が格納されます。
     - 回復ポイントの数は、ディスク バックアップ インスタンスのバックアップを構成するために使用されるバックアップ ポリシーによって決定されます。 古いブロック BLOB は、対応する古い回復ポイントが除去されるときに、ガベージ コレクション プロセスに従って削除されます。

   - Azure Backup サービスによって作成されたストレージ アカウントまたはスナップショット リソース グループにリソース ロックやポリシー、ファイアウォールを適用しないでください。 このサービスは、ディスクのバックアップを構成するときに、バックアップ インスタンスに割り当てられているこのスナップショット リソース グループ内のリソースを作成し、管理します。 ストレージ アカウントとそのリソースは、このサービスによって作成されます。これを削除したり移動したりすることは避けてください。

     >[!Note]
     >ストレージ アカウントが削除されると、バックアップは失敗し、既存のすべての復旧ポイントで復元が失敗します。

   :::image type="content" source="./media/backup-managed-disks/validate-snapshot-resource-group-inline.png" alt-text="前提条件チェックを開始するプロセスを示すスクリーンショット。" lightbox="./media/backup-managed-disks/validate-snapshot-resource-group-expanded.png":::

1. 検証が完了したら、[バックアップの準備] 列にエラーがレポートされていないかを確認します。

   >[!Note]
   >検証は完了するまでに数分かかることがあります。 検証は、次の場合に失敗する可能性があります。
   >
   >- ディスクがサポートされていない。 サポートされないシナリオについては、[サポート マトリックス](./disk-backup-support-matrix.md)を参照してください。
   >- バックアップ コンテナーのマネージド ID に、バックアップする "_ディスク_" または増分スナップショットが格納される "_スナップショット リソース グループ_" に対する有効なロールが割り当てられていない。

   **[バックアップの準備]** 列に "_Role assignment not done (ロールの割り当てが完了していません)_ " というエラー メッセージが表示される場合、バックアップ コンテナーのマネージド ID に、選択したディスクまたはスナップショット リソース グループに対するロールのアクセス許可が必要です。 

   :::image type="content" source="./media/backup-managed-disks/role-assignment-not-done-error-inline.png" alt-text="&quot;Role assignment not done (ロールの割り当てが完了していません)&quot; というエラー メッセージを示すスクリーンショット。" lightbox="./media/backup-managed-disks/role-assignment-not-done-error-expanded.png":::

   マネージド ディスクのバックアップを構成するには、以下の前提条件を満たす必要があります。

   >[!Note]
   >バックアップ コンテナーは、マネージド ID を使用して他の Azure リソースにアクセスします。 マネージド ディスクのバックアップを構成するには、バックアップ コンテナーのマネージド ID に、スナップショットが作成および管理されるソース ディスクとリソース グループに対する一連のアクセス許可を付与する必要があります。

   システム割り当てマネージド ID は、1 つのリソースにつき 1 つに限定されており、このリソースのライフサイクルに関連付けられています。 マネージド ID にアクセス許可を付与するには、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。 マネージド ID は、Azure リソースでのみ使用できる、特殊な種類のサービス プリンシパルです。 [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。

   - バックアップ コンテナーのマネージド ID に、バックアップする必要のあるソース ディスクに対する **ディスク バックアップ リーダー** ロールを割り当てます。
   - バックアップ コンテナーのマネージド ID に、Azure Backup サービスによってバックアップが作成および管理されるリソース グループに対するディスク スナップショット共同作成者ロールを割り当てます。 ディスクのスナップショットは、サブスクリプション内のリソース グループに格納されます。 Azure Backup サービスでスナップショットを作成、保存、管理できるようにするには、バックアップ コンテナーへのアクセス許可を指定する必要があります。

   >[!Note]
   >上記のリソースに必要なロールのアクセス許可を付与するには、Azure portal を使用したバックアップの構成フローが便利です。 

1. [バックアップの準備] 列に "_Role assignment not done (ロールの割り当てが完了していません)_ " というエラー メッセージが表示されている各行の横にあるチェック ボックスをオンにして、 **[Add missing roles]\(不足しているロールの追加\)** をクリックすると、選択したリソースに関してバックアップ コンテナーのマネージド ID に必要なロールのアクセス許可が自動的に付与されます。

   :::image type="content" source="./media/backup-managed-disks/add-missing-roles-inline.png" alt-text="不足しているロールを追加するプロセスを示すスクリーンショット。" lightbox="./media/backup-managed-disks/add-missing-roles-expanded.png":::

1. **[確定]** をクリックして同意します。 ロールの割り当ての変更が、ユーザーに代わって Azure Backup によって自動的に伝達され、再検証が試行されます。

   選択したディスクおよびスナップショット リソース グループに対するアクセス許可をバックアップ コンテナーのマネージド ID に付与したい場合は、 **[スコープ]** ドロップダウン リストで **[リソース]** を選択します。 

   :::image type="content" source="./media/backup-managed-disks/confirm-role-propagation-inline.png" alt-text="ロールの割り当てを Azure Backup で自動的に実行することへの同意を確認するオプションのスクリーンショット。" lightbox="./media/backup-managed-disks/confirm-role-propagation-expanded.png":::

   >[!Tip]
   >将来、同じリソース グループまたはサブスクリプションの他のディスクのバックアップを構成する予定がある場合、アクセス許可を付与するスコープとして、リソース グループまたはサブスクリプションのどちらかを選択できます。

   :::image type="content" source="./media/backup-managed-disks/permission-deployment-in-progress-inline.png" alt-text="アクセス許可のデプロイを示すスクリーンショット。" lightbox="./media/backup-managed-disks/permission-deployment-in-progress-expanded.png":::

   :::image type="content" source="./media/backup-managed-disks/permission-waiting-to-propagate-inline.png" alt-text="リソース グループへの伝達を待つアクセス許可のスクリーンショット。" lightbox="./media/backup-managed-disks/permission-waiting-to-propagate-expanded.png":::

   :::image type="content" source="./media/backup-managed-disks/revalidating-permission-propagation-inline.png" alt-text="伝達されたアクセス許可の再検証を試行する画面のスクリーンショット (失敗した場合のシナリオ)。" lightbox="./media/backup-managed-disks/revalidating-permission-propagation-expanded.png":::

   >[!Note]
   >- ロールの割り当ての伝達には最大 30 分かかり、結果的に再検証エラーになる場合があります。 このような場合は、しばらくしてから再試行してください。
   >- **[Add missing roles]\(不足しているロールの追加\)** アクションで、"Insufficient permission for role assignment (ロールの割り当てに必要なアクセス許可がありません)" というエラーが [バックアップの準備] 列に表示され、アクセス許可の割り当てに失敗した場合、ロールのアクセス許可を割り当てる権限がご自身にないことを意味します。 [Download role assignment template]\(ロール割り当てテンプレートのダウンロード\) を選択して、ロールの割り当てをスクリプトとしてダウンロードし、社内の IT 管理者に相談のうえ、スクリプトを実行して前提条件を満たしてください。 

   :::image type="content" source="./media/backup-managed-disks/permission-propagation-taking-long-time-inline.png" alt-text="アクセス許可の伝達に時間 (最大 30 秒) がかかる例のスクリーンショット。" lightbox="./media/backup-managed-disks/permission-propagation-taking-long-time-expanded.png":::

1. 検証に成功したら、 **[次へ]** をクリックして **[レビューと構成]** タブに移動し、 **[バックアップの構成]** をクリックして、選択したディスクのバックアップを構成します。

   :::image type="content" source="./media/backup-managed-disks/configure-backup-of-selected-disks-inline.png" alt-text="選択したディスクのバックアップを構成するプロセスを示すスクリーンショット。" lightbox="./media/backup-managed-disks/configure-backup-of-selected-disks-expanded.png":::

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

1. 前の手順で作成した *DemoVault* **バックアップ コンテナー** で、 **[バックアップ インスタンス]** に移動してバックアップ インスタンスを選択します。

   ![バックアップ インスタンスを選択する](./media/backup-managed-disks/select-backup-instance.png)

1. **[バックアップ インスタンス]** 画面に、以下が表示されます。

   - ソース ディスク名、増分スナップショットが格納されるスナップショット リソース グループ、バックアップ コンテナー、バックアップ ポリシーなどの **要点**。
   - 過去 7 日間のバックアップおよび復元操作とその状態の概要を示す **ジョブの状態**。
   - 選択された期間の **復元ポイント** の一覧。

1. **[バックアップ]** を選択してオンデマンド バックアップを開始します。

   ![[今すぐバックアップ] の選択](./media/backup-managed-disks/backup-now.png)

1. バックアップ ポリシーに関連付けられている保持規則のいずれかを選択します。 この保持規則によって、このオンデマンド バックアップのリテンション期間が決まります。 **[今すぐバックアップ]** を選択してバックアップを開始します。

   ![バックアップを開始する](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>バックアップ操作を追跡する

スケジュールされたバックアップの実行時、またはオンデマンド バックアップ操作をトリガーすると、追跡用のジョブが Azure Backup サービスによって作成されます。 バックアップ ジョブの状態を表示するには、次の手順に従います。

1. **[バックアップ インスタンス]** 画面に移動します。 ここには、過去 7 日間の操作と状態を含むジョブのダッシュボードが表示されます。

   ![ジョブのダッシュボード](./media/backup-managed-disks/jobs-dashboard.png)

1. バックアップ操作の状態を表示するには、 **[すべて表示]** を選択して、このバックアップ インスタンスの進行中および過去のジョブを表示します。

   ![[すべて表示] を選択する](./media/backup-managed-disks/view-all.png)

1. バックアップおよび復元ジョブとそれらの状態の一覧を確認します。 ジョブの一覧からジョブを選択すると、そのジョブの詳細が表示されます。

   ![詳細を表示するジョブを選択する](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>次の手順

- [Azure マネージド ディスクの復元](restore-managed-disks.md)