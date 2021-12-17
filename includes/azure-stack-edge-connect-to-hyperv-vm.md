---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 04/12/2021
ms.openlocfilehash: 299e4fdb495388780295c5d92dc3ac3e29f43295
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "112968402"
---
1. **Hyper-V マネージャー** のスコープ ペインで、VM を右クリックしてコンテキスト メニューを開いてから、 **[開始]** を選択します。 

    ![VM を選択して起動します](./media/azure-stack-edge-connect-to-hyperv-vm/connect-virtual-machine-01.png)

2. VM の状態が **[実行中]** の場合は、VM を選択し、右クリックして **[接続]** を選択します。

    ![VM への接続](./media/azure-stack-edge-connect-to-hyperv-vm/connect-virtual-machine-02.png)

3. 仮想マシンでセットアップが起動され、物理コンピューターと同じように、インストールを行うことができます。
 
   ![VM のオペレーティング システムの構成](./media/azure-stack-edge-connect-to-hyperv-vm/connect-virtual-machine-03.png)<!--Reshot. How best to generalize client name?-->

<!--Compare with the Hyper-V VM steps in https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/create-virtual-machine#complete-the-operating-system-deployment. Should licensing be raised as an issue in the Azure Stack Edge version?-->
