---
title: Azure NetApp Files ボリュームのネットワーク機能を構成する | Microsoft Docs
description: ネットワーク機能のオプションと、ボリュームのネットワーク機能オプションを構成する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/04/2021
ms.custom: references_regions
ms.author: b-juche
ms.openlocfilehash: 6350084ef916132d7b4d77da00853836daeabccd
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539002"
---
# <a name="configure-network-features-for-an-azure-netapp-files-volume"></a>Azure NetApp Files ボリュームのネットワーク機能を構成する

**ネットワーク機能** の機能は、パブリック プレビューで使用できます。  この機能で、Azure NetApp Files ボリュームに VNet 機能を使用するかどうかを指定できます。 この機能を使用して、**_[標準]_ *_ または _* _[基本]_** にオプションを設定できます。 この設定は、新しい NFS、SMB、またはデュアルプロトコル ボリュームを作成するときに指定できます。 ネットワーク機能の詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md)」を参照してください。

この記事では、オプションをわかりやすく説明し、ネットワーク機能を構成する方法を示します。

## <a name="options-for-network-features"></a>ネットワーク機能のオプション 

ネットワーク機能には、次の 2 つの設定を使用できます。 

* ***Standard***  
    この設定により、ボリュームの VNet 機能が有効になります。  

    [ネットワーク セキュリティグループ](../virtual-network/network-security-groups-overview.md)、[ユーザー定義のルート](../virtual-network/virtual-networks-udr-overview.md#user-defined)、追加の接続パターンなど、より高い IP 制限または VNet 機能が必要な場合は、 **[ネットワーク機能]** を *[標準]* に設定する必要があります。

* ***Basic***  
    この設定では、IP 制限が厳しくなり (<1000)、ボリュームの追加 VNet 機能はありません。

    VNet 機能が必要でない場合は、 **[ネットワーク機能]** を *[基本]* に設定します。  

## <a name="supported-regions"></a>サポートされているリージョン 

現在、ネットワーク機能は、次のリージョンでサポートされています。 

* 米国中北部 

## <a name="considerations"></a>考慮事項

* 設定するネットワーク機能のオプション ( *[標準]* または *[基本]* ) に関係なく、Azure VNet で Azure NetApp ファイルに委任されるサブネットは 1 つだけです。 「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md#considerations)」を参照してください。 
 
* 現時点では、新しいボリュームの作成プロセスでのみ、ネットワーク機能の設定を指定できます。 既存のボリュームの設定を変更することはできません。 

* 標準ネットワーク機能でボリュームを作成できるのは、対応する [Azure リージョンが標準ボリューム機能をサポートしている](#supported-regions)場合のみです。 
    * 標準ボリューム機能がリージョンでサポートされている場合、[ボリュームの作成] ページの [ネットワーク機能] フィールドの既定値は *[標準]* です。 この設定は、 *[基本]* に変更できます。 
    * 標準ボリューム機能がリージョンで使用可能でない場合、[ボリュームの作成] ページの [ネットワーク機能] フィールドの既定値は *[基本]* です。

* 目的の種類のネットワーク機能と互換性のあるストレージを見つける機能は、指定された VNet によって異なります。  リソースが不足しているためにボリュームを作成できない場合は、互換性のある記憶域が使用可能な別の VNet を試すことができます。
  
## <a name="register-the-feature"></a>機能を登録する 

ネットワーク機能は、現在パブリックプレビューの段階にあります。 この機能を初めて使用する場合は、まず機能を登録する必要があります。

1.  次のコマンドを使用して、この機能を登録します。

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

2. 機能の登録の状態を確認します。 

    > [!NOTE]
    > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

また、[Azure CLI のコマンド](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

## <a name="set-the-network-features-option"></a>[ネットワーク機能] オプションの設定

このセクションでは、[ネットワーク機能] オプションを設定する方法を説明します。 

1. 新しい [NFS](azure-netapp-files-create-volumes.md)、[SMB](azure-netapp-files-create-volumes-smb.md)、または [デュアルプロトコル](create-volumes-dual-protocol.md) ボリュームを作成するプロセスでは、[ボリュームの作成] 画面の [基本] タブで **[ネットワーク機能]** オプションを **[基本]** または  **[標準]** に設定できます。

    次のスクリーンショットは、標準ネットワーク機能をサポートするリージョンのボリューム作成例を示しています。 

    ![標準ネットワーク機能のボリューム作成を示すスクリーンショット。](../media/azure-netapp-files/network-features-create-standard.png)

    次のスクリーンショットは、標準ネットワーク機能をサポート *しない* リージョンのボリューム作成例を示しています。 

    ![基本ネットワーク機能のボリューム作成を示すスクリーンショット。](../media/azure-netapp-files/network-features-create-basic.png)

2. ボリュームの作成プロセスを完了する前に、[ボリュームの作成] 画面の **[確認と作成]** タブに、指定したネットワーク機能の設定を表示できます。 **[作成]** をクリックしてボリュームの作成を完了します。

    ![ボリューム作成の [確認と作成] タブを示すスクリーンショット。](../media/azure-netapp-files/network-features-review-create-tab.png)

3. **[ボリューム]** をクリックすると、各ボリュームのネットワーク機能の設定を表示できます。

    [ ![ネットワーク機能の設定を表示した [ボリューム] ページのスクリーンショット。](../media/azure-netapp-files/network-features-volume-list.png)](../media/azure-netapp-files/network-features-volume-list.png#lightbox)

## <a name="next-steps"></a>次のステップ  

* <bpt id="p1">[</bpt>Azure NetApp Files のネットワーク計画のガイドライン<ept id="p1">](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)</ept>
* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files のデュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md) 