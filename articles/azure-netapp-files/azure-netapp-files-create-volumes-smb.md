---
title: Azure NetApp Files の SMB ボリュームを作成する | Microsoft Docs
description: この記事では、Azure NetApp Files の SMB3 ボリュームを作成する方法について説明します。 Active Directory の接続と Domain Services に対する要件について説明します。
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
ms.author: b-juche
ms.openlocfilehash: 738e3d6cb3b27180cd8337e09a72fbe3a0639edf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240055"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files の SMB ボリュームを作成する

Azure NetApp Files では、NFS (NFSv3 または NFSv4.1)、SMB3、またはデュアル プロトコル (NFSv3 と SMB、または NFSv4.1 と SMB) を使用したボリュームの作成がサポートされています。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。 

この記事では、SMB3 ボリュームを作成する方法について説明します。 NFS ボリュームについては、[NFS ボリュームの作成](azure-netapp-files-create-volumes.md)に関する記事を参照してください。 デュアルプロトコル ボリュームについては、[デュアルプロトコル ボリュームの作成](create-volumes-dual-protocol.md)に関する記事を参照してください。

## <a name="before-you-begin"></a>開始する前に 

* あらかじめ容量プールを設定しておく必要があります。 「[容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)」を参照してください。     
* サブネットが Azure NetApp Files に委任されている必要があります。 「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」を参照してください。

## <a name="configure-active-directory-connections"></a>Active Directory Domain Services 接続を構成する 

SMB ボリュームを作成する前に Active Directory Domain Services の接続を作成する必要があります。 Azure NetApp ファイルの Active Directory Domain Services 接続を構成していない場合は、「[Active Directory 接続の作成と管理](create-active-directory-connections.md)」に記載されている手順に従ってください。

## <a name="add-an-smb-volume"></a>SMB ボリュームを追加する

1. [容量プール] ブレードから **[ボリューム]** ブレードをクリックします。 

    ![ボリュームに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。  
    [ボリュームの作成] ウィンドウが表示されます。

3. [ボリュームの作成] ウィンドウで **[作成]** をクリックし、[基本] タブで次のフィールドの情報を入力します。   
    * **ボリューム名**      
        作成するボリュームの名前を指定します。   

        ボリューム名は、各容量プール内で一意である必要があります。 3 文字以上になるようにしてください。 名前は英字で始まる必要があります。 使用できるのは、文字、数字、アンダースコア ('_')、ハイフン ('-') のみです。 

        `default` または `bin` をボリューム名として使用することはできません。

    * **容量プール**  
        ボリュームを作成する容量プールを指定します。

    * **[クォータ]**  
        ボリュームに割り当てられる論理ストレージの量を指定します。  

        **[使用可能なクォータ]** フィールドには、選択した容量プール内の未使用の領域のうち、新しいボリュームの作成に使用できる領域の量が示されます。 新しいボリュームのサイズが、使用可能なクォータを超えてはいけません。  

    * **スループット (MiB/秒)**    
        ボリュームが手動 QoS 容量プールで作成されている場合は、ボリュームに必要なスループットを指定します。   

        ボリュームが自動 QoS 容量プールで作成されている場合は、このフィールドに表示される値は (クォータ x サービス レベルのスループット) です。   

    * **Virtual Network**  
        ボリュームへのアクセス元となる Azure 仮想ネットワーク (VNet) を指定します。  

        指定する VNet には、Azure NetApp Files に委任されているサブネットが存在する必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ VNet から、またはボリュームと同じリージョンにある VNet から VNet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[ボリュームの作成] ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * **ネットワーク機能**  
        サポートされているリージョンでは、ボリュームで使用するネットワーク機能を **Basic** または **Standard** から選ぶことができます。 細については、[ボリュームのネットワーク機能を構成する](configure-network-features.md)ことに関するページと、「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md)」を参照してください。

    * 既存のスナップショット ポリシーをボリュームに適用する場合は、 **[詳細セクションの表示]** をクリックして展開し、スナップショットのパスを非表示にするかどうかを指定して、プルダウン メニューでスナップショット ポリシーを選択します。 

        スナップショット ポリシーの作成については、「[スナップショット ポリシーを管理する](snapshots-manage-policy.md)」を参照してください。

        ![詳細セクションの表示](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. **[プロトコル]** をクリックし、次の情報を入力します。  
    * ボリュームのプロトコルの種類として **[SMB]** を選択します。  

    * ドロップダウン リストから **Active Directory** の接続を選択します。  
    
    * ボリュームに対する一意の **共有名** を指定します。 この共有名は、マウント ターゲットを作成するときに使用します。 共有名の要件は次のとおりです。   
        - リージョン内の各サブネットにおいて一意である必要があります。 
        - 英文字で始まる必要があります。
        - 文字、数字、ダッシュ (`-`) だけで構成する必要があります。 
        - 長さが 80 文字以内である必要があります。   
        
    * <a name="smb3-encryption"></a>SMB3 の暗号化を有効にする場合は、 **[SMB3 プロトコルの暗号化を有効にする]** を選択します。   
        この機能により、移動中の SMB3 データの暗号化が有効になります。 SMB3 暗号化を使用していない SMB クライアントは、このボリュームにアクセスできません。  保存データは、この設定に関係なく暗号化されます。  
        詳細については、「[SMB 暗号化](azure-netapp-files-smb-performance.md#smb-encryption)」を参照してください。 

        **SMB3 の暗号化** 機能は現在、プレビューの段階です。 この機能を初めて使用する場合は、使用する前に機能を登録してください。 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        機能の登録の状態を確認します。 

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        また、[Azure CLI のコマンド](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。  
    * <a name="continuous-availability"></a>SMB ボリュームの継続的な可用性を有効にする場合は、 **[継続的可用性を有効にする]** を選択します。    

        > [!IMPORTANT]   
        > SMB の継続的な可用性機能は、現在パブリック プレビュー段階です。 **[[Azure NetApp Files SMB Continuous Availability Shares Public Preview]\(Azure NetApp Files の SMB の継続的な可用性の共有パブリック プレビュー\) 順番待ち送信ページ](https://aka.ms/anfsmbcasharespreviewsignup)** から、機能にアクセスするための順番待ち要求を送信する必要があります。 Azure NetApp Files チームからの正式な確認メールを待ってから、継続的な可用性機能を使用してください。   
        > 
        SQL Server と [FSLogix のユーザー プロファイル コンテナー](../virtual-desktop/create-fslogix-profile-container.md)でのみ継続的可用性を有効にする必要があります。 SMB 継続的可用性共有を SQL Server と FSLogix のユーザー プロファイル コンテナー以外のワークロードに使用することはサポートされて *いません*。 この機能は現在、Windows SQL Server でサポートされています。 Linux SQL Server では現在サポートされていません。 SQL Server のインストールに管理者以外のアカウント (ドメイン) を使用している場合は、必要なセキュリティ特権がアカウントに割り当てられていることを確認してください。 必要なセキュリティ特権 (`SeSecurityPrivilege`) がドメイン アカウントになく、ドメイン レベルで特権を設定できない場合は、Active Directory 接続の **"セキュリティ特権ユーザー"** フィールドを使用して、そのアカウントに特権を付与できます。 「[Active Directory 接続を作成する](create-active-directory-connections.md#create-an-active-directory-connection)」を参照してください。

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![SMB ボリュームの作成の [プロトコル] タブについて説明しているスクリーンショット。](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **[確認および作成]** をクリックし、ボリュームの詳細を確認します。  **[作成]** をクリックして SMB ボリュームを作成します。

    作成したボリュームは [ボリューム] ページに表示されます。 
 
    ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="control-access-to-an-smb-volume"></a>SMB ボリュームへのアクセスを制御する  

SMB ボリュームへのアクセスはアクセス許可によって管理されます。 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS ファイルおよびフォルダーの権限  

Windows SMB クライアントで、オブジェクトのプロパティの **[セキュリティ]** タブを利用し、ファイルまたはフォルダーのアクセス許可を設定できます。
 
![ファイルとフォルダーの権限を設定する](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>次のステップ  

* [Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 用に ADDS LDAP over TLS を構成する](configure-ldap-over-tls.md) 
* [既存の SMB ボリュームで継続的可用性を有効にする](enable-continuous-availability-existing-SMB.md)
* [SMB 暗号化](azure-netapp-files-smb-performance.md#smb-encryption)
* [Azure NetApp Files のボリュームに関するエラーをトラブルシューティングする](troubleshoot-volumes.md)
* [Azure サービスの仮想ネットワーク統合について理解する](../virtual-network/virtual-network-for-azure-services.md)
* [Azure CLI を使用して新しい Active Directory フォレストをインストールする](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
