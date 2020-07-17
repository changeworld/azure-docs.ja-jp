---
title: Azure Site Recovery データ暗号化機能の廃止 | Microsoft Docs
description: Azure Site Recovery データ暗号化機能の詳細
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134399"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Site Recovery のデータ暗号化機能の廃止

このドキュメントでは、Azure への Hyper-V 仮想マシンのディザスター リカバリーを構成するときに Site Recovery データ暗号化機能を使用する場合に必要となる、廃止の詳細と修復アクションについて説明します。 

## <a name="deprecation-information"></a>廃止情報


Site Recovery データ暗号化機能は、レプリケートされたデータがセキュリティ上の脅威から保護されることを保証するため、Hyper-V VM を保護するお客様のために提供されました。 この機能は、**2019 年 12 月 30 日**に廃止になります。 これは、[Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) を使用する、より高度な[保存時の暗号化](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/)機能に置き換えられています。 SSE を使用すると、データは暗号化されてからストレージに保存され、取得時に復号化されます。また、Azure へのフェールオーバー時に、VM は暗号化されたストレージ アカウントから実行されるため、目標復旧時間 (RTO) が向上します。

この機能を使用している既存のお客様には、廃止の詳細と修復の手順が記載された通知が送られています。 


## <a name="what-are-the-implications"></a>どのような影響がありますか?

**2019 年 12 月 30 日**を過ぎると、廃止された暗号化機能を引き続き使用しているすべての VM で、フェールオーバーが実行できなくなります。 

## <a name="required-action"></a>必要なアクション
正常なフェールオーバー操作とレプリケーションを続行するには、次の手順に従います。

各 VM に対して次の手順を実行します。 
1.  [レプリケーションを無効にする](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)。
2.  [新しいレプリケーション ポリシーを作成する](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy)。
3.  [レプリケーションを有効](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication)にし、SSE が有効になっているストレージ アカウントを選択する。

SSE が有効になっているストレージ アカウントへの初期レプリケーションが完了すると、VM で Azure Site Recovery による保存時の暗号化が使用されるようになります。


## <a name="next-steps"></a>次のステップ
修復手順の実行を計画し、最も早い段階で実行します。 この廃止に関してご質問がおありの場合は、Microsoft サポートにお問い合わせください。 Hyper-V から Azure へのシナリオの詳細については、[こちら](hyper-v-vmm-architecture.md)を参照してください。

