---
author: alkohli
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 24cc360da36e49726cc23b407ada5a3d88c3a2a1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082962"
---
1. デバイスの更新プログラムが利用可能になると、Azure Stack Edge リソースの **[概要]** ページに通知が表示されます。 通知を選択するか、上部のコマンド バーで **[デバイスの更新]** を選択します。 これにより、デバイスのソフトウェア更新プログラムが適用できるようになります。

    ![デバイスの更新を選択する](media/azure-stack-edge-install-2110-updates/install-updates-portal-1.png)

2. **[デバイスの更新プログラム]** ブレードで、リリース ノートの新機能に関連付けられているライセンス条項を確認したことを示すチェック ボックスをオンにします。

    更新プログラムがデバイスにダウンロードされると、更新プログラムの **[自動インストール]** を選択できます。 

    ![[更新プログラムを自動的にインストールする] オプションを選択する](media/azure-stack-edge-install-2110-updates/install-updates-portal-2.png)    

    更新プログラムをダウンロードし、**更新プログラムを後で手動でインストールする** こともできます。

    ![[更新プログラムを後で手動でインストールする] オプションを選択する](media/azure-stack-edge-install-2110-updates/install-updates-portal-3.png)

3. 更新プログラムのダウンロードが開始されます。 ダウンロードが進行中であることが通知されます。

    ![更新プログラムのダウンロードが進行中であることを表示する通知](media/azure-stack-edge-install-2110-updates/install-updates-portal-4.png)

    通知バナーは Azure portal にも表示されます。 これは、ダウンロードの進行状況を示します。 更新の詳細な状態を確認するには、この通知を選択するか、 **[デバイスの更新]** を選択します。

    ![[デバイスの更新プログラム] ブレードで詳細な更新プログラムの状態を表示する](media/azure-stack-edge-install-2110-updates/install-updates-portal-5.png)


4. ダウンロードが完了すると、通知バナーが更新され、完了が示されます。 更新プログラムを自動的にインストールすることを選択した場合、インストールが自動的に開始されます。

    更新プログラムを後で手動でインストールすることを選択した場合は、通知を選択して **[デバイスの更新プログラム]** ブレードを開きます。 **[更新プログラムのインストール]** を選択します。
 
    ![更新プログラムのダウンロード後に [更新プログラムのインストール] を選択する](media/azure-stack-edge-install-2110-updates/install-updates-portal-6.png)
 
5. インストールが進行中であることが通知されます。 ポータルには、インストールが進行中であることを示す情報アラートも表示されます。 デバイスがオフラインになり、メンテナンス モードになります。
   
    ![デバイスがメンテナンス中であることを示すバナー通知 ](media/azure-stack-edge-install-2110-updates/install-updates-portal-7.png)

6. これは 1 ノードのデバイスなので、更新プログラムのインストール後にデバイスが再起動されます。 

    ![デバイスが再起動中であることを示すバナー通知](media/azure-stack-edge-install-2110-updates/install-updates-portal-8.png)

7. 再起動後、デバイス ソフトウェアの更新が完了します。 Kubernetes ソフトウェア更新プログラムが自動的に開始されます。 デバイスが再びオフラインになり、メンテナンス モードになります。

    ![デバイスがメンテナンス モードであることを示すバナー通知](media/azure-stack-edge-install-2110-updates/install-updates-portal-9.png)   


8. デバイス ソフトウェアと Kubernetes の更新プログラムが正常にインストールされると、バナー通知は表示されなくなります。 デバイスの状態が **[Your device is online]\(デバイスはオンライン\)** に更新されます。 

    ![更新が完了してデバイスがオンラインになる](media/azure-stack-edge-install-2110-updates/install-updates-portal-10.png)

    ローカル Web UI にアクセスして、 **[ソフトウェア更新プログラム]** ページにアクセスします。 デバイス ソフトウェアと Kubernetes が正常に更新され、ソフトウェアのバージョンにそれが反映されたことを確認します。

    ![更新されたソフトウェア バージョンをローカル UI で確認する](media/azure-stack-edge-install-2110-updates/install-updates-portal-11.png)


