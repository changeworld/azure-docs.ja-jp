---
title: Azure Site Recovery と Azure Backup の使用のサポート
description: Azure Site Recovery と Azure Backup を一緒に使用する方法の概要を示します。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376211"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Azure Site Recovery と Azure Backup の使用

この記事では、[Site Recovery サービス](site-recovery-overview.md)と [Azure Backup サービス](https://docs.microsoft.com/azure/backup/backup-overview)を一緒に使用するためのサポートについて説明します。

**操作** | **Site Recovery のサポート** | **詳細**
--- | --- | ---
**サービスを一緒にデプロイする** | サポートされています | サービスは相互運用可能であり、一緒に構成できます。
**ファイルのバックアップ/復元** | サポートされています | VM のバックアップとレプリケーションが有効になっていて、バックアップが取られる場合、ソース側の VM または VM グループでのファイルの復元には何の問題もありません。 レプリケーションは通常どおり続行され、レプリケーションの正常性に変化はありません。
**ディスクの復元** | 現在サポートされていません | バックアップしたディスクを復元する場合、VM のレプリケーションを無効にしてから、再度有効にする必要があります。
**VM の復元** | 現在サポートされていません | VM または VM グループを復元する場合、VM のレプリケーションを無効にしてから、再度有効にする必要があります。  


