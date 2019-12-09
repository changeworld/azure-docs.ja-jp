---
title: Recovery Services コンテナーの診断設定を使用する
description: Azure Backup の新旧の診断イベントの使用方法を説明する記事
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 875adb82aeeb56b378a84ca01e716c7189abc64f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280993"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Recovery Services コンテナーの診断設定を使用する

Azure Backup を使用して、分析、アラート、およびレポートの目的で収集して使用できる診断イベントを送信できます。 

Azure portal を使用して Recovery Services コンテナーの診断設定を構成できます。それには、コンテナーに移動し、 **[診断設定]** メニュー項目をクリックします。 **[+ 診断設定の追加]** をクリックすることで、1 つ以上の診断イベントをストレージ アカウント、イベン トハブ、または Log Analytics (LA) ワークスペースに送信できます。

![[診断設定] ブレード](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure Backup ユーザーが利用できる診断イベント

Azure Backup には次の診断イベントが用意されており、それぞれがバックアップ関連成果物の特定のセットに関する詳細なデータを提供します。
* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage 

[Azure Backup 診断イベントのデータ モデル](https://aka.ms/diagnosticsdatamodel)

これらのイベントのデータを、ストレージ アカウント、LA ワークスペース、またはイベント ハブに送信できます。 このデータを LA ワークスペースに送信する場合は、 **[診断設定]** 画面の **[リソース固有]** トグルを選択する必要があります (以下のセクションで詳細を参照してください)。

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Log Analytics (LA) での診断設定の使用

Azure Log Analytics のロードマップに合わせて、Azure Backup を使用して、コンテナーの診断データを Backup 専用の LA テーブルに送信できるようになりました。 これらは、[リソース固有テーブル](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)と呼ばれます。

コンテナーの診断データを LA に送信するには:
1.  お使いのコンテナーに移動し、 **[診断設定]** をクリックします。 **[+ 診断設定の追加]** をクリックします。
2.  診断設定に名前を付けます。
3.  **[Log Analytics に送信]** チェックボックスをオンにし、Log Analytics ワークスペースを選択します。
4.  トグルの **[リソース固有]** を選択し、次の 6 つのイベントをオンにします。**CoreAzureBackup**、**AddonAzureBackupAlerts**、**AddonAzureBackupProtectedInstance**、**AddonAzureBackupJobs**、**AddonAzureBackupPolicy**、および **AddonAzureBackupStorage**。
5.  **[Save]** をクリックします。

![リソース固有モード](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

データが LA ワークスペースに送信されると、これらのイベントごとに専用のテーブルがワークスペース内に作成されます。 これらのテーブルのどれに対しても、直接クエリを実行できます。さらに、必要に応じて、これらのテーブル間で JOIN または UNION を実行できます。

> [!IMPORTANT]
> 上の 6 つのイベント (CoreAzureBackup、AddonAzureBackupAlerts、AddonAzureBackupProtectedInstance、AddonAzureBackupJobs、AddonAzureBackupPolicy、および AddonAzureBackupStorage) は、リソース固有モードで**のみ**サポートされます。 **これら 6 つのイベントのデータを Azure Diagnostics モードで送信しようとした場合、データはまったく LA ワークスペースに送信されないことに注意してください。**

## <a name="legacy-event"></a>レガシ イベント

従来、コンテナーのバックアップ関連の診断データは、すべてが "AzureBackupReport" という名前の 1 つのイベントに含まれていました。 上に示した 6 つのイベントは、実質的には、AzureBackupReport に含まれるすべてのデータを分解したものです。 

現時点では、ユーザーがこのイベントに対して既存のカスタム クエリ (カスタム ログ アラートやカスタム視覚化など) を持っている場合の下位互換性を保つために、AzureBackupReport イベントは引き続きサポートされています。ただし、コンテナーに関するすべての新しい診断設定用の新しいイベントを設定することをお勧めします。これにより、ログ クエリでのデータの操作がはるかに容易になり、スキーマとそれらの構造が検出しやすくなり、取り込み遅延とクエリ時間の両方でパフォーマンスが向上します。 Azure Diagnostics モードの使用に対するサポートは、ゆくゆくは段階的に廃止されるため、新しいイベントを選択しておくと、後日の複雑な移行を回避するのに役立つ可能性があります。

新しいテーブルのデータを使用するようにすべてのカスタム クエリが移行されるまで、AzureBackupReport と 6 つの新しいイベントに対して個別の診断設定を作成することを選択できます。 次の図に、2 つの診断設定を持つコンテナーの例を示します。 最初の **Setting1** という名前の設定では、AzureBackupReport イベントのデータが、Azure Diagnostics モードで LA ワークスペースに送信されます。 2 つ目の **Setting2** という名前の設定では、6 つの新しい Azure Backup イベントのデータが、リソース固有モードで LA ワークスペースに送信されます。

![2 つの設定](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport イベントは、Azure Diagnostics モードで**のみ**サポートされています。 **このイベントのデータを リソース固有モードで送信しようとした場合、データはまったく LA ワークスペースに送信されないことに注意してください。**

> [!NOTE]
> [Azure Diagnostics]/[リソース固有] のトグルは、 **[Log Analytics に送信]** がオンの場合にのみ表示されます。 ストレージ アカウントまたはイベント ハブにデータを送信するには、必要な宛先を選択し、目的のイベントをオンにするだけです。追加の入力はありません。 ここでも、今後は、レガシ イベントである AzureBackupReport を選択しないことをお勧めします。

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Log Analytics (LA) に Azure Site Recovery イベントを送信するユーザー

Azure Backup イベントと Azure Site Recovery イベントは、同じ Recovery Services コンテナーから送信されます。 Azure Site Recovery は現時点ではリソース固有テーブルにオンボードされていないため、Azure Site Recovery イベントを LA に送信したいユーザーは、Azure Diagnostics モード**のみ**を使用するように指示されます (下図を参照)。 **Azure Site Recovery イベントに対してリソース固有モードを選択すると、必要なデータが LA ワークスペースに送信されなくなります**。

![Site Recovery イベント](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

上記の差異をまとめると、次のようになります。

* Azure Diagnostics で既に LA 診断が設定されていることに加え、カスタム クエリが記述されている場合は、新しいイベントのデータを使用するようにクエリを移行するまで、設定を**そのまま**にしておきます。
* 新しいテーブルにオンボードする (推奨されています) 場合は、**新しい**診断設定を作成し、 **[リソース固有]** を選択し、上で指定した 6 つの新しいイベントを選択します。
* 現在 Azure Site Recovery イベントを LA に送信し ている場合は、これらのイベントに対して [リソース固有] モードを選択**しない**でください。選択すると、これらのイベントのデータは、LA ワークスペースに送信されなくなります。 代わりに、**追加の診断設定**を作成し、 **[Azure Diagnostics]** を選択し、関連する Azure Site Recovery イベントを選択します。

次の図に、1 つのコンテナーに対して 3 つの診断設定を行っているユーザーの例を示します。 最初の **Setting1** という名前の設定では、AzureBackupReport イベントのデータが、Azure Diagnostics モードで LA ワークスペースに送信されます。 2番目の **Setting2** という名前の設定では、6 つの新しい Azure Backup イベントのデータが、リソース固有モードで LA ワークスペースに送信されます。 3 つ目の **Setting3** という名前の設定では、Azure Site Recovery イベントのデータが、Azure Diagnostics モードで LA ワークスペースに送信されます。

![3 つの設定](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>次の手順

[診断イベント用の Log Analytics データ モデルを確認する](https://aka.ms/diagnosticsdatamodel)
