---
title: Azure VMware Solution by CloudSimple プライベート クラウドの削除
description: CloudSimple プライベート クラウドを削除する方法について説明します。 プライベート クラウドを削除すると、すべてのクラスターが削除されます。
author: suzizuber
ms.author: v-szuber
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b8afadea5c1f0236166f9d2cd5b7c7fa7d6c3d6e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620395"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドの削除

CloudSimple は、プライベート クラウドを削除する柔軟性を提供します。  プライベート クラウドは、1 つ以上の vSphere クラスターで構成されます。 各クラスターには、3 から 16 個のノードを含めることができます。 プライベート クラウドを削除すると、すべてのクラスターが削除されます。

## <a name="before-you-begin"></a>開始する前に

プライベート クラウドを削除すると、プライベート クラウド全体が削除されます。  プライベート クラウドのすべてのコンポーネントが削除されます。  データを保持する場合は、オンプレミスのストレージまたは Azure ストレージにデータをバックアップしていることを確認してください。

プライベート クラウドのコンポーネントには、次のものがあります。

* CloudSimple ノード
* 仮想マシン
* VLAN またはサブネット
* プライベート クラウドに格納されているすべてのユーザー データ
* VLAN またはサブネットへのすべてのファイアウォール規則の添付

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="delete-a-private-cloud"></a>プライベート クラウドを削除する

1. [CloudSimple ポータルにアクセスします](access-cloudsimple-portal.md)。

2. **[リソース]** ページを開きます。

3. 削除するプライベート クラウドをクリックします

4. [概要] ページで **[削除]** をクリックします。

    ![プライベート クラウドを削除する](media/delete-private-cloud.png)

5. 確認ページで、プライベート クラウドの名前を入力し、**[削除]** をクリックします。 

    ![プライベート クラウドを削除する - 確認](media/delete-private-cloud-confirm.png)

プライベート クラウドが削除対象としてマークされます。  削除プロセスが 3 時間後に開始され、プライベート クラウドが削除されます。

> [!CAUTION]
> プライベート クラウドを削除した後にノードを削除する必要があります。  ノードの従量制課金は、サブスクリプションからノードが削除されるまで続行されます。

## <a name="next-steps"></a>次の手順

* [ノードを削除する](delete-nodes.md)
