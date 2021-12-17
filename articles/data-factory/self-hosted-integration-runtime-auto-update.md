---
title: セルフホステッド統合ランタイムの自動更新と期限切れ通知
description: セルフホステッド統合ランタイムの自動更新と期限切れ通知について説明します。
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: 5a1cf4366ffd86491cc3e3e09358e364d878cb87
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989339"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>セルフホステッド統合ランタイムの自動更新と期限切れ通知

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、セルフホステッド統合ランタイムを最新バージョンに自動更新する方法と、ADF でセルフホステッド統合ランタイムのバージョンが管理される方法について説明します。

## <a name="self-hosted-integration-runtime-auto-update"></a>セルフホステッド統合ランタイムの自動更新
一般に、ローカル コンピューターまたは Azure VM にセルフホステッド統合ランタイムをインストールする場合、セルフホステッド統合ランタイムのバージョンを管理するためのオプションには自動更新と手動での管理の 2 つがあります。 通常、ADF では毎月、新機能のリリース、バグ修正、または機能強化を含むセルフホステッド統合ランタイムの 2 つの新しいバージョンをリリースします。 そのため、最新の機能と機能強化を入手するために、最新バージョンに更新することをお勧めします。

最も便利な方法は、セルフホステッド統合ランタイムを作成または編集するときに自動更新を有効にすることです。 セルフホステッド統合ランタイムは最新版に自動的に更新されます。 また、希望する最も適した時間スロットに更新をスケジュールすることもできます。

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update.png" alt-text="自動更新を有効にする":::

セルフホステッド統合ランタイム クライアントで最終更新の日付と時刻を確認できます。

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update-2.png" alt-text="更新時刻の確認のスクリーンショット":::

この [PowerShell コマンド](/powershell/module/az.datafactory/get-azdatafactoryv2integrationruntime?view=azps-6.1.0&preserve-view=true#example-5--get-self-hosted-integration-runtime-with-detail-status)を使用し、自動更新版を入手できます。 

> [!NOTE]
> セルフホステッド統合ランタイム ノードが複数ある場合、自動更新中のダウンタイムはありません。 自動更新は最初に 1 つのノードで実行されます。その間、他のノードはタスクを処理します。 更新が完了した最初のノードにより残りのタスクが引き継がれ、他のノードの更新が始まります。 セルフホステッド統合ランタイムが 1 つしかない場合、自動更新中には若干のダウンタイムが発生します。

## <a name="auto-update-version-vs-latest-version"></a>自動更新バージョンと最新バージョン
セルフホステッド統合ランタイムの安定を確保するため、2 つのバージョンをリリースしますが、毎月 1 つのバージョンのみをプッシュします。 そのため、自動更新バージョンが、実際の最新バージョンの前のバージョンである場合があります。 最新バージョンを取得する場合は、[ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)に移動できます。 また、バージョンへの自動更新は管理されたものです。 変更することはできません。 最新バージョンにアップグレードする場合は、手動で行う必要があります。 

ADF ポータルのセルフホステッド統合ランタイム **自動更新** ページには、現行版が古い場合、最新版が表示されます。 セルフホステッド統合ランタイムがオンラインのとき、このバージョンは自動更新バージョンであり、スケジュールされた時刻にセルフホステッド統合ランタイムを自動更新します。 ただし、セルフホステッド統合ランタイムがオフラインの場合、ページには最新版のみが表示されます。

複数のノードがあり、何らかの理由でそれらの一部が正常に自動更新されない場合、 これらのノードは自動更新前のノード全体で同一だったバージョンにロールバックされます。 

## <a name="self-hosted-integration-runtime-expire-notification"></a>セルフホステッド統合ランタイムの期限切れ通知
セルフホステッド統合ランタイムのバージョンを手動で制御する場合は、自動更新の設定を無効にして、手動でインストールすることができます。 セルフホステッド統合ランタイムの各バージョンは 1 年で有効期限が切れます。 有効期限切れのメッセージは、有効期限の **90 日** 前に ADF ポータルとセルフホステッド統合ランタイム クライアントに表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory の統合ランタイムの概念](./concepts-integration-runtime.md)を確認します。

- [Azure portal 上でセルフホステッド統合ランタイムを作成する](./create-self-hosted-integration-runtime.md)方法を確認します。
