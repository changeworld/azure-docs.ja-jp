---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールを使用したバックアップ | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールのバックアップ コマンドを実行するためのガイドを提供します。
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
ms.topic: reference
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 5fd588cc9ff36f4213d62ee47ce296e9eadfc40e
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296759"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool"></a>Azure アプリケーション整合性スナップショット ツールを使用してバックアップする

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールのバックアップ コマンドを実行するためのガイドを提供します。

## <a name="introduction"></a>はじめに

ストレージ スナップショット ベースのバックアップは、`azacsnap -c backup` コマンドを使用して実行されます。  このコマンドを使用すると、DATA ボリューム上のデータベース整合性ストレージ スナップショットのオーケストレーションと、OTHER ボリューム上のストレージ スナップショット (データベース整合性の設定なし) が実行されます。  

DATA ボリュームの場合、`azacsnap` によってストレージ スナップショット用のデータベースが準備され、次に構成されたすべてのボリュームのストレージ スナップショットが取得され、最後にスナップショットが完了したことがデータベースに通知されます。  また、スナップショット バックアップ操作を記録するデータベース エントリ (SAP HANA バックアップ カタログなど) もこれによって管理されます。

## <a name="command-options"></a>コマンド オプション

`-c backup` コマンドは次の引数を使用します。

- `--volume=` スナップショットするボリュームの種類。このパラメーターには `data` や `other` を含めることができます
  - `data` 構成ファイルの dataVolume 節内のボリュームのスナップショットを設定します。
  - `other` 構成ファイルの otherVolume 節内のボリュームのスナップショットを設定します。
  
  > [!NOTE]
  > ブート ボリュームを otherVolume として使用して別の構成ファイルを作成することによって、`boot` スナップショットをまったく異なるスケジュール (たとえば毎日) で実行できるようになります。

- `--prefix=` スナップショット名のユーザー スナップショット プレフィックス。 このパラメーターには 2 つの目的があります。 1 つ目の目的は、スナップショットをグループ化するための一意の名前を指定することです。 2 つ目は、指定された `--prefix` に対して保持されるストレージ スナップショットの `--retention` の数を決定します。

    > [!IMPORTANT]
    > アルファベット ("A-Z、a-z、0-9")、アンダースコア ("_")、およびダッシュ ("-") 文字のみを使用できます。

- `--retention` 定義された `--prefix` のスナップショットを保持する数。 この `--prefix` に対して新しいスナップショットが取得された後、追加のスナップショットはすべて削除されます。

- `--trim` SAP HANA v2 以降では、このオプションを使用すると、バックアップ カタログと、ディスク カタログおよびログ バックアップが維持されます。 バックアップ カタログに保持されるエントリの数は上記の `--retention` オプションによって決定され、定義されたプレフィックス (`--prefix`) の古いエントリが、バックアップ カタログ、および関連する物理ログのバックアップから削除されます。 また、ログ バックアップ以外の最古のエントリよりも古いログ バックアップ エントリも削除されます。 この操作は、ログ バックアップによって使用可能なすべてのディスク領域が使い切られてしまう事を防ぐのに役立ちます。

  > [!NOTE]
  > 次のコマンド例では、9 つのストレージ スナップショットを保持し、保持されている 9 個のストレージ スナップショットに一致するようにバックアップ カタログが継続的にトリミングされていることを確認します。

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` SAP HANA との通信に使用する暗号化方法を定義する省略可能なパラメーター (`openssl` または `commoncrypto`)。 定義されている場合、`azacsnap -c backup` コマンドでは、同じディレクトリ内に 2 つのファイルが含まれていることが想定されています。これらのファイルには、対応する SID に基づく名前を付ける必要があります。 「[SAP HANA との通信での SSL の使用](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)」を参照してください。 次の例では、`hana_TEST` というプレフィックスを持つ `hana` 型のスナップショットを取得し、SSL (`openssl`) を使用してそのうちの `9` つを保持して SAP HANA と通信します。

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` は、カスタム構成ファイル名を許可する省略可能なパラメーターです。

## <a name="snapshot-backups-are-fast"></a>高速なスナップショット バックアップ

スナップショット バックアップの実行時間はボリュームのサイズに依存せず、10 TB のボリュームも 10 GB のボリュームとおおよそ同じ時間でスナップされます。  

全体的な実行時間に影響を与える主な要因は、スナップショットされるボリュームの数と `--retention` パラメーターに加えた変更です (減らすとより多くのスナップショットが削除されるため、実行時間が長くなる可能性があります)。

上記の構成例 (**Azure Large Instance**) では、2 つのボリュームのスナップショットは 5 秒未満で完了しました。 **Azure NetApp Files** の場合、2 つのボリュームのスナップショットには約 60 秒かかります。

> [!NOTE]
> `azacsnap` を実行した後に `--retention` を大幅に低くした場合 (たとえば、`--retention 50` から `--retention 5`)、`azacsnap` によって余分なスナップショットが削除されるため、実行時間は長くなります。

## <a name="example-with-data-parameter"></a>`data` パラメーターを使用した例

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

このコマンドはコンソールに出力されませんが、ログ ファイル、結果ファイル、および `/var/log/messages` には書き込まれます。

この例では、"*ログ ファイル*" 名は `azacsnap-backup-azacsnap.log` です (「[ログ ファイル](#log-files)」を参照)

`--volume data` オプションを指定して `-c backup` を実行すると、結果ファイルもファイルとして生成され、バックアップの結果をすばやく確認できるようになります。  "*結果*" ファイルの名前は、ログ ファイルと同じベース名にサフィックス `.result` が付ついたものになります。

この例の "*結果ファイル*" の名前は `azacsnap-backup-azacsnap.result` で、以下の出力が含まれます。

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

`/var/log/messages` ファイルには、`.result` ファイルと同じ出力が含まれています。 次の例を参照してください (ルートとして実行)。

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>`other` パラメーターを使用した例

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

このコマンドはコンソールに出力されませんが、ログ ファイルには書き込まれます。  結果ファイルや `/var/log/messages` へは "_書き込まれません_"。

この例では、"*ログ ファイル*" 名は `azacsnap-backup-azacsnap.log` です (「[ログ ファイル](#log-files)」を参照)。

## <a name="example-with-other-parameter-to-backup-host-os"></a>`other` パラメーターを使用した例 (ホスト OS をバックアップする場合)

> [!NOTE]
> ブート ボリュームのみが含まれる別の構成ファイル (`--configfile bootVol.json`) の使用。

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

> [!IMPORTANT]
> Azure Large Instance の場合、ブート ボリュームの構成ファイル ボリューム パラメーターが、ホスト オペレーティング システム レベルで表示されない可能性があります。
> この値は、Microsoft Operations によって提供されます。

このコマンドはコンソールに出力されませんが、ログ ファイルには書き込まれます。  結果ファイルや `/var/log/messages` へは "_書き込まれません_"。

この例では、"*ログ ファイル*" 名は `azacsnap-backup-bootVol.log` です (「[ログ ファイル](#log-files)」を参照)。

## <a name="log-files"></a>ログ ファイル

ログ ファイル名は、次の "(コマンド名)-(`-c` オプション)-(構成ファイル名)" に基づいて作成されます。  たとえば、コマンド `azacsnap -c backup --configfile h80.json --retention 5 --prefix one-off` を実行すると、ログ ファイルの名前は `azacsnap-backup-h80.log` になります。  また、同じ構成ファイルで `-c test` オプションを使用した場合 (たとえば `azacsnap -c test --configfile h80.json`)、ログ ファイルには `azacsnap-test-h80.log` という名前がつきます。

## <a name="next-steps"></a>次のステップ

- [スナップショットの詳細の取得](azacsnap-cmd-ref-details.md)
- [スナップショットの削除](azacsnap-cmd-ref-delete.md)
