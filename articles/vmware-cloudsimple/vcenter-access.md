---
title: Azure VMware Solutions (AVS) - vSphere クライアントにアクセスする
description: AVS プライベート クラウドから vCenter にアクセスする方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022666"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>AVS プライベート クラウドの vCenter ポータルへのアクセス

AVS プライベート クラウドの vCenter ポータルは、Azure portal または AVS ポータルから起動できます。 vCenter ポータルでは、AVS プライベート クラウド上の VMware インフラストラクチャを管理できます。

## <a name="before-you-begin"></a>開始する前に

vCenter ポータルにアクセスするには、ネットワーク接続を確立し、DNS 名前解決を有効にする必要があります。 AVS プライベート クラウドへのネットワーク接続を確立するには、次のいずれかのオプションを使用できます。

* [ExpressRoute を使用してオンプレミスから AVS に接続する](on-premises-connection.md)
* [AVS プライベート クラウドへの VPN 接続を構成する](set-up-vpn.md)

AVS プライベート クラウドの VMware インフラストラクチャ コンポーネントの DNS 名前解決を設定するには、「[オンプレミスのワークステーションから AVS プライベート クラウドの vCenter にアクセスするための名前解決用に DNS を構成する](on-premises-dns-setup.md)」を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="access-vcenter-from-azure-portal"></a>Azure Portal から vCenter にアクセスする

Azure portal から AVS プライベート クラウドの vCenter ポータルを起動することができます。

1. **[すべてのサービス]** を選択します。

2. **AVS サービス**を検索します。

3. 接続する AVS プライベート クラウドの AVS サービスを選択します。

4. **[概要]** ページで、 **[View VMware AVS Private Clouds]\(VMware AVS プライベート クラウドの表示\)** をクリックします

    ![AVS サービスの概要](media/cloudsimple-service-overview.png)

5. AVS プライベート クラウドの一覧から AVS プライベート クラウドを選択し、 **[vSphere クライアントの起動]** をクリックします。

    ![vSphere Client を起動する](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>AVS ポータルから vCenter にアクセスする

AVS ポータルから AVS プライベート クラウドの vCenter ポータルを起動することができます。

1. [AVS ポータル](access-cloudsimple-portal.md)にアクセスします。

2. **[リソース]** から、アクセスする AVS プライベート クラウドを選択し、 **[vSphere クライアントの起動]** をクリックします。

    ![vSphere クライアントの起動 - リソース](media/cloudsimple-portal-resources-launch-vcenter.png)

3. また、AVS プライベート クラウドの概要画面から vCenter ポータルを起動することもできます。

    ![vSphere クライアントの起動 - 概要](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>次のステップ

* [AVS プライベート クラウド用の VLAN またはサブネットを作成して管理する](create-vlan-subnet.md)
* [VMware vCenter の AVS プライベート クラウド アクセス許可モデル](learn-private-cloud-permissions.md)