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
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 2a969240da6e16f5a5ba76605a6efa2a96f9e4dd
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43283041"
---
## <a name="upgrade-the-mars-agent"></a>MARS エージェントをアップグレードする

2.0.9083.0 より前のバージョンの Microsoft Azure Recovery Service (MARS) エージェントには、Azure Access Control Service (ACS) との依存関係があります。 MARS エージェントは、Azure Backup エージェントとも呼ばれます。 2018 年に、Azure では、[Azure Access Control Service (ACS) が非推奨になりました](../articles/active-directory/develop/active-directory-acs-migration.md)。 2018 年 3 月 19 日以降、2.0.9083.0 より前のすべてのバージョンの MARS エージェントでバックアップが失敗します。 バックアップの失敗を回避または解決するには、[MARS エージェントを最新バージョンにアップグレード](https://go.microsoft.com/fwlink/?linkid=229525)します。 MARS エージェントのアップグレードが必要なサーバーを識別するには、[MARS エージェントのアップグレードに関するバックアップ ブログ](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)の手順に従ってください。 MARS エージェントは、ファイル、フォルダー、およびシステム状態データを Azure にバックアップするために使用します。 System Center DPM および Azure Backup Server は、MARS エージェントを使用してデータを Azure にバックアップします。
