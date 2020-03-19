---
title: Azure Backup エージェントのアップグレード
description: この情報は、Azure Backup エージェントをアップグレードすべき理由と、アップグレードをダウンロードする場所について説明しています。
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673183"
---
## <a name="upgrade-the-mars-agent"></a>MARS エージェントをアップグレードする

2\.0.9083.0 より前のバージョンの Microsoft Azure Recovery Service (MARS) エージェントには、Azure Access Control Service との依存関係があります。 MARS エージェントは、Azure Backup エージェントとも呼ばれます。

2018 年に、Microsoft では、[Azure Access Control Service (ACS) が非推奨になりました](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)。 2018 年 3 月 19 日以降、2.0.9083.0 より前のすべてのバージョンの MARS エージェントでバックアップが失敗します。 バックアップの失敗を回避または解決するには [MARS エージェントを最新バージョンにアップグレード](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent)します。 MARS エージェントのアップグレードが必要なサーバーを識別するには、「[Microsoft Azure Recovery Services (MARS) エージェントのアップグレード](../articles/backup/upgrade-mars-agent.md)」の手順に従ってください。

MARS エージェントは、ファイル、フォルダー、およびシステム状態データを Azure にバックアップするために使用します。 System Center DPM および Azure Backup Server は、MARS エージェントを使用してデータを Azure にバックアップします。
