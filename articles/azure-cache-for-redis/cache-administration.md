---
title: Azure Cache for Redis を管理する方法
description: Azure Cache for Redis の管理タスク (再起動、更新のスケジュールなど) を実行する方法について説明します
author: curib
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: cauribeg
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4532b13389e85be2b506bdc433cf3dc650fdb38
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538878"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Azure Cache for Redis を管理する方法

この記事では、Azure Cache for Redis インスタンスについて、[再起動](#reboot)、[更新スケジュール](#schedule-updates)などの管理タスクを実行する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>再起動します

左側の **[再起動]** では、キャッシュの 1 つ以上のノードを再起動できます。 この再起動機能により、アプリケーションにキャッシュ ノードの障害が発生した場合の回復性をテストすることができます。

:::image type="content" source="media/cache-administration/cache-administration-reboot-2.png" alt-text="[再起動] メニュー オプションが強調表示されているスクリーンショット":::

再起動するノードを選び、 **[再起動]** を選択します。

:::image type="content" source="media/cache-administration/redis-cache-reboot-2.png" alt-text="再起動できるノードを示すスクリーンショット":::

クラスタリングが有効になっている Premium キャッシュがある場合は、再起動するキャッシュのシャードを選択できます。

:::image type="content" source="media/cache-administration/redis-cache-reboot-cluster-2.png" alt-text="シャードのオプションのスクリーンショット":::

キャッシュの 1 つ以上のノードを再起動するには、ノードを選択し、 **[再起動]** を選択します。 クラスタリングが有効になっている Premium キャッシュがある場合は、再起動するシャードを選択し、 **[再起動]** を選択します。 数分後、選択したノードが再起動され、さらに数分後にオンラインに戻ります。

クライアント アプリケーションへの影響は、再起動するノードによって異なります。

* **マスター** - プライマリ ノードが再起動されると、Azure Cache for Redis からレプリカ ノードへのフェールオーバーが行われ、そのノードがプライマリに昇格します。 このフェールオーバー中、キャッシュに接続できない短いインターバルが発生する可能性があります。
* **レプリカ** - レプリカ ノードが再起動されても、通常は、キャッシュ クライアントに影響を与えることはありません。
* **プライマリとレプリカの両方** - 両方のキャッシュ ノードが再起動されると、キャッシュ内のすべてのデータが失われ、プライマリ ノードがオンラインに戻るまでキャッシュに接続できません。 [データの永続化](cache-how-to-premium-persistence.md)を構成した場合は、キャッシュがオンラインに戻ったときに、最新のバックアップが復元されます。 ただし、最新のバックアップの後に生じたキャッシュの書き込みはすべて失われます。
* **クラスタリングが有効になっている Premium キャッシュのノード** - クラスタリングが有効になっている Premium キャッシュのノードを 1 つ以上再起動したとき、選んだノードの動作は、非クラスター化キャッシュの対応するノードを再起動する場合と同じです。

## <a name="reboot-faq"></a>再起動に関する FAQ

* [アプリケーションのテストでは、どのノードを再起動する必要がありますか。](#which-node-should-i-reboot-to-test-my-application)
* [キャッシュを再起動することでクライアント接続を消去できますか。](#can-i-reboot-the-cache-to-clear-client-connections)
* [再起動すると、キャッシュのデータは失われますか。](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [PowerShell、CLI、またはその他の管理ツールを使用して、キャッシュを再起動できますか。](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>アプリケーションのテストでは、どのノードを再起動する必要がありますか。

キャッシュのプライマリ ノードの障害に対するアプリケーションの回復性をテストするには、 **マスター** ノードを再起動します。 レプリカ ノードの障害に対するアプリケーションの回復性をテストするには、**レプリカ** ノードを再起動します。 キャッシュの全体的な障害に対するアプリケーションの回復性をテストするには、 **両方** のノードを再起動します。

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>キャッシュを再起動することでクライアント接続を消去できますか。

はい。キャッシュを再起動すると、すべてのクライアント接続がクリアされます。 再起動は、ロジック エラーやクライアント アプリケーションのバグによって、各クライアント接続がすべて使用されているときに便利です。 価格レベルごとに、さまざまなサイズに対するさまざまな[クライアント接続の制限](cache-configure.md#default-redis-server-configuration)があり、こうした制限に達すると、それ以上のクライアント接続は受け入れられなくなります。 キャッシュを再起動することで、すべてのクライアント接続がクリアされます。

> [!IMPORTANT]
> キャッシュを再起動してクライアント接続をクリアすると、Redis ノードがオンラインに戻ったときに、StackExchange.Redis が自動的に再接続されます。 根本的な問題が解決されない限り、クライアント接続はその後も消耗される可能性があります。
>
>

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>再起動すると、キャッシュのデータは失われますか。

**マスター** と **レプリカ** のノードの両方を再起動すると、キャッシュ (または、クラスタリングが有効になっている Premium キャッシュを使用している場合はそのシャード) のすべてのデータが失われることがあります。 ただし、データが失われない可能性もあります。 [データの永続化](cache-how-to-premium-persistence.md)を構成した場合は、キャッシュがオンラインに戻ったときに、最新のバックアップが復元されます。 ただし、そのバックアップの作成後に発生したキャッシュ書き込みは失われます。

ノードのいずれかを 1 つだけ再起動しても、通常、データが失われることはありませんが、失われる可能性もあります。 たとえば、プライマリ ノードが再起動されたときに、キャッシュの書き込みが実行中だと、そのキャッシュの書き込みのデータは失われます。 また、一方のノードを再起動した場合に、もう一方のノードが偶然同じタイミングで故障しダウンした場合もやはりデータが失われます。 データが失われるさまざまな原因について詳しくは、「[Redis のデータが正常ではない](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)」をご覧ください。

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>PowerShell、CLI、またはその他の管理ツールを使用して、キャッシュを再起動できますか。

PowerShell での手順については、「[To reboot an Azure Cache for Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis)」(Azure Cache for Redis を再起動するには) をご覧ください。

## <a name="schedule-updates"></a>更新のスケジュール

左側の **[更新のスケジュール設定]** では、キャッシュ インスタンスのメンテナンス期間を選択できます。 メンテナンス期間を使用すると、キャッシュをホストしている VM を更新できる曜日と時間を制御できます。 Azure Cache for Redis では、定義された時間枠の中で Redis サーバー ソフトウェアの更新を開始して完了するための最大限の努力が行われます。

> [!NOTE]
> メンテナンス期間は Redis サーバーの更新と、キャッシュをホストしている VM のオペレーティング システムの更新に適用されます。 メンテナンス期間は、キャッシュ VM やその他の Azure ネットワーク コンポーネントをホストしているホストのホスト OS 更新には適用されません。 滅多にありませんが、以前のモデルでキャッシュがホストされている場合 (キャッシュが以前のモデル上にあるかどうかは、キャッシュの DNS 名が "cloudapp.net"、"chinacloudapp.cn"、"usgovcloudapi.net" または "cloudapi.de" の接頭辞で解決されるかどうかで判断できます)、メンテナンス期間はゲスト OS 更新にも適用されません。
>
> 現時点では、Enterprise レベルのキャッシュの再起動やスケジュールされた更新を構成するするためのオプションはありません。
>

:::image type="content" source="media/cache-administration/redis-schedule-updates-2.png" alt-text="更新のスケジュール設定を示すスクリーンショット":::

メンテナンス期間を指定するには、目的の曜日をオンにし、曜日ごとにメンテナンス期間の開始時刻を指定します。 **[OK]** をクリックします。 メンテナンス時間の時刻は UTC 時間です。

更新の既定の最小メンテナンス時間は 5 時間です。 この値は、Azure portal からは構成できませんが、PowerShell で [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) コマンドレットの `MaintenanceWindow` パラメーターを使用して構成できます。 詳しくは、「[PowerShell、CLI、またはその他の管理ツールを使用して、スケジュールされている更新を管理できますか](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)」をご覧ください。

## <a name="schedule-updates-faq"></a>更新のスケジュールに関する FAQ

* [更新スケジュール機能を使用しない場合、更新はどのタイミングで実行されますか。](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [スケジュールされたメンテナンス時間に行われるのは、どのような更新ですか。](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [PowerShell、CLI、またはその他の管理ツールを使用して、スケジュールされている更新を管理できますか。](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)
* ["スケジュールされた更新" 機能の対象として管理されている更新を、[スケジュールされた更新] ウィンドウの外部で実行できますか。](#can-an-update-that-is-covered-and-managed-by-the-scheduled-updates-feature-happen-outside-the-scheduled-updates-window)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>更新スケジュール機能を使用しない場合、更新はどのタイミングで実行されますか。

メンテナンス時間を指定しない場合は、いつでも更新を実行できます。

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>スケジュールされたメンテナンス時間に行われるのは、どのような更新ですか。

スケジュールされたメンテナンス時間に行われるのは、Redis サーバーの更新だけです。 メンテナンス期間は、Azure の更新や、VM のオペレーティング システムへの更新には適用されません。

### <a name="can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>PowerShell、CLI、またはその他の管理ツールを使用して、スケジュールされている更新を管理できますか。

はい、次の PowerShell コマンドレットを使用して、スケジュールされている更新を管理できます。

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

### <a name="can-an-update-that-is-covered-and-managed-by-the-scheduled-updates-feature-happen-outside-the-scheduled-updates-window"></a>スケジュールされた更新機能の対象として管理されている更新を、[スケジュールされた更新] ウィンドウの外部で実行できますか。

はい。 通常、更新は、構成済みの [スケジュールされた更新] ウィンドウの外部では適用されません。 まれに発生する重要なセキュリティ更新は、セキュリティ ポリシーの一環として、パッチ適用スケジュール外で適用できます。

## <a name="next-steps"></a>次のステップ

Azure Cache for Redis の機能について

* [Azure Cache for Redis サービス レベル](cache-overview.md#service-tiers)
