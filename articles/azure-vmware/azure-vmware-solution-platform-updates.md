---
title: Azure VMware Solution のプラットフォーム更新プログラム
description: Azure VMware Solution のプラットフォーム更新プログラムについて説明します。
ms.topic: reference
ms.date: 09/21/2021
ms.openlocfilehash: 3159323fa567bc622c9627ded4e8f70bfac20b4f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636118"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware Solution のプラットフォーム更新プログラム

Azure VMware Solution の重要な更新プログラムが 2021 年 3 月から適用されます。 メンテナンスのタイムラインが記載された通知が Azure Service Health 経由で届きます。 詳細については、「[ホストのメンテナンスとライフサイクル管理](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)」を参照してください。

## <a name="september-21-2021"></a>2021 年 9 月 21 日
VMware セキュリティ アドバイザリ [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html) によると、VMware vCenter Server の複数の脆弱性が VMware に報告されています。
 
VMware セキュリティ アドバイザリ [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html) で報告された脆弱性 (CVE-2021-21980、CVE-2021-21991、CVE-2021-21992、CVE-2021-21993、CVE-2021-22005、CVE-2021-22006、CVE-2021-22007、CVE-2021-22008、CVE-2021-22009、CVE-2021-22010、CVE-2021-22011、CVE-2021-22012、CVE-2021-22013、CVE-2021-22014、CVE-2021-22015、CVE-2021-22016、CVE-2021-22017、CVE-2021-22018、CVE-2021-22019、CVE-2021-22020) に対処するために、すべての Azure VMware Solution プライベート クラウドで vCenter Server が 6.7 Update 3o に更新されました。 すべての新しい Azure VMware Solution プライベート クラウドは、vCenter Server バージョン 6.7 Update 3o を使用してデプロイされます。
 
詳細については、[VMware vCenter Server 6.7 Update 3o リリース ノート](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3o-release-notes.html)を参照してください。
 
ユーザーによる対処は不要です。

## <a name="september-10-2021"></a>2021 年 9 月 10 日

すべての新しい Azure VMware Solution プライベート クラウドは、ESXi バージョン ESXi670-202103001 (ビルド番号: 17700523) を使用してデプロイされるようになりました。 既存のプライベート クラウド内の ESXi ホストは、このバージョンになるようパッチが適用されています。

この ESXi バージョンの詳細については、「[VMware ESXi 6.7, Patch Release ESXi670-202103001](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202103001.html)」を参照してください。




## <a name="july-23-2021"></a>2021 年 7 月 23 日

すべての新しい Azure VMware Solution プライベート クラウドは、NSX-T バージョン [!INCLUDE [nsxt-version](includes/nsxt-version.md)] でデプロイされます。 既存のプライベート クラウドの NSX-T バージョンは、2021 年 9 月までに NSX-T [!INCLUDE [nsxt-version](includes/nsxt-version.md)] リリースにアップグレードされます。
 
計画メンテナンスの日時が記載された電子メールが届きます。 アップグレードを再スケジュールできます。 電子メールには、アップグレードされたコンポーネントの詳細や、ワークロード、プライベート クラウドへのアクセス、その他の Azure サービスへの影響についても記載されています。 

この NSX-T バージョンの詳細については、「[VMware NSX-T データ センター [!INCLUDE [nsxt-version](includes/nsxt-version.md)] リリース ノート」を参照してください](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html)。




## <a name="may-25-2021"></a>2021 年 5 月 25 日
VMware セキュリティ アドバイザリ [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html) によると、VMware ESXi と vSphere Client (HTML5) の複数の脆弱性が VMware にレポートされています。 

VMware セキュリティ アドバイザリ [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html) でレポートされている脆弱性 ([CVE-2021-21985](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21985) および [CVE-2021-21986](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21986)) に対処するために、すべての Azure VMware Solution プライベート クラウドの vCenter Server が更新されました。

これ以上操作は必要ありません。

## <a name="may-21-2021"></a>2021 年 5 月 21 日
 
Azure VMware Solution サービスは、プライベート クラウドの vCenter サーバーに重要な更新プログラムを適用するために、2021 年 5 月 23 日までメンテナンス作業を行います。  プライベート クラウドのメンテナンスのタイムラインが記載された通知が、Azure Service Health 経由で届きます。
 
この間、VMware vCenter は使用できなくなり、VM を管理 (停止、開始、作成、または削除) できなくなります。 プライベート クラウドでこの期間中に、プライベート クラウドの拡大、新しいネットワークの作成など、その他のアクティビティを計画しないことをお勧めします。
 
プライベート クラウドで実行されているワークロードには影響しません。


## <a name="april-26-2021"></a>2021 年 4 月 26 日
すべての新しい Azure VMware Solution プライベート クラウドが、VMware vCenter バージョン 6.7U3l および NSX-T バージョン 2.5.2 と一緒にデプロイされるようになりました。 新しいプライベート クラウドには、NSX-T 3.1.1 は使用されません。これは、お客様の VM 接続に影響する NSX-T 3.1.1 の問題が特定されたためです。 

VMware 推奨の軽減策が、Azure VMware Solution で NSX-T 3.1.1 を現在実行している既存のすべてのプライベート クラウドに適用されました。 この回避策は、お客様の VM 接続には影響しないことが確認されています。

## <a name="march-24-2021"></a>2021 年 3 月 24 日
すべての新しい Azure VMware Solution プライベート クラウドは、VMware vCenter バージョン 6.7U3l と NSX-T バージョン 3.1.1 と一緒にデプロイされます。 既存のプライベート クラウドは、**2021 年 6 月まで** に上記のリリースに更新およびアップグレードされます。

計画メンテナンスの日時が記載された電子メールが届きます。 アップグレードを再スケジュールできます。 電子メールには、アップグレードされたコンポーネントの詳細や、ワークロード、プライベート クラウドへのアクセス、その他の Azure サービスへの影響についても記載されています。  アップグレードの 1 時間前と完了後にも通知が届きます。

## <a name="march-15-2021"></a>2021 年 3 月 15 日 

- Azure VMware Solution サービスのメンテナンス作業を **2021 年 3 月 19 日** まで実施し、プライベート クラウド内の vCenter サーバーを vCenter Server 6.7 Update 3l バージョンに更新します。

- VMware vCenter は、この期間中は利用できなくなるため、VM は管理 (停止、開始、作成、削除) できません。また、プライベート クラウドのスケーリング (サーバーとクラスターの追加または削除) もできません。 ただし、VMware High Availability (HA) は、既存の VM を保護するために動作し続けます。 
 
この vCenter バージョンの詳細については、[VMware vCenter Server 6.7 Update 3l のリリースノート](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)を参照してください。

## <a name="march-4-2021"></a>2021 年 3 月 4 日

- Azure VMware Solution では、**2021 年 3 月 15 日** までの間に、[VMware ESXi 6.7、パッチ リリース ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) が既存のプライベート クラウドに適用されます。

- vSphere スタックについて [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html) で文書化されている回避策も、**2021 年 3 月 15 日** までの間に適用されます。

>[!NOTE]
>これには中断がともなわず、Azure VMware Services にもワークロードにも影響を与えることがありません。 メンテナンス中、"_Lost network connectivity on DVPorts (DVPorts でネットワーク接続が失われました)_ " や "_Lost uplink redundancy on DVPorts (DVPorts でアップリンクの冗長性が失われました)_ " など、さまざまな VMware アラートが vCenter に表示され、メンテナンスの進行に合わせて自動的に消えます。

## <a name="post-update"></a>更新後
完了後、新しいバージョンの VMware コンポーネントが表示されます。 問題が発生した場合、または質問がある場合は、サポート チケットを開いて、サポート チームにお問い合わせください。
