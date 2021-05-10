---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465620"
---
VPN を確認するには、作成した仮想ネットワークからのみアクセスできるストレージ アカウントを作成します。 このストレージ アカウントを作成して、作成した仮想ネットワークに関連付けるには、次の手順に従います。

1. ストレージ アカウントを作成します。 Azure Stack Edge デバイスで使用する予定のストレージ アカウントを使用できます。 外部ネットワーク (選択したネットワークの外部) から、ストレージ アカウントへのアクセスを試してください。 ストレージ アカウントにアクセスできるはずです。
2. Azure portal で、ストレージ アカウントに移動します。 
3. **[ファイアウォールと仮想ネットワーク]** に移動します。 **[許可するアクセス元]** の右側のウィンドウで、 **[選択されたネットワーク]** を選択します。 **[仮想ネットワークを持つストレージ アカウントをセキュリティで保護する]** で **[+ 既存の仮想ネットワークを追加]** を選択します。

    ![VPN を確認する 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. **[ネットワークを追加する]** ブレードで、次の操作を行います。

    - サブスクリプションを選択します。 これは、Azure Stack Edge リソースや Data Box Gateway リソースに関連付けられているものと同じサブスクリプションです。 
    - ドロップダウン リストから、このストレージ アカウントに関連付ける仮想ネットワークを選択します。 前の手順で作成した仮想ネットワークを選択します。
    - **[サブネット]** で、*_[既定値]_* と GatewaySubnet を選択します。 仮想ネットワークとサブネットのこの組み合わせに対して、サービス エンドポイントが有効になります。 アクセスが有効になるまでに、最大 15 分かかります。
    - **[有効化]** を選択します。

    ![VPN を確認する 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. **[設定]** を保存します。

    ![VPN を確認する 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. 設定が保存されると、仮想ネットワークが有効になっているサブネットを表示することができます。

    ![VPN を確認する 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. データが VPN 経由でのみ移動することを確認するには、次の手順に従います。 
    - 外部ネットワーク (選択したネットワークの外部) から、ストレージ アカウントへのアクセスを試してください。 ストレージ アカウントにアクセスできないはずです。 
    - 選択したネットワークで有効にした仮想ネットワークとサブネットから、ストレージ アカウントへのアクセスを試してください。 ストレージ アカウントにアクセスできるはずです。 
 
このストレージ アカウントには、VPN が有効になっている場合にのみアクセスできます。 VPN を無効にすると、ストレージ アカウントのネットワーク設定も調整する必要があります。 

詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../articles/storage/common/storage-network-security.md)」をご覧ください。 

