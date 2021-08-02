---
title: Azure VMware Solution の cloudadmin 資格情報のローテーション
description: Azure VMware Solution プライベート クラウドの vCenter Server および NSX-T Manager の資格情報をローテーションする方法について説明します。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 305447f8eac40a08564b9b57d82709f223f6086d
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112021503"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Azure VMware Solution の cloudadmin 資格情報のローテーション

この記事では、Azure VMware Solution プライベート クラウドで、cloudadmin の資格情報 (vCenter および NSX-T 資格情報) をローテーションします。  これらのアカウントのパスワードに有効期限はありませんが、新しいパスワードを生成することもできます。 新しいパスワードを生成したら、VMware HCX Connector に新しい資格情報を適用して更新する必要があります。

[vCenter CloudAdmin および NSX-T の管理者パスワードをリセットする](https://youtu.be/cK1qY3knj88)方法の動画も用意されています。 

## <a name="prerequisites"></a>前提条件

HCX、vRealize Orchestrator、vRealize Operations Manager、VMware Horizon などの接続済みサービスに cloudadmin の資格情報を使用している場合、パスワードを更新すると接続が停止します。  パスワードのローテーションを開始する前に、これらのサービスを停止します。 そうしないと、これらのサービスでは古い資格情報を使用して継続的に呼び出しが行なわれるため、vCenter CloudAdmin と NSX-T 管理者のアカウントが一時的にロックされることがあります。  接続済みサービスに個別のアカウントを設定する方法の詳細については、[アクセスと ID の概念](./concepts-identity.md)に関する記事を参照してください。

## <a name="reset-your-azure-vmware-solution-cloudadmin-credentials"></a>Azure VMware Solution cloudadmin の資格情報をリセットする

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

## <a name="update-hcx-connector-with-the-latest-cloudadmin-credentials"></a>HCX Connector を最新の cloudadmin 資格情報で更新する

このステップでは、HCX Connector を最新の資格情報で更新します。

1. オンプレミスの HCX コネクタ (https://{ip of the HCX connector appliance}:443) に移動し、新しい資格情報を使用してサインインします。

   必ずポート 443 を使用してください。 

2. VMware HCX ダッシュボードで、 **[Site Pairing]\(サイトのペアリング\)** を選択します。
    
   :::image type="content" source="media/rotate-cloudadmin-credentials/hcx-site-pairing.png" alt-text="[Site Pairing]\(サイトのペアリング\) が強調表示されている VMware HCX ダッシュボードのスクリーンショット。":::
 
3. Azure VMware Solution への正しい接続を選択し、 **[Edit Connection]\(接続の編集\)** を選択します。
 
4. vCenter Server CloudAdmin の新しいユーザー資格情報を指定し、 **[編集]** を選択して資格情報を保存します。 保存すると、成功と表示されます。

## <a name="next-steps"></a>次のステップ

Azure VMware Solution の vCenter Server と NSX-T Manager の資格情報のリセットについて理解したので、次の事項の学習に進むことができます。

- [Azure VMware Solution で NSX ネットワーク コンポーネントを構成する](configure-nsx-network-components-azure-portal.md)
- [Azure ネイティブのサービスを Azure VMware Solution と連携させる](integrate-azure-native-services.md)
- [VMware HCX で、Azure VMware Solution のワークロードに対するディザスター リカバリーをデプロイする](deploy-disaster-recovery-using-vmware-hcx.md)
