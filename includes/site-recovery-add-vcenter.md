---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171763"
---
**[Add vCenter (vCenter の追加)]** で、vSphere ホストまたは vCenter サーバーのフレンドリ名を指定し、サーバーの IP アドレスまたは FQDN を指定します。 別のポートで要求をリッスンするように VMware サーバーが構成されている場合を除き、ポートは 443 のままにしておきます。 VMware vCenter または vSphere ESXi サーバーに接続するためのアカウントを選択します。 Click **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > vCenter サーバーまたはホスト サーバーに対する管理者特権のないアカウントを使用して VMware vCenter サーバーまたは VMware vSphere ホストを追加する場合、そのアカウントで、次の特権が有効になっていることを確認します: Datacenter、Datastore、Folder、Host、Network、Resource、Virtual Machine、vSphere Distributed Switch。 さらに、VMware vCenter サーバーは Storage ビューの特権が有効になっている必要があります。
