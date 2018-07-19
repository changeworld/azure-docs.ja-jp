---
title: Azure Backup エージェントのアップグレード
description: この情報は、Azure Backup エージェントをアップグレードする理由と、アップグレードをダウンロードする場所について説明しています。
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750332"
---
## <a name="upgrade-the-mars-agent"></a>MARS エージェントをアップグレードする
2.0.9083.0 より前のバージョンの Microsoft Azure Recovery Service (MARS) エージェントには、Azure Access Control Service (ACS) との依存関係があります。 2018 年に、Azure は [Azure Access Control Service (ACS) を非推奨にします](../articles/active-directory/develop/active-directory-acs-migration.md)。 2018 年 3 月 19 日以降、2.0.9083.0 より前のすべてのバージョンの MARS エージェントでバックアップが失敗します。 バックアップの失敗を回避または解決するには [MARS エージェントを最新バージョンにアップグレード](https://go.microsoft.com/fwlink/?linkid=229525)します。 MARS エージェントのアップグレードが必要なサーバーを識別するには、[Azure Backup エージェントのアップグレードに関するバックアップ ブログ](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)の手順に従ってください。 MARS エージェントを使って、次のデータ型を Azure にバックアップします。
- ファイル 
- システム状態データ
- System Center DPM を Azure にバックアップ
- Azure Backup Server (MABS)
