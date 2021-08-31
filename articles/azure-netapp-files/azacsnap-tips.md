---
title: Azure NetApp Files で Azure アプリケーション整合性スナップショット ツールを使用するためのヒントとテクニック | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの使い方に関するヒントとテクニックを提供します。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/04/2021
ms.author: phjensen
ms.openlocfilehash: 6650554c92f42a8b5c25a26be5f4ea41947105e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733401"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool"></a>Azure アプリケーション整合性スナップショット ツールを使用するためのヒントとテクニック

この記事では、AzAcSnap を使用する際に役立つヒントとテクニックについて説明します。

## <a name="limit-service-principal-permissions"></a>サービス プリンシパルのアクセス許可を制限する

AzAcSnap サービス プリンシパルのスコープを制限する必要がある場合があります。  Azure リソースのきめ細かなアクセス管理の詳細については、[Azure RBAC のドキュメント](../role-based-access-control/index.yml)を参照してください。  

次に、AzAcSnap を機能させるために必要な最小限のアクションでのロール定義の例を示します。

```azurecli
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

復元オプションが正常に機能するためには、AzAcSnap サービス プリンシパルでもボリュームを作成できる必要があります。  この場合、ロールの定義には追加のアクションが必要になります。そのため、完全なサービス プリンシパルは次の例のようになります。

```azurecli
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>スナップショットを手動で取得する

バックアップ コマンド (`azacsnap -c backup`) を実行する前に、テスト コマンドを実行して構成を確認し、正常に実行されることを確認します。  これらのテストが正しく実行されると、`azacsnap` が、インストールされている SAP HANA データベース、および **Azure Large Instance** または **Azure NetApp Files** システム上の SAP HANA の基になるストレージ システムと通信できることを確認できます。

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

その後、データベース スナップショットの手動バックアップを作成するために、次のコマンドを実行します。

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>スナップショットの自動バックアップを設定する

UNIX または Linux システムでは、`cron` を使用してシステムでのコマンドの実行を自動化するのが一般的です。 スナップショット ツールの標準的な方法では、ユーザーの `crontab` を設定します。

ユーザー `azacsnap` がスナップショットを自動化する `crontab` の例を次に示します。

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

上記の crontab の説明。

- `MAILTO=""`: 空の値を指定することで、crontab エントリを実行するときに、cron によって自動的に電子メールがユーザーに送信される (これは、ローカル ユーザーのメール ファイルに送られる可能性が高い) のを回避できます。
- crontab エントリのタイミングの短縮形は一目瞭然です。
  - `@monthly` = 月に 1 回実行する。つまり、"0 0 1 * *"。
  - `@weekly`  = 週に 1 回実行する。つまり、"0 0 * * 0"。
  - `@daily`   = 1 日に 1 回実行する。つまり、"0 0 * * *"。
  - `@hourly`  = 1 時間に 1 回実行する。つまり、"0 * * * *"。
- 最初の 5 つの列は、時間を指定するために使用されます。下の列の例を参照してください。
  - `0,15,30,45`:15 分ごと
  - `0-23`:1 時間ごと
  - `*` : 毎日
  - `*` : 毎月
  - `*` : 毎日
- かっこ "()"で囲まれた、実行するコマンド ライン
  - `. /home/azacsnap/.profile` = ユーザーの .profile をプルして、$PATH などの環境を設定します。
  - `cd /home/azacsnap/bin` = 構成ファイルがある場所 "/home/azacsnap/bin" に実行ディレクトリを変更します。
  - `azacsnap -c .....` = すべてのオプションを含む、実行する azacsnap コマンド全体。

cron の詳しい説明と crontab ファイルの形式については、こちらを参照してください: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> ユーザーには、cron ジョブを監視して、スナップショットが正常に生成されていることを確認する責任があります。

## <a name="monitor-the-snapshots"></a>スナップショットを監視する

システムの正常性を確保するために、次の状態を監視する必要があります。

1. 使用可能なディスク領域。 古いディスク ブロックがスナップショットに保持されるため、ディスク領域が徐々に消費されます。
    1. ディスク領域の管理を自動化するには、`--retention` および `--trim` の各オプションを使用して、古いスナップショットとデータベース ログ ファイルを自動的にクリーンアップします。
1. スナップショット ツールの正常な実行
    1. `*.result` ファイルで、`azacsnap` の最新の実行が成功したか失敗したかを確認します。
    1. `/var/log/messages` で、`azacsnap` コマンドの出力を確認します。
1. スナップショットの一貫性 (スナップショットを定期的に別のシステムに復元することによって実現)。

> [!NOTE]
> スナップショットの詳細を一覧表示するには、`azacsnap -c details` コマンドを実行します。

## <a name="delete-a-snapshot"></a>スナップショットを削除する

スナップショットを削除する場合、`azacsnap -c delete` のコマンドを使用します。 OS レベルからスナップショットを削除することはできません。 ストレージ スナップショットを削除するには、正しいコマンド (`azacsnap -c delete`) を使用する必要があります。

> [!IMPORTANT]
> スナップショットを削除するときは注意が必要です。 一旦削除されると、削除されたスナップショットは復旧 **できません**。

## <a name="restore-a-snapshot"></a>スナップショットの復元

ストレージ ボリュームのスナップショットは、新しいボリュームに復元できます (`-c restore --restore snaptovol`)。  Azure Large Instance の場合は、ボリュームをスナップショットに戻すことができます (`-c restore --restore revertvolume`)。

> [!NOTE]
> データベースの復旧コマンドは、提供されて **いません**。

スナップショットは SAP HANA データ領域にコピーして戻すことができますが、コピーを作成するときに SAP HANA が実行中であってはなりません (`cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*`)。

Azure Large Instance の場合、サービス要求を開いて Microsoft の運用チームに連絡し、既存の使用可能なスナップショットから目的のスナップショットを復元できます。 サービス要求は、Azure portal (<https://portal.azure.com>) から開くことができます。

ディザスター リカバリー フェールオーバーを実行する場合は、DR サイトで `azacsnap -c restore --restore revertvolume` コマンドを実行すると、自動的に最新の (`/hana/data` と `/hana/logbackups`) ボリューム スナップショットが使用可能になって、SAP HANA の復旧が可能になります。 運用と DR のサイト間のレプリケーションが中断されるため、このコマンドは注意して使用してください。

## <a name="set-up-snapshots-for-boot-volumes-only"></a>"ブート" ボリュームのみのスナップショットを設定する

> [!IMPORTANT]
> この操作は、Azure Large Instance にのみ適用されます。

場合によっては、SAP HANA を保護するためのツールが既にあり、"ブート" ボリューム スナップショットの構成のみが必要であることがあります。  この場合は、次の手順のみを完了します。

1. インストールの前提条件の手順 1から 4 を完了します。
1. ストレージとの通信を有効にします。
1. スナップショット ツールをインストールする、インストーラーをダウンロードして実行します。
1. スナップショット ツールの設定を完了します。
1. azacsnap 構成ファイルに追加するボリュームの一覧を取得します。この例では、Storage User Name は `cl25h50backup` で、Storage IP Address は `10.1.1.10` です。 
    ```bash
    ssh cl25h50backup@10.1.1.10 "volume show -volume *boot*"
    ```
    ```output
    Last login time: 7/20/2021 23:54:03
    Vserver   Volume       Aggregate    State      Type       Size  Available Used%
    --------- ------------ ------------ ---------- ---- ---------- ---------- -----
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v51_vol aggr_n01_ssd online RW 150GB 57.24GB  61%
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v52_vol aggr_n01_ssd online RW 150GB 81.06GB  45%
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v53_vol aggr_n01_ssd online RW 150GB 79.56GB  46%
    3 entries were displayed.
    ```
    > [!NOTE] 
    > この例では、このホストは 3 ノードのスケールアウト システムの一部であり、このホストから 3 つのすべてのブート ボリュームを確認できます。  つまり、3 つのすべてのブート ボリュームのスナップショットをこのホストから作成でき、3 つすべてを次の手順で構成ファイルに追加する必要があります。

1. 次のようにして、新しい構成ファイルを作成します。 ブート ボリュームの詳細は、OtherVolume スタンザに含まれている必要があります。
    ```bash
    azacsnap -c configure --configuration new --configfile BootVolume.json
    ```
    ```output
    Building new config file
    Add comment to config file (blank entry to exit adding comments): Boot only config file.
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: y
    HANA SID (for example, H80): X
    HANA Instance Number (for example, 00): X
    HANA HDB User Store Key (for example, `hdbuserstore List`): X
    HANA Server's Address (hostname or IP address): X
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: y
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v51_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v52_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v53_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. 構成ファイルを確認します。次の例を参照してください。

    `cat` コマンドを使用して、構成ファイルの内容を表示します。

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v51_vol"
                  },
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v52_vol"
                  },
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v53_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. ブート ボリューム バックアップをテストします。

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. それがリストされていることを確認します。`--snapshotfilter` オプションを追加することで、返されるスナップショット リストが制限されることに注意してください。

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    コマンドの出力:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t250_sles_boot_sollabams07v51_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v51_vol, , , Size used by Snapshots, 1.31GB
    #1, t250_sles_boot_sollabams07v52_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v52_vol, , , Size used by Snapshots, 1.31GB
    #1, t250_sles_boot_sollabams07v53_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v53_vol, , , Size used by Snapshots, 1.31GB
    ```

1. *省略可能* `crontab` を使用するか、`azacsnap` バックアップ コマンドを実行する機能がある適切なスケジューラーで、自動スナップショット バックアップを設定します。

> [!NOTE]
> SAP HANA との通信の設定は必要ありません。

## <a name="restore-a-boot-snapshot"></a>"ブート" スナップショットを復元する

> [!IMPORTANT]
> この操作は、Azure Large Instance のみを対象としています。
> サーバーは、スナップショットが作成された時点に復元されます。

"ブート" スナップショットは次のように復旧できます。

1. お客様は、サーバーをシャットダウンする必要があります。
1. サーバーがシャットダウンされたら、復元するマシン ID とスナップショットが含まれたサービス要求を開く必要があります。
    > サービス要求は、Azure portal (<https://portal.azure.com>) から開くことができます。
1. Microsoft では、指定されたマシン ID とスナップショットを使用してオペレーティング システムの LUN を復元してから、サーバーを起動します。
1. その後、お客様側で、サーバーが起動され、正常であることを確認する必要があります。

復元後に実行するその他の手順はありません。

## <a name="key-facts-to-know-about-snapshots"></a>スナップショットについて知っておくべき重要事項

ストレージ ボリューム スナップショットの主な属性:

- **スナップショットの場所**: スナップショットは、ボリューム内の仮想ディレクトリ (`.snapshot`) にあります。  次に示す **Azure Large Instance** の例を参照してください。
  - データベース: `/hana/data/<SID>/mnt00001/.snapshot`
  - 共有: `/hana/shared/<SID>/.snapshot`
  - ログ: `/hana/logbackups/<SID>/.snapshot`
  - ブート: HLI のブートスナップショットは、OS レベルからは **表示できません** が、`azacsnap -c details` を使用して一覧表示できます。

  > [!NOTE]
  >  `.snapshot` は、読み取り専用で非表示の "*仮想*" フォルダーで、スナップショットへの読み取り専用アクセスを提供します。

- **最大スナップショット:** ハードウェアでは、1 ボリュームあたり最大 250 個のスナップショットを維持できます。 スナップショット コマンドでは、コマンド ラインに設定された保有期間に基づいて、プレフィックスに対応する最大数のスナップショットが保持されます。最大保持数を超えた場合は、最も古いスナップショットが削除されます。
- **スナップショット名:** スナップショット名には、お客様によって指定されたプレフィックス ラベルが含まれます。
- **スナップショットのサイズ:** データベース レベルでのサイズと変更によって決まります。
- **ログ ファイルの場所:** コマンドによって生成されたログ ファイルは、JSON 構成ファイルに定義されたフォルダーに出力されます。既定では、コマンドの実行場所にあるサブフォルダー (`./logs` など) です。

## <a name="next-steps"></a>次のステップ

- [[トラブルシューティング]](azacsnap-troubleshoot.md)
