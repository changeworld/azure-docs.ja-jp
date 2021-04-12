---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールとは | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合スナップショット ツールの概要について説明します。
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
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 4ba679459686340396e0e4d65344295c0fa9c4be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869958"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールとは (プレビュー)

Azure アプリケーション整合性スナップショットツール (AzAcSnap) は、サードパーティのデータベースのデータ保護を可能にするコマンドライン ツールです。これを使用すると、ストレージのスナップショットを作成する前にデータベースをアプリケーション整合状態にするために必要なすべてのオーケストレーションを処理することができます。その後、データベースは稼働状態に戻ります。

## <a name="supported-platforms-and-os"></a>サポートされているプラットフォームと OS

- **データベース**
  - SAP HANA (詳細については[サポート マトリックス](azacsnap-get-started.md#snapshot-support-matrix-from-sap)を参照してください)

- **オペレーティング システム**
  - SUSE Linux Enterprise Server 12+
  - Red Hat Enterprise Linux 7+

## <a name="benefits-of-using-azacsnap"></a>AzAcSnap を使用する利点

AzAcSnap では、Azure NetApp Files と Azure Large Instance のボリューム スナップショット機能およびレプリケーション機能が活用されます。  これには、次のようなメリットがあります。

- **アプリケーション整合性データ保護** AzAcSnap は、重要なデータベース ファイルをバックアップするための集中型ソリューションです。 ストレージ ボリューム スナップショットを実行する前に、データベースの整合性が確保されます。 その結果、データベースの復旧にストレージ ボリューム スナップショットを確実に使用できます。
- **データベース カタログ管理** 組み込みのバックアップ カタログを備えたデータベースで AzAcSnap を使用すると、カタログ内のレコードがストレージ スナップショットで最新の状態に維持されます。  この機能により、データベース管理者はバックアップ アクティビティを確認できます。
- **アドホックなボリューム保護** この機能は、ストレージ スナップショットを作成する前にアプリケーションの休止処理を必要としないデータベース以外のボリュームに役立ちます。  たとえば、SAP HANA のログ バックアップ ボリュームや SAPTRANS ボリュームなどです。
- **ストレージボリュームの複製** この機能により、開発とテストのために、領域の効率に優れたストレージ ボリュームの複製が提供されます。
- **ディザスター リカバリーのサポート** AzAcSnap では、ストレージ ボリューム レプリケーションを利用して、リモート サイトにあるレプリケートされたアプリケーション整合性スナップショットを復旧するためのオプションが提供されます。

AzAcSnap は単一のバイナリです。  データベースまたはストレージと対話するために、追加のエージェントまたはプラグインは必要ありません (Azure Resource Manager 経由での Azure NetApp Files、SSH 経由での Azure Large Instance)。  AzAcSnap は、データベースとストレージに接続できるシステムにインストールする必要があります。  ただし、柔軟なインストールと構成により、単一の集中インストール、または各データベース インストールにコピーがインストールされる完全分散インストールのいずれかを実行できます。

## <a name="architecture-overview"></a>アーキテクチャの概要

AzAcSnap は、データベース (SAP HANA) と同じホストにインストールすることも、集中システムにインストールすることもできます。  ただし、データベース サーバーとストレージ バックエンドへのネットワーク接続が必要です (Azure NetApp Files の場合は Azure Resource Manager、Azure Large Instance の場合は SSH)。

AzAcSnap は軽量アプリケーションであり、通常は、外部スケジューラから実行されます。  ほとんどの Linux システムにおいて、この操作は `cron` であり、このドキュメントではこれに焦点を当てています。  ただし、`azacsnap` ユーザーのシェル プロファイルをインポートできる限り、代わりのツールとしてスケジューラを使用できます。  ユーザーの環境設定をインポートすることで、ファイル パスとアクセス許可が正しく初期化されます。

## <a name="command-synopsis"></a>コマンドの概要

コマンドの一般的な形式は次のようになります: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]`。

## <a name="command-options"></a>コマンド オプション

コマンド オプションは次のようになります。メインの箇条書きがコマンドであり、インデントされた箇条書きは関連するサブコマンドです。

- **`-h`** AzAcSnap の使用方法についての、拡張されたコマンド ライン ヘルプと例が提供されます。
- **`-c configure`** このコマンドを使用すると、`azacsnap` の構成ファイル (既定では `azacsnap.json`) を作成または変更するための、Q&A スタイルの対話型インターフェイスが提供されます。
  - **`--configuration new`** 新しい構成ファイルを作成します。
  - **`--configuration edit`** 既存の構成ファイルを編集します。
  - [configure コマンドのリファレンス](azacsnap-cmd-ref-configure.md)に関する記事を参照してください。
- **`-c test`** 構成ファイルの検証と、接続のテストが行われます。
  - **`--test hana`** SAP HANA インスタンスへの接続がテストされます。
  - **`--test storage`** 基になるストレージ インターフェイスとの通信がテストされます。構成されているすべての `data` ボリュームで一時的なストレージ スナップショットが作成された後、それらは削除されます。
  - **`--test all`** `hana` テストと `storage` テストの両方が順番に実行されます。
  - [test コマンドのリファレンス](azacsnap-cmd-ref-test.md)に関する記事を参照してください。
- **`-c backup`** データ (SAP HANA データ ボリューム) ボリュームとその他の (共有、ログ バックアップ、ブートなど) ボリュームに対してデータベース整合性ストレージ スナップショットを実行するための、主要なコマンドです。
  - **`--volume data`** 構成ファイルの `dataVolume` 節にあるすべてのボリュームのスナップショットを取得します。
  - **`--volume other`** 構成ファイルの `otherVolume` 節にあるすべてのボリュームのスナップショットを取得します。
  - [backup コマンドのリファレンス](azacsnap-cmd-ref-backup.md)に関する記事を参照してください。
- **`-c details`** スナップショットまたはレプリケーションに関する情報が提供されます。
  - **`--details snapshots`** 構成されている各ボリュームのスナップショットに関する基本的な詳細の一覧が提供されます。
  - **`--details replication`** 運用サイトからディザスター リカバリー サイトへのレプリケーションの状態に関する基本的な詳細が提供されます。
  - [details コマンドのリファレンス](azacsnap-cmd-ref-details.md)に関する記事を参照してください。
- **`-c delete`** ストレージ スナップショットまたはスナップショットのセットが削除されます。 HANA Studio に表示される SAP HANA バックアップ ID、またはストレージ スナップショット名を使用できます。 バックアップ ID は、データ ボリュームと共有ボリューム用に作成された `hana` スナップショットにのみ関連付けられます。 それに対し、スナップショット名を入力すると、入力したスナップショット名と一致するすべてのスナップショットが検索されます。
  - [delete](azacsnap-cmd-ref-delete.md) に関する記事を参照してください。
- **`-c restore`** ボリュームにスナップショットを復元するために、スナップショットに基づく新しいボリュームの作成と、プレビュー状態へのボリュームのロールバックの 2 つの方法が用意されています。
  - **`--restore snaptovol`** ターゲット ボリュームの最新のスナップショットに基づいて、新しいボリュームを作成します。
  - **`-c restore --restore revertvolume`** 最新のスナップショットに基づいて、ターゲット ボリュームを前の状態に戻します。
  - [restore コマンドのリファレンス](azacsnap-cmd-ref-restore.md)に関する記事を参照してください。
- **`[--configfile <configfilename>]`** 異なる JSON 構成ファイル名を指定するためのコマンド ライン パラメーター (省略可能)。  これは、SID ごとに個別の構成ファイルを作成する場合に特に便利です (`--configfile H80.json` など)。

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション整合性スナップショット ツールの使用を開始する](azacsnap-get-started.md)
