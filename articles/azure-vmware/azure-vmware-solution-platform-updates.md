---
title: Azure VMware Solution のプラットフォーム更新プログラム
description: Azure VMware Solution のプラットフォーム更新プログラムについて説明します。
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045214"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware Solution のプラットフォーム更新プログラム

Azure VMware Solution の重要な更新プログラムが 2021 年 3 月から適用されます。 メンテナンスのタイムラインが記載された通知が Azure Service Health 経由で届きます。 詳細については、「[Ａzure VMware Solution のプライベート クラウドの更新とアップグレード](concepts-upgrades.md)」をご覧ください。

## <a name="march-24-2021"></a>2021 年 3 月 24 日
すべての新しい Azure VMware Solution プライベート クラウドは、VMware vCenter バージョン 6.7U3l と NSX-T バージョン 3.1.1 と一緒にデプロイされます。 既存のプライベート クラウドは、**2021 年 6 月まで** に上記のリリースに更新およびアップグレードされます。

計画メンテナンスの日時が記載された電子メールが届きます。 アップグレードを再スケジュールできます。 電子メールには、アップグレードされたコンポーネントの詳細や、ワークロード、プライベート クラウドへのアクセス、その他の Azure サービスへの影響についても記載されています。  アップグレードの 1 時間前と完了後にも通知が届きます。

## <a name="march-15-2021"></a>2021 年 3 月 15 日 

- Azure VMware Solution サービスのメンテナンス作業を **2021 年 3 月 19 日** まで実施し、プライベート クラウド内の vCenter サーバーを vCenter Server 6.7 Update 3l バージョンに更新します。

- VMware vCenter は、この期間中は利用できなくなります。  そのため、VM を管理 (停止、開始、作成、削除) したり、プライベートクラウドのスケーリング (サーバーとクラスターの追加や削除) を行ったりすることはできません。 ただし、VMware High Availability (HA) は、既存の VM を保護するために動作し続けます。 
 
この vCenter バージョンの詳細については、[VMware vCenter Server 6.7 Update 3l のリリースノート](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)を参照してください。

## <a name="march-4-2021"></a>2021 年 3 月 4 日

- Azure VMware Solution では、**2021 年 3 月 15 日** までの間に、[VMware ESXi 6.7、パッチ リリース ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) が既存のプライベート クラウドに適用されます。

- vSphere スタックについて [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html) で文書化されている回避策も、**2021 年 3 月 15 日** までの間に適用されます。

>[!NOTE]
>これには中断がともなわず、Azure VMware Services にもワークロードにも影響を与えることがありません。 メンテナンス中、"_Lost network connectivity on DVPorts (DVPorts でネットワーク接続が失われました)_ " や "_Lost uplink redundancy on DVPorts (DVPorts でアップリンクの冗長性が失われました)_ " など、さまざまな VMware アラートが vCenter に表示され、メンテナンスの進行に合わせて自動的に消えます。

## <a name="post-update"></a>更新後
完了後、新しいバージョンの VMware コンポーネントが表示されます。 問題が発生した場合、または質問がある場合は、サポート チケットを開いて、サポート チームにお問い合わせください。