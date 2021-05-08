---
title: Azure VMware Solution の vSphere 資格情報をリセットする
description: Azure VMware Solution プライベート クラウドの vSphere 資格情報をリセットし、HCX コネクタに最新の vSphere 資格情報が確実に設定されるようにする方法について説明します。
ms.topic: how-to
ms.date: 03/31/2021
ms.openlocfilehash: 793b79e42a0adbca54804d1b66102736aff22d7a
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109103"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Azure VMware Solution の vSphere 資格情報をリセットする

この記事では、Azure VMware Solution プライベート クラウドの vCenter Server および NSX-T Manager の資格情報をリセットする手順について説明します。 これにより、HCX コネクタに最新の vCenter Server の資格情報を確実に設定できます。

こちらの攻略ガイドに加えて、[vCenter CloudAdmin と NSX-T 管理者パスワードのリセット](https://youtu.be/cK1qY3knj88)に関するビデオも視聴できます。

## <a name="reset-your-azure-vmware-solution-credentials"></a>Azure VMware Solution の資格情報をリセットする

 まず、Azure VMware Solution コンポーネントの資格情報をリセットしましょう。 vCenter Server CloudAdmin と NSX-T の管理者資格情報は期限切れになりませんが、次の手順に従って、これらのアカウントの新しいパスワードを生成できます。

> [!NOTE]
> HCX、vRealize Orchestrator、vRealizae Operations Manager、VMware Horizon などの接続済みサービスに CloudAdmin の資格情報を使用している場合、パスワードを更新すると接続が停止します。  パスワードのローテーションを開始する前に、これらのサービスを停止する必要があります。  そうしないと、これらのサービスでは古い資格情報を使用して継続的に呼び出しを行うため、vCenter CloudAdmin と NSX-T 管理者のアカウントが一時的にロックされることがあります。  接続済みサービスに個別のアカウントを設定する方法の詳細については、[アクセスと ID の概念](https://docs.microsoft.com/azure/azure-vmware/concepts-identity)に関する記事を参照してください。

1. Azure portal から Azure Cloud Shell セッションを開きます。

2. 次のコマンドを実行して、vCenter CloudAdmin パスワードを更新します。  {SubscriptionID}、{ResourceGroup}、{PrivateCloudName} は、CloudAdmin アカウントが属しているプライベート クラウドの実際の値に置き換える必要があります。

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. 次のコマンドを実行して、NSX-T 管理者パスワードを更新します。 {SubscriptionID}、{ResourceGroup}、{PrivateCloudName} は、NSX-T 管理者アカウントが属しているプライベート クラウドの実際の値に置き換える必要があります。

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>HCX コネクタに vCenter Server の最新の資格情報を確実に設定する

資格情報をリセットしたので、次の手順に従って、更新した資格情報を HCX コネクタに確実に設定します。

1. パスワードが変更されたら、 https://{コネクタ アプライアンスのIP}:443 を使用してオンプレミスの HCX コネクタ Web インターフェイスにアクセスします。 必ずポート 443 を使用してください。 新しい資格情報を使用してログインします。

2. VMware HCX ダッシュボードで、 **[Site Pairing]\(サイトのペアリング\)** を選択します。
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="[Site Pairing]\(サイトのペアリング\) が強調表示されている VMware HCX ダッシュボードのスクリーンショット。":::
 
3. (複数の場合は) Azure VMware Solution への適切な接続を選択し、 **[Edit Connection]\(接続の編集\)** を選択します。
 
4. vCenter Server CloudAdmin の新しいユーザー資格情報を指定し、 **[編集]** を選択して資格情報を保存します。 保存すると、成功と表示されます。

## <a name="next-steps"></a>次のステップ

Azure VMware Solution の vCenter Server と NSX-T Manager の資格情報のリセットについて理解したので、次の事項の学習に進むことができます。

- [Azure VMware Solution での NSX ネットワーク コンポーネントの構成](configure-nsx-network-components-azure-portal.md)。
- [Azure VMware Solution VM のライフサイクル管理](lifecycle-management-of-azure-vmware-solution-vms.md)。
- [Azure VMware Solution を使用した仮想マシンのディザスター リカバリーの実施](disaster-recovery-for-virtual-machines.md)。
