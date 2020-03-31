---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181180"
---
## <a name="use-the-azure-portal"></a>Azure ポータルの使用
1. 再デプロイする VM を選択し、 *[設定]* ブレードで *[再デプロイ]* をクリックします。 次の例に示すように、[再デプロイ] が含まれる **[サポート + トラブルシューティング]** セクションが表示されるまで必要に応じてスクロールしてください。
   
    ![Azure VM ブレード](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. *[再デプロイ]* をクリックして、操作を確定します。
   
    ![VM の再デプロイ ブレード](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. VM が再デプロイの準備に入ると、次の例に示すように VM の **[ステータス]** が "*更新中*" に変わります。
   
    ![VM 更新中](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. VM が新しい Azure ホスト上で起動すると、次の例に示すように VM の **[ステータス]** が "*開始中*" に変わります。
   
    ![VM 開始中](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. VM の起動プロセスが完了すると、**ステータス**は *"実行中"* に戻ります。これは、VM が適切に再デプロイされたことを示します。
   
    ![VM 実行中](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

