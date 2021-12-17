---
title: データ永続化を構成する - Premium Azure Cache for Redis
description: Premium レベルの Azure Cache for Redis インスタンス用にデータの永続化を構成して管理する方法について説明します
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/13/2021
ms.openlocfilehash: 6a8bb7c24b7d2713286141cbd160f2ff400f0a1b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252922"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>Premium Azure Cache for Redis インスタンスのデータ永続化の構成

[Redis 永続化](https://redis.io/topics/persistence)を使用すると、Redis に格納されたデータを保持できます。 また、スナップショットを取得したりデータをバックアップしたりすることもできます。 ハードウェア障害が発生した場合は、データを読み込みます。 データを永続化する機能は、すべてのデータがメモリに格納される Basic レベルまたは Standard レベルよりも大きなメリットになります。 キャッシュ ノードがダウンしているときに障害が発生した場合、データ損失が発生する可能性があります。

Azure Cache for Redis は、Redis データベース (RDB) と追加専用ファイル (AOF) を使用して Redis の永続化を提供します。

- **RDB 永続化** - RDB 永続化を使用する場合、Azure Cache for Redis によってキャッシュのスナップショットがバイナリ形式で保持されます。 スナップショットは Azure Storage アカウントに保存されます。 構成可能なバックアップ頻度によって、スナップショットを永続化する頻度が決まります。 プライマリとレプリカの両方のキャッシュが無効になるような致命的なイベントが発生した場合、最新のスナップショットを使用してキャッシュが再構築されます。 RDB 永続化の[長所](https://redis.io/topics/persistence#rdb-advantages)と[短所](https://redis.io/topics/persistence#rdb-disadvantages)について、詳細をご確認ください。
- **AOF の永続化** - AOF の永続化を使用する場合、Azure Cache for Redis がすべての書き込み操作をログに保存します。 ログは、少なくとも 1 秒に 1 回 Azure Storage に保存されます。 プライマリとレプリカの両方のキャッシュが無効になるような致命的なイベントが発生した場合、保存されている書き込み操作を使用してキャッシュが再構築されます。 AOF 永続化の[長所](https://redis.io/topics/persistence#aof-advantages)と[短所](https://redis.io/topics/persistence#aof-disadvantages)について、詳細をご確認ください。

永続化では、自分が所有して管理している Azure Storage アカウントに Redis データが書き込まれます。 キャッシュの作成中に左側で **新しい Azure Cache for Redis** を構成します。 既存の Premium キャッシュの場合は、 **[リソース] メニュー** を使用します。

> [!NOTE]
>
> Azure Storage では、データは永続化されるときに自動的に暗号化されます。 暗号化には独自のキーを使用できます。 詳細については、「[Azure Key Vault でのカスタマー マネージド キー](../storage/common/storage-service-encryption.md)」を参照してください。
>
>

## <a name="set-up-data-persistence"></a>データ永続化の設定

1. Premium キャッシュを作成するには、[Azure portal](https://portal.azure.com) にサインインし、 **[リソースの作成]** を選択します。 キャッシュは Azure portal で作成できます。 Resource Manager テンプレート、PowerShell、または Azure CLI を使用してキャッシュを作成することもできます。 Azure Cache for Redis の作成について詳しくは、[キャッシュの作成](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)に関するページを参照してください。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="リソースの作成。":::
  
2. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="[Azure Cache for Redis] を選択します。":::

3. **[新規 Redis Cache]** ページで、新しい Premium キャッシュの設定を構成します。
  
   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 |
   | **サブスクリプション** | ドロップダウンでご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 |
   | **リソース グループ** | ドロップダウンでリソース グループを選択するか、 **[新規作成]** を選択して新しいリソース グループ名を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 |
   | **場所** | ドロップダウンで場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **キャッシュの種類** | ドロップダウンで Premium キャッシュを選択し、Premium 機能を構成します。 詳細については、「[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)」を参照してください。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |

4. **[ネットワーク]** タブを選択するか、ページの下部にある **[ネットワーク]** ボタンを選択します。

5. **[ネットワーク]** タブで、接続方法を選択します。 Premium キャッシュ インスタンスの場合、パブリック IP アドレスまたはサービス エンドポイント経由で公的に接続することができます。 プライベートに接続するには、プライベート エンドポイントを使用します。

6. **[次へ: 詳細]** タブを選択するか、ページの下部にある **[次へ: 詳細]** ボタンを選択します。

7. Premium キャッシュ インスタンスの **[詳細]** タブで、非 TLS ポート、クラスタリング、データ永続化の設定を構成します。 データの永続化の場合、**RDB** または **AOF** 永続化を選択できます。

8. RDB 永続化を有効にするには、 **[RDB]** を選択し、設定を構成します。
  
   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **バックアップ頻度** | ドロップダウンからバックアップ間隔を選択します。 選択肢は、**15 分**、**30 分**、**60 分**、**6 時間**、**12 時間**、**24 時間** です。 | 前のバックアップ操作が正常に完了すると、この間隔のカウントダウンが開始されます。 この期間が経過すると、新しいバックアップが開始されます。 |
   | **ストレージ アカウント** | ドロップダウンで、お使いのストレージ アカウントを選択します。 | キャッシュと同じリージョンおよびサブスクリプション内にあるストレージ アカウントを選択してください。 スループットが高い **Premium Storage** アカウントの使用をお勧めします。 また、ストレージ アカウントで論理的な削除機能を使用すると、ストレージ コストが増加する可能性があります。 詳細については、「[価格と課金](../storage/blobs/soft-delete-blob-overview.md)」を参照してください。 |
   | **ストレージ キー** | ドロップダウンで、使用する **[主キー]** または **[セカンダリ キー]** を選択します。 | 永続化アカウントのストレージ キーを再生成した場合、 **[ストレージ キー]** ドロップダウンからキーを再構成する必要があります。 |

    バックアップ間隔が経過すると、最初のバックアップが開始されます。
  
   > [!NOTE]
   > RDB ファイルがストレージにバックアップされると、ページ BLOB の形式で格納されます。
  
9. AOF の永続化を有効にするには、 **[AOF]** を選択し、設定を構成します。

   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **最初のストレージ アカウント** | ドロップダウンで、お使いのストレージ アカウントを選択します。 | キャッシュと同じリージョンおよびサブスクリプション内にあるストレージ アカウントを選択してください。 スループットが高い **Premium Storage** アカウントの使用をお勧めします。 また、ストレージ アカウントで論理的な削除機能を使用すると、ストレージ コストが増加する可能性があります。 詳細については、「[価格と課金](../storage/blobs/soft-delete-blob-overview.md)」を参照してください。 |
   | **最初のストレージ キー** | ドロップダウンで、使用する **[主キー]** または **[セカンダリ キー]** を選択します。 | 永続化アカウントのストレージ キーを再生成した場合、 **[ストレージ キー]** ドロップダウンからキーを再構成する必要があります。 |
   | **2 つ目のストレージ アカウント** | (省略可能) ドロップダウンで、お使いの 2 つ目のストレージ アカウントを選択します。 | 必要に応じて、別のストレージ アカウントを構成できます。 2 つ目のストレージ アカウントが構成されていると、レプリカ キャッシュへの書き込みはこの 2 つ目のストレージ アカウントに書き込まれます。 |
   | **2 つ目のストレージ キー** | (省略可能) ドロップダウンで、使用する **[主キー]** または **[セカンダリ キー]** を選択します。 | 永続化アカウントのストレージ キーを再生成した場合、 **[ストレージ キー]** ドロップダウンからキーを再構成する必要があります。 |

    AOF 永続化が有効になっていると、キャッシュへの書き込み操作は指定したストレージ アカウント (2 つ目のストレージ アカウントを構成している場合は 2 つのストレージ アカウント) に保存されます。 プライマリとレプリカの両方のキャッシュが削除されるような致命的なエラーが発生した場合は、保存されている AOF ログを使用してキャッシュが再構築されます。

10. ページの下部にある **[次へ: タグ]** タブを選択するか、ページの下部にある **[次へ: タグ]** ボタンを選択します。

11. 必要に応じて、 **[タグ]** タブで、リソースを分類する場合は名前と値を入力します。

12. **[Review + create]\(レビュー + 作成\)** を選択します。 [確認および作成] タブが表示され、Azure によって構成が検証されます。

13. 緑色の検証に成功のメッセージが表示された後、 **[作成]** を選択します。

キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。

## <a name="persistence-faq"></a>永続化の FAQ

次の一覧は、Azure Cache for Redis の永続化に関するよく寄せられる質問への回答です。

- [以前に作成したキャッシュで永続化を有効にできますか](#can-i-enable-persistence-on-a-previously-created-cache)
- [AOF 永続化と RDB 永続化を同時に有効にすることはできますか](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
- [どちらの永続化モデルを選択すべきですか](#which-persistence-model-should-i-choose)
- [別のサイズにスケーリングしていて、スケーリング操作の前に作成したバックアップを復元したらどうなりますか](#what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
- [2 つの異なるキャッシュ間で永続化するために同じストレージ アカウントを使用することはできますか。](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
- [データ永続化で使用されているストレージに対して課金されますか。](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)
- [RDB および AOF 永続化の BLOB への書き込みはどのくらいの頻度で行われますか。また、論理的な削除を有効にする必要はありますか](#how-frequently-does-rdb-and-aof-persistence-write-to-my-blobs-and-should-i-enable-soft-delete)

### <a name="rdb-persistence"></a>RDB 永続化

- [キャッシュの作成後に RDB バックアップ頻度を変更できますか](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
- [RDB バックアップ頻度を 60 分に設定しているときに、バックアップの間隔が 60 分より長くなるのはなぜですか](#why-is-there-more-than-60-minutes-between-backups-when-i-have-an-rdb-backup-frequency-of-60-minutes)
- [新しいバックアップが作成されると、古い RDB バックアップはどうなりますか](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF 永続化

- [どのような場合に 2 つ目のストレージ アカウントを使用すべきですか](#when-should-i-use-a-second-storage-account)
- [AOF 永続化はキャッシュのスループット、待ち時間、またはパフォーマンスに影響しますか](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
- [2 つ目のストレージ アカウントを削除するには、どうすればよいですか](#how-can-i-remove-the-second-storage-account)
- [再書き込みとは何ですか。キャッシュにどのように影響しますか](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
- [AOF が有効になっているキャッシュをスケーリングする場合に、どのようなことを想定すべきですか](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
- [AOF データはストレージ内でどのように整理されますか](#how-is-my-aof-data-organized-in-storage)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>以前に作成したキャッシュで永続化を有効にできますか

はい、Redis の永続化はキャッシュの作成時と、既存の Premium キャッシュの両方に構成できます。

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>AOF 永続化と RDB 永続化を同時に有効にすることはできますか

いいえ、RDB または AOF を有効にすることはできますが、両方を同時に有効にすることはできません。

### <a name="which-persistence-model-should-i-choose"></a>どちらの永続化モデルを選択すべきですか

AOF 永続化では、すべての書き込みがログに保存されます。これはスループットに大きな影響を与えます。 AOF と比較して RDB の永続化では、構成されたバックアップ間隔に基づいてバックアップを保存し、パフォーマンスへの影響を最小限に抑えます。 主な目的がデータ損失の最小化で、キャッシュでのスループットの低下に対処できるという場合は、AOF 永続化を選択してください。 キャッシュでのスループットを最適に維持したいものの、データ復旧メカニズムも必要という場合は、RDB 永続化を選択してください。

- RDB 永続化の[長所](https://redis.io/topics/persistence#rdb-advantages)と[短所](https://redis.io/topics/persistence#rdb-disadvantages)について、詳細をご確認ください。
- AOF 永続化の[長所](https://redis.io/topics/persistence#aof-advantages)と[短所](https://redis.io/topics/persistence#aof-disadvantages)について、詳細をご確認ください。

AOF 永続化を使用しているときのパフォーマンスの詳細については、「[AOF 永続化はキャッシュのスループット、待ち時間、またはパフォーマンスに影響しますか](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)」を参照してください。

### <a name="what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>別のサイズにスケーリングしていて、スケーリング操作の前に作成したバックアップを復元したらどうなりますか

RDB 永続化の場合も AOF 永続化の場合も、以下のように処理されます。

- より大きなサイズにスケーリングした場合、影響はありません。
- 小さいサイズにスケーリングした場合、新しいサイズの[データベースの制限](cache-configure.md#databases)より大きなカスタムの[データベース](cache-configure.md#databases)設定が存在すると、そのデータベースのデータは復元されません。 詳細については、「[スケーリング中に影響を受けるカスタム データベース](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)」を参照してください
- 小さいサイズにスケーリングしていて、最新のバックアップからのデータをすべて保持するにはサイズが小さいためスペースが足りない場合、キーは復元プロセス中に削除されます。  通常は [allkeys-lru](https://redis.io/topics/lru-cache) 削除ポリシーを使用して、キーが削除されます。

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>2 つの異なるキャッシュ間で永続化するために同じストレージ アカウントを使用することはできますか。

はい、2 つの異なるキャッシュ間で永続化するために同じストレージ アカウントを使用できます。

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>データ永続化で使用されているストレージに対して課金されますか。

はい。使用されているストレージ アカウントの価格モデルに従って、使用されているストレージに対して課金されます。

### <a name="how-frequently-does-rdb-and-aof-persistence-write-to-my-blobs-and-should-i-enable-soft-delete"></a>RDB および AOF 永続化の BLOB への書き込みはどのくらいの頻度で行われますか。また、論理的な削除を有効にする必要はありますか。

論理的な削除は推奨されません。 RDB および AOF 永続化では、1 時間ごと、数分ごと、または 1 秒ごとに BLOB に書き込むことができます。 また、ストレージ アカウントで論理的な削除を有効にすると、Azure Cache for Redis では、古いバックアップ データを削除することでストレージ コストを最小限に抑えることができなくなります。 論理的な削除では、通常のデータ サイズのキャッシュと、毎秒の書き込み操作によって、すぐにコストが高くなる可能性があります。 論理的な削除のコストの詳細については、「[価格と課金](../storage/blobs/soft-delete-blob-overview.md)」を参照してください。

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>キャッシュの作成後に RDB バックアップ頻度を変更できますか

はい、左側の **[データ永続化]** で RDB 永続化のバックアップ頻度を変更できます。 手順については、「Redis の永続化を構成する」をご覧ください。

### <a name="why-is-there-more-than-60-minutes-between-backups-when-i-have-an-rdb-backup-frequency-of-60-minutes"></a>RDB バックアップ頻度を 60 分に設定しているときに、バックアップの間隔が 60 分より長くなるのはなぜですか

RDB 永続化のバックアップ頻度の間隔は、その前のバックアップ プロセスが正常に完了するまでは開始しません。 バックアップ間隔を 60 分に設定し、バックアップ プロセスが完了するのに 15 分かかる場合、次のバックアップは、前回のバックアップの開始時刻から 75 分経過するまで開始しません。

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>新しいバックアップが作成されると、古い RDB バックアップはどうなりますか

最新のものを除くすべての RDB 永続化のバックアップは自動的に削除されます。 この削除はすぐに行われないことがありますが、古いバックアップは無期限には保持されません。 ストレージ アカウントで論理的な削除が有効になっている場合、論理的な削除の設定が適用され、既存のバックアップは引き続き論理的な削除状態になります。

### <a name="when-should-i-use-a-second-storage-account"></a>どのような場合に 2 つ目のストレージ アカウントを使用すべきですか

AOF 永続化用の 2 つ目のストレージ アカウントは、キャッシュに対するセット操作の数が予想より多いと感じた場合に使用してください。  2 つ目のストレージ アカウントを設定することにより、キャッシュがストレージの帯域幅制限に達することを防止できます。

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>AOF 永続化はキャッシュのスループット、待ち時間、またはパフォーマンスに影響しますか

キャッシュによる負荷が最大負荷を下回っている (CPU およびサーバー負荷が両方とも 90% 未満である) 場合、AOF 永続化のスループットへの影響は約 15 ～ 20% となります。 キャッシュがこれらの制限内にある場合、待ち時間の問題は発生しません。 ただし、AOF が有効になっていると、キャッシュがより早くこれらの制限に達するようになります。

### <a name="how-can-i-remove-the-second-storage-account"></a>2 つ目のストレージ アカウントを削除するには、どうすればよいですか

AOF 永続化の 2 つ目のストレージ アカウントは、その 2 つ目のストレージ アカウントを最初のストレージ アカウントと同じように設定にすることで削除できます。 既存のキャッシュでは、左側の **[データ永続化]** には、ご利用のキャッシュの **[リソース]** メニューからアクセスします。 AOF の永続化を無効にするには、 **[無効]** を選択します。

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>再書き込みとは何ですか。キャッシュにどのように影響しますか

AOF ファイルが十分な大きさになると、キャッシュに対する再書き込みが自動的にキューに登録されます。 再書き込みにより、現在のデータ セットを作成するために必要な最小の操作セットで AOF ファイルのサイズ変更が行われます。 再書き込み中はパフォーマンス制限に早く達するということを想定しておいてください (特に大型のデータセットを処理する場合)。 再書き込みの発生頻度は、AOF ファイルが大きくなるにつれて低下しますが、発生時にはかなりの時間を要します。

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>AOF が有効になっているキャッシュをスケーリングする場合に、どのようなことを想定すべきですか

スケーリング時の AOF ファイルが大きい場合は、スケーリングの完了後にファイルが再度読み込まれるため、スケール操作に予想よりも長い時間がかかることを想定しておいてください。

スケーリングの詳細については、「[別のサイズにスケーリングしていて、スケーリング操作の前に作成したバックアップを復元したらどうなりますか](#what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)」を参照してください。

### <a name="how-is-my-aof-data-organized-in-storage"></a>AOF データはストレージ内でどのように整理されますか

AOF ファイルに格納されたデータは、ストレージへのデータの保存パフォーマンスを向上させるため、ノードごとに複数のページ BLOB に分割されます。 次の表は、各価格レベルで使用されるページ BLOB の数を示しています。

| Premium レベル | BLOB |
|--------------|-------|
| P1           | シャードあたり 4    |
| P2           | シャードあたり 8    |
| P3           | シャードあたり 16   |
| P4           | シャードあたり 20   |

クラスタリングが有効になっている場合は、前の表に示されているように、キャッシュ内の各シャードにそれぞれのページ BLOB のセットが含まれます。 たとえば、3 つのシャードがある P2 キャッシュでは、AOF ファイルが 24 個のページ BLOB (シャードあたり 8 BLOB で 3 シャード) に振り分けられています。

再書き込み後、ストレージ内には 2 セットの AOF ファイルが存在します。 再書き込みはバックグラウンドで実行され、最初のファイルのセットに追加されます。 再書き込み時にキャッシュに送信されるセット操作は、2番目のセットに追加されます。 障害が発生した場合、バックアップは再書き込み中に一時的に保存されます。 再書き込みが完了すると、バックアップは即座に削除されます。 ストレージ アカウントで論理的な削除が有効になっている場合、論理的な削除設定が適用され、既存のバックアップは引き続き論理的な削除状態になります。

## <a name="next-steps"></a>次のステップ

Azure Cache for Redis の機能について

- [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)