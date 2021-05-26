---
title: Azure VMware Solution の cloudadmin 資格情報のローテーション
description: Azure VMware Solution プライベート クラウドの vCenter Server および NSX-T Manager の資格情報をローテーションする方法について説明します。
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: fbfed495fd904b67ce283934791cee516349dfa6
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814958"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Azure VMware Solution の cloudadmin 資格情報のローテーション

この記事では、Azure VMware Solution プライベート クラウドの cloudadmin 資格情報をローテーションする手順について説明します。  vCenter Server CloudAdmin と NSX-T Manager の管理者資格情報は期限切れになりませんが、これらのアカウントの新しいパスワードを生成することは可能です。 資格情報をローテーションした後、HCX コネクタに vCenter Server の最新の資格情報が設定されていることを確認します。

[vCenter CloudAdmin および NSX-T の管理者パスワードをリセットする](https://youtu.be/cK1qY3knj88)方法の動画も用意されています。 

## <a name="prerequisites"></a>前提条件

HCX、vRealize Orchestrator、vRealize Operations Manager、VMware Horizon などの接続済みサービスに cloudadmin の資格情報を使用している場合、パスワードを更新すると接続が停止します。  パスワードのローテーションを開始する前に、これらのサービスを停止します。 そうしないと、これらのサービスでは古い資格情報を使用して継続的に呼び出しが行なわれるため、vCenter CloudAdmin と NSX-T 管理者のアカウントが一時的にロックされることがあります。  接続済みサービスに個別のアカウントを設定する方法の詳細については、[アクセスと ID の概念](./concepts-identity.md)に関する記事を参照してください。

## <a name="reset-your-azure-vmware-solution-credentials"></a>Azure VMware Solution の資格情報をリセットする

このステップでは、Azure VMware Solution コンポーネントの cloudadmin 資格情報をローテーションします。 

>[!NOTE]
>**{SubscriptionID}** 、 **{ResourceGroup}** 、および **{PrivateCloudName}** は、実際のプライベート クラウドの情報に置き換える必要があることにご注意ください。

1. Azure portal から Azure Cloud Shell セッションを開きます。

2. vCenter CloudAdmin パスワードを更新します。  

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
          
3. NSX-T 管理者パスワードを更新します。 

   ```azurecli-interactive
   az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```

## <a name="verify-hcx-connector-has-the-latest-credentials"></a>HCX コネクタに最新の資格情報が設定されていることを確認する

このステップでは、HCX コネクタに更新済みの資格情報が含まれているかどうかを確認します。

1. オンプレミスの HCX コネクタ (https://{ip of the HCX connector appliance}:443) に移動し、新しい資格情報を使用してサインインします。

   必ずポート 443 を使用してください。 

2. VMware HCX ダッシュボードで、 **[Site Pairing]\(サイトのペアリング\)** を選択します。
    
   :::image type="content" source="media/rotate-cloudadmin-credentials/hcx-site-pairing.png" alt-text="[Site Pairing]\(サイトのペアリング\) が強調表示されている VMware HCX ダッシュボードのスクリーンショット。":::
 
3. Azure VMware Solution への正しい接続を選択し、 **[Edit Connection]\(接続の編集\)** を選択します。
 
4. vCenter Server CloudAdmin の新しいユーザー資格情報を指定し、 **[編集]** を選択して資格情報を保存します。 保存すると、成功と表示されます。

## <a name="next-steps"></a>次のステップ

Azure VMware Solution の vCenter Server と NSX-T Manager の資格情報のリセットについて理解したので、次の事項の学習に進むことができます。

- [Azure VMware Solution での NSX ネットワークコンポーネントの構成](configure-nsx-network-components-azure-portal.md)
- [Azure VMware Solution VM の監視と管理](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Azure VMware Solution を使用した仮想マシンのディザスター リカバリーの実施](disaster-recovery-for-virtual-machines.md)
