---
title: Azure VMware Solutions (AVS) プライベート クラウドを削除する
description: AVS プライベート クラウドを削除する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024757"
---
# <a name="delete-an-avs-private-cloud"></a>AVS プライベート クラウドを削除する

AVS では、AVS プライベート クラウドを削除する柔軟性が提供されます。 AVS プライベート クラウドは、1 つ以上の vSphere クラスターで構成されます。 各クラスターには、3 から 16 個のノードを含めることができます。 AVS プライベート クラウドを削除すると、すべてのクラスターが削除されます。

## <a name="before-you-begin"></a>開始する前に

AVS プライベート クラウドを削除すると、AVS プライベート クラウド全体が削除されます。 AVS プライベート クラウドのすべてのコンポーネントが削除されます。 データを保持する場合は、オンプレミスのストレージまたは Azure ストレージにデータをバックアップしていることを確認してください。

AVS プライベート クラウドのコンポーネントには、次のものがあります。

* AVS ノード
* 仮想マシン
* VLAN またはサブネット
* AVS プライベート クラウドに格納されているすべてのユーザー データ
* VLAN またはサブネットへのすべてのファイアウォール規則の添付

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="delete-an-avs-private-cloud"></a>AVS プライベート クラウドを削除する

1. [AVS ポータルにアクセスします](access-cloudsimple-portal.md)。

2. **[リソース]** ページを開きます。

3. 削除する AVS プライベート クラウドをクリックします

4. [概要] ページで **[削除]** をクリックします。

    ![AVS プライベート クラウドを削除する](media/delete-private-cloud.png)

5. 確認ページで、AVS プライベート クラウドの名前を入力し、 **[削除]** をクリックします。 

    ![AVS プライベート クラウドを削除する - 確認](media/delete-private-cloud-confirm.png)

AVS プライベート クラウドが削除対象としてマークされます。 削除プロセスが 3 時間後に開始され、AVS プライベート クラウドが削除されます。

> [!CAUTION]
> AVS プライベート クラウドを削除した後にノードを削除する必要があります。 ノードの従量制課金は、サブスクリプションからノードが削除されるまで続行されます。

## <a name="next-steps"></a>次のステップ

* [ノードを削除する](delete-nodes.md)
