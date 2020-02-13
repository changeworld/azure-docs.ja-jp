---
title: Azure VMware Solutions (AVS) - AVS プライベート クラウドをセキュリティで保護する
description: Azure VMware Solutions (AVS) プライベート クラウドをセキュリティで保護する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020082"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>AVS プライベート クラウド環境をセキュリティで保護する方法

Azure から AVS サービス、AVS ポータル、AVS プライベート クラウドに対するロールベースのアクセス制御 (RBAC) を定義します。 AVS プライベート クラウドの vCenter にアクセスするためのユーザー、グループ、ロールは、VMware SSO を使用して指定します。 

## <a name="rbac-for-avs-service"></a>AVS サービスに対する RBAC

AVS サービスの作成には、Azure サブスクリプションでの**所有者**ロールまたは**共同作成者**ロールが必要です。 既定では、すべての所有者および共同作成者が AVS サービスを作成し、AVS ポータルにアクセスして、AVS プライベート クラウドを作成したり管理したりすることができます。 作成できる AVS サービスは、各リージョンにつき 1 つだけです。 特定の管理者にアクセスを限定するには、次の手順に従ってください。

1. Azure portal で新しい**リソース グループ**に AVS サービスを作成します
2. リソース グループの RBAC を指定します。
3. ノードを購入し、AVS サービスと同じリソース グループを使用します

以後、AVS サービスの表示と AVS ポータルの起動は、このリソース グループの**所有者**と**共同作成者**の特権を持つユーザーに限定されます。

RBAC の詳細については、「[Azure リソースのロールベースのアクセス制御 (RBAC) の概要](../role-based-access-control/overview.md)」を参照してください。

## <a name="rbac-for-avs-private-cloud-vcenter"></a>AVS プライベート クラウドの vCenter に対する RBAC

AVS プライベート クラウドを作成すると、vCenter SSO ドメインに既定のユーザー `CloudOwner@AVS.local` が作成されます。 CloudOwner ユーザーには、vCenter を管理するための特権が与えられます。 vCenter SSO には、さまざまなユーザーにアクセス権を付与するための ID ソースが別途追加されます。 vCenter には、新たにユーザーを追加する際に使用できる定義済みのロールとグループが設定されます。

### <a name="add-new-users-to-vcenter"></a>vCenter に新しいユーザーを追加する

1. AVS プライベート クラウドにおける **CloudOwner@AVS.local** ユーザーの[権限をエスカレート](escalate-private-cloud-privileges.md)します。
2. **CloudOwner@AVS.local** を使用して vCenter にサインインします。
3. [vCenter シングル サインオン ユーザーを追加](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)します。
4. [vCenter シングル サインオン グループ](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)にユーザーを追加します。

定義済みのロールとグループについて詳しくは、「[VMware vCenter の AVS プライベート クラウド アクセス許可モデル](learn-private-cloud-permissions.md)」の記事を参照してください。

### <a name="add-new-identity-sources"></a>新しい ID ソースを追加する

AVS プライベート クラウドの vCenter SSO ドメインに使用する ID プロバイダーを別途追加できます。 ID プロバイダーは認証の働きをし、また、vCenter SSO グループはユーザーの承認の働きをします。

* AVS プライベート クラウドの vCenter で [ID プロバイダーとして Active Directory を使用する](set-vcenter-identity.md)。
* AVS プライベート クラウドの vCenter で [ID プロバイダーとして Azure AD を使用する](azure-ad.md)

1. AVS プライベート クラウドにおける **CloudOwner@AVS.local** ユーザーの[権限をエスカレート](escalate-private-cloud-privileges.md)します。
2. **CloudOwner@AVS.local** を使用して vCenter にサインインします。
3. ID プロバイダーから [vCenter シングル サインオン グループ](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)にユーザーを追加します。

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>AVS プライベート クラウド環境のネットワークをセキュリティで保護する

AVS プライベート クラウド環境のネットワーク セキュリティは、ネットワーク アクセスをセキュリティ保護し、リソース間のネットワーク トラフィックをコントロールすることで制御されます。

### <a name="access-to-avs-private-cloud-resources"></a>AVS プライベート クラウドのリソースにアクセスする

AVS プライベート クラウドの vCenter とリソースへのアクセスは、セキュリティで保護されたネットワーク接続を介して行われます。

* **[ExpressRoute 接続](on-premises-connection.md)** 。 オンプレミス環境との間には、ExpressRoute によって待ち時間の短い高帯域幅の安全な接続が確保されます。 オンプレミスのサービス、ネットワーク、ユーザーは、この接続を使用して AVS プライベート クラウドの vCenter にアクセスできます。
* **[サイト間 VPN ゲートウェイ](vpn-gateway.md)** 。 サイト間 VPN により、オンプレミスから安全なトンネルを介して AVS プライベート クラウドのリソースにアクセスできます。 どのオンプレミス ネットワークが AVS プライベート クラウドへのネットワーク トラフィックを送受信できるかは、ユーザーが指定します。
* **[ポイント対サイト VPN ゲートウェイ](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** 。 AVS プライベート クラウドの vCenter に対して迅速にリモート アクセスするには、ポイント対サイト VPN 接続を使用します。

### <a name="control-network-traffic-in-avs-private-cloud"></a>AVS プライベート クラウドのネットワーク トラフィックを制御する

AVS プライベート クラウドのネットワーク トラフィックは、ファイアウォール テーブルとファイアウォール規則によって制御します。 ファイアウォール テーブルに定義された規則の組み合わせに基づいて、送信元ネットワーク アドレス (IP アドレス) と送信先ネットワーク アドレス (IP アドレス) との間のネットワーク トラフィックを制御できます。

1. [ファイアウォール テーブル](firewall.md#add-a-new-firewall-table)を作成します。
2. ファイアウォール テーブルに[規則を追加](firewall.md#create-a-firewall-rule)します。
3. [ファイアウォール テーブルを VLAN またはサブネットにアタッチ](firewall.md#attach-vlanssubnet します。
