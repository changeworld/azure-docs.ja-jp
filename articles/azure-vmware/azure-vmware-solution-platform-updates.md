---
title: Azure VMware Solution のプラットフォーム更新プログラム
description: Azure VMware Solution のプラットフォーム更新プログラムについて説明します。
ms.topic: reference
ms.date: 03/05/2021
ms.openlocfilehash: 1f1a0c29ffde20b54abb9e4d1d1127fc93a712d9
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444027"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware Solution のプラットフォーム更新プログラム


## <a name="march-4-2021"></a>2021 年 3 月 4 日

Azure VMware Solution の重要な更新プログラムは 2021 年 3 月から適用されます。 メンテナンスのタイムラインが記載された通知が Azure Service Health 経由で届きます。 この記事では、このメンテナンス中に予想されることと、プライベート クラウドの変更点について説明します。

- Azure VMware Solution では、2021 年 3 月 15 日までの間、既存のプライベート クラウドの ESXi 修正プログラムが [VMware ESXi 6.7 修正プログラム リリース ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) に適用されます。

- vSphere スタックについて [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html) で文書化されている回避策も、2021 年 3 月 15 日までの間に適用されます。

>[!NOTE]
>これには中断がともなわず、Azure VMware Services にもワークロードにも影響を与えることがありません。 メンテナンス中、"_Lost network connectivity on DVPorts (DVPorts でネットワーク接続が失われました)_ " や "_Lost uplink redundancy on DVPorts (DVPorts でアップリンクの冗長性が失われました)_ " など、さまざまな VMware アラートが vCenter に表示され、メンテナンスの進行に合わせて自動的に消えます。


## <a name="post-update"></a>更新後
完了後、新しいバージョンの VMware コンポーネントが表示されます。 問題が発生した場合、または質問がある場合は、サポート チケットを開いて、サポート チームにお問い合わせください。



