---
title: Azure Site Recovery と Azure Backup の相互運用性  | Microsoft Docs
description: Azure Site Recovery と Azure Backup を一緒に使用する方法の概要を示します。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731871"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>Site Recovery と Backup の相互運用性について

この記事では、Azure IaaS VM Backup と Azure VM ディザスター リカバリーを正しく使用するためのガイダンスを提供します。

## <a name="azure-backup"></a>Azure Backup

Azure Backup は、オンプレミス サーバー、仮想マシン、仮想化されたワークロード、SQL Server、SharePoint サーバーなどのデータの保護に役立ちます。 Azure Site Recovery は、Azure VM、オンプレミス VM、物理サーバーのディザスター リカバリーの調整と管理を行います。

## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Backup と Azure Site Recovery の両方を 1 台の VM または VM グループに構成することが可能です。 両方の製品は、相互運用が可能です。 Backup と Azure Site Recovery の間の相互運用が重要になるシナリオとしては次のようなものがあります。

### <a name="file-backuprestore"></a>ファイルのバックアップ/復元

バックアップとレプリケーションの両方が有効になっていて、バックアップが取られる場合、ソース サイド VM または VM グループでのファイルの復元には何の問題もありません。 レプリケーションは通常どおり続行され、レプリケーションの正常性に変化はありません。

### <a name="disk-backuprestore"></a>ディスクのバックアップ/復元

ディスクをバックアップから復元する場合は、仮想マシンの保護を再び有効にする必要があります。

### <a name="vm-backuprestore"></a>VM のバックアップ/復元

VM または VM グループのバックアップと復元はサポートされていません。 機能するようにするには、保護を再度有効にする必要があります。

**シナリオ** | **Azure Site Recovery によってサポートされるか** | **回避策 (ある場合)**  
--- | --- | ---
ファイル/フォルダーのバックアップ | はい | 適用外
ディスクのバックアップ | 現時点ではできません | 保護の無効化と有効化
VM のバックアップ | いいえ  | 保護の無効化と有効化
