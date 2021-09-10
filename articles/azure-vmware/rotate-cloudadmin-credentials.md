---
title: Azure VMware Solution の cloudadmin 資格情報のローテーション
description: Azure VMware Solution プライベート クラウドの vCenter Server の資格情報をローテーションする方法について説明します。
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 22a88feb7e7b8656666d82cdac4b4d02d546441e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258067"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Azure VMware Solution の cloudadmin 資格情報のローテーション

>[!IMPORTANT]
>現時点では、NSX-T Manager の *admin* のローテーションはサポートされていません。  NSX-T Manager のパスワードをローテーションするには、[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)を送信します。 このプロセスは、HCX サービスの実行に影響する可能性があります。

この記事では、Azure VMware Solution プライベート クラウドで、cloudadmin の資格情報 (vCenter の *CloudAdmin* 資格情報) をローテーションします。  このアカウントのパスワードの有効期限が切れることはありませんが、いつでも新しいパスワードを生成することができます。

>[!CAUTION]
>cloudadmin ユーザーの資格情報を使用してプライベート クラウド内の vCenter にサービスを接続した場合、パスワードをローテーションすると、それらの接続は機能しなくなります。 これらの接続では、パスワードをローテーションする前にサービスを停止しない限り、cloudadmin アカウントもロックアウトされます。

## <a name="prerequisites"></a>[前提条件]

パスワードをローテーションする前に、どのサービスが *cloudadmin@vsphere.local* として vCenter に接続されているか検討し、特定します。 これらのサービスには、HCX、vRealize Orchestrator、vRealize Operations Manager、VMware Horizon、または監視やプロビジョニングに使用されるその他のサードパーティ製ツールなどの VMware サービスが含まれる場合があります。 

cloudadmin ユーザーで vCenter に対して認証しているサービスを特定する方法の 1 つは、プライベート クラウドの vSphere Client を使用して vSphere イベントを検査することです。 このようなサービスを特定したら、パスワードをローテーションする前に、これらのサービスを停止する必要があります。 そうしないと、パスワードをローテーションした後にサービスが動作しなくなります。 また、vCenter CloudAdmin アカウントが一時的にロックされます。これは、これらのサービスが古い認証情報のキャッシュ バージョンを使用して認証を継続的に試みるためです。 

cloudadmin ユーザーを使用してサービスを vCenter に接続するのではなく、サービスごとに個別のアカウントを使用することをお勧めします。 接続済みサービスに個別のアカウントを設定する方法の詳細については、[アクセスと ID の概念](./concepts-identity.md)に関する記事を参照してください。

## <a name="reset-your-vcenter-credentials"></a>vCenter の資格情報をリセットする

1. Azure portal から Azure Cloud Shell セッションを開きます。

2. vCenter *CloudAdmin* 資格情報を更新します。  **{SubscriptionID}** 、 **{ResourceGroup}** 、および **{PrivateCloudName}** は、実際のプライベート クラウドの情報に置き換える必要があることにご注意ください。 

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
 
## <a name="update-hcx-connector"></a>HCX コネクタを更新する 

1. オンプレミスの HCX コネクタ (https://{ip of the HCX connector appliance}:443) に移動し、新しい資格情報を使用してサインインします。

   必ずポート **443** を使用してください。 

2. VMware HCX ダッシュボードで、 **[Site Pairing]\(サイトのペアリング\)** を選択します。
    
   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="[Site Pairing]\(サイトのペアリング\) が強調表示されている VMware HCX ダッシュボードのスクリーンショット。":::
 
3. Azure VMware Solution への正しい接続を選択し、 **[Edit Connection]\(接続の編集\)** を選択します。
 
4. 新しい vCenter ユーザー資格情報を指定し、 **[編集]** を選択します。これにより、資格情報が保存されます。 保存すると、成功と表示されます。


## <a name="next-steps"></a>次のステップ

Azure VMware Solution の vSphere 資格情報のリセットについて理解したので、次の事項の学習に進むことができます。

- [Azure ネイティブのサービスを Azure VMware Solution と連携させる](integrate-azure-native-services.md)
- [VMware HCX で、Azure VMware Solution のワークロードに対するディザスター リカバリーをデプロイする](deploy-disaster-recovery-using-vmware-hcx.md)
