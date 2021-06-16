---
title: BareMetal インフラストラクチャでの Oracle の SnapCenter 統合
description: Oracle と NetApp のスナップショット テクノロジを使用して、BareMetal インフラストラクチャ上に Oracle データベースの運用復旧バックアップを作成する方法について説明します。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: d9cf6933287167da82b1a5ad4ab8848d5c34d404
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579891"
---
# <a name="snapcenter-integration-for-oracle-on-baremetal-infrastructure"></a>BareMetal インフラストラクチャ上の Oracle の SnapCenter 統合

この操作方法ガイドは、Oracle と NetApp のスナップショット テクノロジを使用して、Oracle データベースの運用復旧バックアップを作成するのに役立ちます。 スナップショットの使用は、Oracle でのいくつかのデータ保護アプローチの 1 つに過ぎません。 スナップショットによって、BareMetal インフラストラクチャ上で Oracle データベースを実行する際のダウンタイムとデータ損失を軽減できます。 

>[!IMPORTANT]
>SnapCenter では Oracle for SAP アプリケーションがサポートされていますが、SAP BR\* Tools の統合は提供されません。

Oracle にはスナップショット用に 2 つの異なるバックアップ方法が用意されています。ただし、NetApp の SnapCenter ではホット バックアップ モードという 1 つの方法しかサポートされません。 ホット バックアップ モードは、Oracle スナップショットのバックアップと作成の従来型バージョンです。 データベースを一時的にホット バックアップ モードにしてアーカイブ ログを適切にカタログ化するために、Oracle ホストとの対話が必要になります。 また、ホット バックアップ モードを使用すると、RMAN データベースとのより高度な相互作用が可能になり、復旧に使用できるスナップショットをより詳細に追跡できます。 

このガイドの記事では、ホット バックアップ モードで Oracle の運用復旧とディザスター リカバリーのフレームワークを作成する手順について説明します。 ディザスター リカバリーとは、障害発生後のデータベースまたはデータベースの一部の復旧です。 例としては、正常に機能しないドライブや破損したデータベースなどがあります。 運用復旧とは、災害以外からの復旧です。 例としては、ビジネスの妨げにならない数行のデータ消失などがあります。

## <a name="snapcenter-database-organization"></a>SnapCenter データベースの編成
SnapCenter では、データベースはリソース グループに編成されます。 リソース グループは、同じ保護ポリシーを持つ 1 つ以上のデータベースで構成されます。 したがって、バックアップの一部である個々のボリュームを選択する必要はありません。

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-resource-group.png" alt-text="SnapCenter でデータベースがリソース グループに編成される方法を示す図" border="false":::

## <a name="oracle-disaster-recovery-architecture"></a>Oracle のディザスター リカバリー アーキテクチャ

Oracle ホットバックアップは、データファイルの復元後にアーカイブ ログを使用してロールフォワードするために、2 つの異なるバックアップに分割されます。 データファイルとコントロール ファイルの保護は "1 時間ごと" に行われますが、バックアップ頻度をそれより長くすることもできます。 バックアップ間隔が長くなるほど、復旧時間は長くなります。  

SnapCenter では、データベースのローカル スナップショットが既定のデータファイル場所 (nfs01) に作成されます。 スナップショットは、データファイルまたはコントロール ファイルがホストされている各ファイル システム上に作成されます。 これらのバックアップによって、データベースの高速復旧が確保されます。 マルチディスク障害やサイト障害ではデータは保護されません。 

"1 時間ごと" のスナップショットの数は、設定されているバックアップ ポリシーによって決まります。 Oracle データベースでは、スナップショットから 2 - 5 日分の運用復旧を行うことができます。
 
最新の実行可能なデータファイル復旧ポイントから Oracle データベースをロールフォワードするためには、十分なアーカイブ ログがある必要があります。 アーカイブ ログのスナップショットを使用して、Oracle データベースの回復ポイントの目標 (RPO) を下げます。 アーカイブ ログのスナップショットの頻度が短くなるほど、RPO は短くなります。 アーカイブ ログの推奨スナップショット間隔は、5 分 または 15 分です。 短い方の間隔である 5 分では、RPO は最も短くなります。  短い方の間隔では、復旧プロセスの一部としてより多くのスナップショットの管理が必要になるため、複雑さが増します。

## <a name="next-steps"></a>次のステップ

Azure から Oracle の BareMetal インフラストラクチャ サーバーにトラフィックをルーティングするように NetApp SnapCenter を設定する方法について説明します。

> [!div class="nextstepaction"]
> [トラフィックをルーティングするように SnapCenter を設定する](set-up-snapcenter-to-route-traffic.md)
