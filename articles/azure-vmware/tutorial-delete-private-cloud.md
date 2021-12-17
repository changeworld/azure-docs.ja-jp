---
title: チュートリアル - Azure VMware Solution のプライベート クラウドを削除する
description: 不要になった Azure VMware Solution のプライベート クラウドを削除する方法について説明します。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: ed1a4fdc2487bf470c96afa5ff8b3cde67336a40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730209"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>チュートリアル:Azure VMware Solution のプライベート クラウドを削除する

Azure VMware Solution のプライベート クラウドが不要になった場合は、削除できます。 プライベート クラウドには次のものが含まれます。

* 分離されたネットワーク ドメイン

* 専用サーバー ホスト上の 1 つ以上のプロビジョニング済み vSphere クラスター

* 複数の仮想マシン (VM)

プライベート クラウドを削除すると、すべての VM とそのデータ、クラスター、プロビジョニングされたネットワーク アドレス空間が削除されます。 Azure VMware Solution 専用ホストは安全にワイプされ、空きプールに返されます。   

> [!CAUTION]
> プライベート クラウドを削除すると、実行中のすべてのワークロードとコンポーネントが終了します。これは元に戻すことのできない操作です。 プライベート クラウドを削除すると、データを復旧することはできません。

## <a name="prerequisites"></a>前提条件

VM とそのデータが後で必要な場合は、プライベート クラウドを削除する前に、必ずデータをバックアップしてください。  残念ながら、VM とそのデータを復旧する方法はありません。


## <a name="delete-the-private-cloud"></a>プライベート クラウドを削除する

1. [Azure portal](https://portal.azure.com) で、Azure VMware Solutions コンソールにアクセスします。

2. 削除するプライベート クラウドを選択します。
 
3. プライベート クラウドの名前を入力し、 **[はい]** を選択します。 

>[!NOTE]
>削除プロセスは数時間で完了します。  
