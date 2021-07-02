---
title: 'チュートリアル: Azure VMware Solution のプライベート クラウドをデプロイする'
description: Azure VMware Solution のプライベート クラウドを作成してデプロイする方法を説明します
ms.topic: tutorial
ms.date: 06/11/2021
ms.openlocfilehash: 5d882098c29db7fb0272bb503eae42a244113b5b
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112021377"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>チュートリアル: Azure VMware Solution のプライベート クラウドをデプロイする

Azure VMware Solution のプライベートを使用すると、Azure に vSphere クラスターをデプロイできます。 作成された各プライベート クラウドには、既定で 1 つの vSAN クラスターがあります。 クラスターは、追加したり、削除したり、スケーリングしたりすることができます。  クラスターあたりのホストの最小数は 3 です。 さらに増やす場合は、クラスターあたり最大 16 個まで、一度に 1 つずつホストを追加できます。 プライベート クラウドあたりのクラスターの最大数は 4 です。  Azure VMware Solution の初期デプロイには、3 つのホストがあります。 

試用版クラスターは評価用に利用でき、3 台のホストに制限されます。 プライベート クラウドごとに 1 つの試用版クラスターがあります。 試用版クラスターは、評価期間中、1 つのホストによってスケーリングできます。

クラスターの構成や操作のその他ほとんどの側面を管理するには、vSphere と NSX-T Manager を使用します。 クラスター内の各ホストのローカル ストレージはすべて、vSAN の管理下にあります。

>[!TIP]
>最初のデプロイ数を超える数が必要な場合は、後でいつでもクラスターを拡張したり、クラスターを追加したりできます。

Azure VMware Solution では、最初はオンプレミスの vCenter でプライベート クラウドを管理することができないため、別途構成が必要です。 このチュートリアルでは、これらの手順と、関連する前提条件について説明します。

このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * Azure VMware Solution のプライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する

## <a name="prerequisites"></a>前提条件

- プライベート クラウドを作成するための適切な管理者権限とアクセス許可。 サブスクリプションで、少なくとも共同作成者レベルである必要があります。
- [計画](production-ready-deployment-steps.md)の記事で収集した情報に従って、Azure VMware Solution をデプロイします。
- 適切なネットワークが構成されていることを確認するには、[ネットワーク計画のチェックリスト](tutorial-network-checklist.md)をご確認ください。
- ホストがプロビジョニングされており、Microsoft.AVS の[リソース プロバイダーが登録されている](deploy-azure-vmware-solution.md#step-1-register-the-microsoftavs-resource-provider)。

## <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure VMware Solution のプライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する
> * Azure VMware Solution のプライベート クラウドを削除する

次のチュートリアルに進み、ジャンプ ボックスの作成方法を習得します。 プライベート クラウドにローカルで接続できるよう、ジャンプ ボックスを使用して環境に接続します。


> [!div class="nextstepaction"]
> [Azure VMware Solution プライベート クラウドにアクセスする](tutorial-access-private-cloud.md)
