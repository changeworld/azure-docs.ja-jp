---
title: Azure NetApp Files 用のデュアルプロトコル ボリュームを作成する | Microsoft Docs
description: LDAP ユーザー マッピングをサポートするデュアル プロトコル (NFSv3 と SMB、または NFSv4.1 と SMB) を使用するボリュームを作成する方法について説明します。
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
ms.openlocfilehash: 266ff9dd24ac01347f359ea44f46e7950f8b432f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252480"
---
# <a name="create-a-dual-protocol-volume-for-azure-netapp-files"></a>Azure NetApp Files のデュアルプロトコル ボリュームを作成する

Azure NetApp Files では、NFS (NFSv3 または NFSv4.1)、SMB3、またはデュアル プロトコル (NFSv3 と SMB、または NFSv4.1 と SMB) を使用したボリュームの作成がサポートされています。 この記事では、LDAP ユーザー マッピングをサポートするデュアル プロトコルを使用するボリュームを作成する方法について説明します。 

NFS ボリュームを作成するには、[NFS ボリュームの作成](azure-netapp-files-create-volumes.md)に関する記事を参照してください。 SMB ボリュームを作成するには、[SMB ボリュームの作成](azure-netapp-files-create-volumes-smb.md)に関する記事を参照してください。 

## <a name="before-you-begin"></a>開始する前に 

* あらかじめ容量プールを作成しておく必要があります。  
    「[容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)」を参照してください。   
* サブネットが Azure NetApp Files に委任されている必要があります。  
    「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」を参照してください。

## <a name="considerations"></a>考慮事項

* 「[Active Directory 接続の要件](create-active-directory-connections.md#requirements-for-active-directory-connections)」を満たしていることを確認します。 
* Active Directory (AD) に `pcuser` アカウントを作成し、そのアカウントが有効になっていることを確認します。 このアカウントは、既定のユーザーとして機能します。 これは、NTFS セキュリティ スタイルで構成されたデュアルプロトコル ボリュームにアクセスするための UNIX ユーザーのマッピングに使用されます。 `pcuser` アカウントは、AD にユーザーが存在しない場合にのみ使用されます。 ユーザーが AD に POSIX 属性が設定されたアカウントを持っている場合、そのアカウントが認証に使用され、`pcuser` アカウントにはマップされません。 
* DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンのポインター (PTR) レコードを追加します。 そうしないと、デュアルプロトコル ボリュームの作成は失敗します。
* Active Directory 接続の **[LDAP を使用するローカル NFS ユーザーを許可する]** オプションは、ローカル ユーザーに不定期および一時的なアクセスを提供することを目的としています。 このオプションを有効にすると、LDAP サーバーからのユーザー認証と検索が機能しなくなります。 そのため、Active Directory 接続ではこのオプションを "*無効*" にしておく必要があります。ただし、ローカル ユーザーが LDAP が有効になっているボリュームにアクセスする必要がある場合は除きます。 その場合は、ボリュームにローカル ユーザー アクセスが不要になったら、すぐにこのオプションを無効にする必要があります。 ローカル ユーザー アクセスの管理については、「[LDAP を使用するローカル NFS ユーザーにデュアルプロトコル ボリュームへのアクセスを許可する](#allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume)」を参照してください。
* NFS クライアントが最新であり、オペレーティング システムの最新の更新プログラムが実行されていることを確認します。
* デュアルプロトコル ボリュームでは、Active Directory Domain Services (ADDS) と Azure Active Directory Domain Services (AADDS) の両方がサポートされています。 
* デュアルプロトコル ボリュームでは、AADDS での TLS 経由の LDAP の使用はサポートされていません。 [TLS 経由の LDAP に関する考慮事項](configure-ldap-over-tls.md#considerations)に関するセクションを参照してください。
* デュアルプロトコル ボリュームで使用できる NFS のバージョンは、NFSv3 または NFSv4.1 です。 次の考慮事項が適用されます。
    * デュアル プロトコルは、NFS クライアントからの Windows ACL 拡張属性 `set/get` をサポートしていません。
    * NFS クライアントは、NTFS セキュリティ スタイルのアクセス許可を変更することはできません。また、Windows クライアントは、UNIX 形式のデュアル プロトコル ボリュームのアクセス許可を変更することはできません。   

        次の表は、セキュリティ スタイルとその効果について説明しています。  
        
        | セキュリティ スタイル    | アクセス許可を変更できるクライアント   | クライアントが使用できるアクセス許可  | 結果の有効なセキュリティ スタイル    | ファイルにアクセスできるクライアント     |
        |-  |-  |-  |-  |-  |
        | `Unix`    | NFS   | NFSv3 または NFSv4.1 のモード ビット    | UNIX  | NFS と Windows   |
        | `Ntfs`    | Windows   | NTFS ACL     | NTFS  |NFS と Windows|

    * 名前マッピングが発生する方向 (Windows から UNIX、または UNIX から Windows) は、使用されるプロトコルと、ボリュームに適用されるセキュリティ スタイルによって異なります。 Windows クライアントには、常に Windows から UNIX への名前マッピングが必要です。 ユーザーがレビュー アクセス許可に適用されるかどうかは、セキュリティ スタイルによって異なります。 逆に、NFS クライアントでは、NTFS セキュリティ スタイルが使用されている場合にのみ、UNIX から Windows への名前マッピングを使用する必要があります。 

        次の表は、名前マッピングとセキュリティ スタイルについて説明しています。  
    
        |     Protocol          |     セキュリティ スタイル          |     名前マッピングの方向          |     適用されるアクセス許可          |
        |-|-|-|-|
        |  SMB  |  `Unix`  |  Windows から UNIX  |  UNIX (モード ビットまたは NFSv4.x ACL)  |
        |  SMB  |  `Ntfs`  |  Windows から UNIX  |  NTFS ACL (Windows SID アクセス共有に基づく)  |
        |  NFSv3  |  `Unix`  |  なし  |  UNIX (モード ビットまたは NFSv4.x ACL) <br><br>  NFSv4.x ACL は、NFSv4.x 管理クライアントを使用して適用でき、NFSv3 クライアントによって受け入れられる点に注意してください。  |
        |  NFS  |  `Ntfs`  |  UNIX から Windows  |  NTFS ACL (マップされた Windows ユーザー SID に基づく)  |

* 大規模なトポロジがあり、デュアル プロトコル ボリュームを使用した `Unix` セキュリティ スタイル、または拡張グループを使用した LDAP を使用する場合、Azure NetApp Files がトポロジ内のすべてのサーバーにはアクセスできなくなる可能性があります。  この状況が発生した場合は、アカウント チームに問い合わせて支援を受けてください。  <!-- NFSAAS-15123 --> 
* デュアルプロトコル ボリュームを作成するために、サーバー ルート CA 証明書は必要ありません。 これは、LDAP over TLS が有効になっている場合にのみ必要です。

## <a name="create-a-dual-protocol-volume"></a>デュアルプロトコル ボリュームを作成する

1.  [容量プール] ブレードから **[ボリューム]** ブレードをクリックします。 **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。 

    ![ボリュームに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  [ボリュームの作成] ウィンドウで **[作成]** をクリックし、[基本] タブで次のフィールドの情報を入力します。   
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

        指定する Vnet には、Azure NetApp Files に委任されているサブネットがある必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ Vnet から、またはボリュームと同じリージョンにある Vnet から Vnet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[Create a Volume]\(ボリュームの作成) ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * **ネットワーク機能**  
        サポートされているリージョンでは、ボリュームで使用するネットワーク機能を **Basic** または **Standard** から選ぶことができます。 細については、[ボリュームのネットワーク機能を構成する](configure-network-features.md)ことに関するページと、「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md)」を参照してください。

    * 既存のスナップショット ポリシーをボリュームに適用する場合は、 **[詳細セクションの表示]** をクリックして展開し、スナップショットのパスを非表示にするかどうかを指定して、プルダウン メニューでスナップショット ポリシーを選択します。 

        スナップショット ポリシーの作成については、「[スナップショット ポリシーを管理する](snapshots-manage-policy.md)」を参照してください。

        ![詳細セクションの表示](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **[プロトコル]** タブをクリックして、次の操作を実行します。  
    * ボリューム用のプロトコルの種類として、 **[Dual-protocol]\(デュアルプロトコル\)** を選択します。   

    * 使用する **Active Directory** 接続を指定します。

    * 一意の **ボリューム パス** を指定します。 このパスは、マウント ターゲットを作成するときに使用されます。 パスの要件は、次のとおりです。  

        - リージョン内の各サブネットにおいて一意である必要があります。 
        - 英文字で始まる必要があります。
        - 文字、数字、ダッシュ (`-`) だけで構成する必要があります。 
        - 長さが 80 文字以内である必要があります。

    * デュアルプロトコルに使用する **バージョン** を指定します: **NFSv4.1 と SMB**、または **NFSv3 と SMB**。

        **NFSv4.1 と SMB** のデュアルプロトコルを使用する機能は、現在プレビューの段階です。 この機能を初めて使用する場合は、機能を登録する必要があります。  

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```

        機能の登録の状態を確認します。 

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が **Registered** になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```
        また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

    * 使用する **セキュリティ スタイル** を指定します。NTFS (既定値) または UNIX です。

    * デュアルプロトコル ボリュームに対して SMB3 プロトコル暗号化を有効にする場合は、 **[SMB3 プロトコルの暗号化を有効にする]\(Enable SMB3 Protocol Encryption\)** を選択します。   

        この機能により、移動中の SMB3 データに対してのみ暗号化が有効になります。 NFSv3 の移動中データは暗号化されません。 SMB3 暗号化を使用していない SMB クライアントは、このボリュームにアクセスできません。 保存データは、この設定に関係なく暗号化されます。 詳細については、「[SMB 暗号化](azure-netapp-files-smb-performance.md#smb-encryption)」を参照してください。 

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

    * デュアルプロトコル ボリュームのバージョンで NFSv4.1 と SMB を選択した場合は、ボリュームで **Kerberos** 暗号化を有効にするかどうかを指定します。

        Kerberos の場合は追加の構成が必要です。 [NFSv4.1 Kerberos 暗号化の構成](configure-kerberos-encryption.md)に関する記事の手順に従います。

    *  必要に応じて **[Unix Permissions]\(Unix のアクセス許可\)** をカスタマイズして、マウント パスの変更アクセス許可を指定します。 この設定は、マウント パスの下のファイルには適用されません。 既定の設定は `0770` です。 この既定の設定では、所有者とグループに読み取り、書き込み、実行のアクセス許可が付与されますが、他のユーザーにはアクセス許可は付与されません。     
        **[Unix のアクセス許可]** の設定には、登録の要件と考慮事項が適用されます。 [Unix のアクセス許可の構成と所有権モードの変更](configure-unix-permissions-change-ownership-mode.md)に関する記事の手順に従ってください。  

    * 必要に応じて、[ボリュームのエクスポート ポリシーを構成します](azure-netapp-files-configure-export-policy.md)。

    ![デュアルプロトコルの指定](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. **[確認および作成]** をクリックし、ボリュームの詳細を確認します。 次に、 **[作成]** をクリックして、ボリュームを作成します。

    作成したボリュームは [ボリューム] ページに表示されます。 
 
    ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>LDAP を使用するローカル NFS ユーザーにデュアルプロトコル ボリュームへのアクセスを許可する 

Active Directory 接続で **[LDAP を使用するローカル NFS ユーザーを許可する]** オプションを使用すると、Windows LDAP サーバーに存在しないローカル NFS クライアント ユーザーが、拡張グループが有効になっている LDAP を持つデュアルプロトコル ボリュームにアクセスできます。 

> [!NOTE] 
> このオプションを有効にする前に、[考慮事項](#considerations)を理解する必要があります。   
> **[Allow local NFS users with LDAP]\(LDAP を使用するローカル NFS ユーザーを許可する\)** オプションは、**拡張グループを使用する LDAP** 機能の一部であり、登録が必要です。 詳細については、「[NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する](configure-ldap-extended-groups.md)」を参照してください。

1. **[Active Directory 接続]** をクリックします。  既存の Active Directory 接続で、コンテキスト メニュー (3 つのドット `…`) をクリックし、 **[編集]** を選択します。  

2. 表示される **[Active Directory 設定の編集]** ウィンドウで、 **[LDAP を使用するローカル NFS ユーザーを許可する]** オプションを選択します。  

    ![[LDAP を使用するローカル NFS ユーザーを許可する] オプションを示すスクリーンショット](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX 属性を管理する

Active Directory ユーザーとコンピューター MMC スナップインを使用して、UID、ホーム ディレクトリ、その他の値などの POSIX 属性を管理できます。  次の例は、Active Directory の属性エディターを示しています。 

![Active Directory の属性エディター](../media/azure-netapp-files/active-directory-attribute-editor.png) 

LDAP ユーザーおよび LDAP グループには、次の属性を設定する必要があります。 
* LDAP ユーザーに必要な属性:   
    `uid: Alice`,  
    `uidNumber: 139`,  
    `gidNumber: 555`,  
    `objectClass: user, posixAccount`
* LDAP グループに必要な属性:   
    `objectClass: group, posixGroup`,  
    `gidNumber: 555`
* すべてのユーザーとグループには、それぞれ一意の `uidNumber` と `gidNumber` が必要です。 

`objectClass` に指定された値は、個別のエントリです。 たとえば、複数値の文字列エディターでは、`objectClass` には、次のように LDAP ユーザーとして個別の値 (`user` と `posixAccount`) が指定されます。   

![オブジェクト クラスに指定された複数の値を示している複数値の文字列エディターのスクリーンショット。](../media/azure-netapp-files/multi-valued-string-editor.png) 

Azure Active Directory Domain Services (AADDS) では、組織の AADDC Users OU で作成されたユーザーとグループの POSIX 属性を変更することはできません。 回避策として、カスタム OU を作成し、カスタム OU にユーザーとグループを作成することができます。

Azure AD テナント内のユーザーとグループを AADDC Users OU のユーザーとグループに同期している場合、ユーザーとグループをカスタム OU に移動することはできません。 カスタム OU で作成されたユーザーとグループは、AD テナントに同期されません。 詳細については、[AADDS Custom OU に関する考慮事項と制限事項](../active-directory-domain-services/create-ou.md#custom-ou-considerations-and-limitations)に関する記事を参照してください。

### <a name="access-active-directory-attribute-editor"></a>Active Directory 属性エディターにアクセスする 

Windows システムでは、次のように Active Directory 属性エディターにアクセスできます。  

1. **[スタート]** をクリックし、 **[Windows 管理ツール]** に移動し、 **[Active Directory ユーザーとコンピューター]** をクリックして [Active Directory ユーザーとコンピューター] ウィンドウを開きます。  
2.  表示するドメイン名をクリックし、内容を展開します。  
3.  高度な属性エディターを表示するには、Active Directory ユーザー コンピューターの **[表示]** メニューの **[高度な機能]** オプションを有効にします。   
    ![[高度な機能] メニューで属性エディターにアクセスする方法を示しているスクリーンショット。](../media/azure-netapp-files/attribute-editor-advanced-features.png) 
4. 左側のウィンドウで **[ユーザー]** をダブルクリックして、ユーザーの一覧を表示します。
5. 特定のユーザーをダブルクリックすると、その **[属性エディター]** タブが表示されます。
 
## <a name="configure-the-nfs-client"></a>NFS クライアントを構成する 

NFS クライアントを構成するには、「[Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)」の手順に従います。  

## <a name="next-steps"></a>次の手順  

* [NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)
* [Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)
* [Unix のアクセス許可を構成して所有権モードを変更する](configure-unix-permissions-change-ownership-mode.md) 
* [Azure NetApp Files 用に ADDS LDAP over TLS を構成する](configure-ldap-over-tls.md)
* [NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する](configure-ldap-extended-groups.md)
* [Azure NetApp Files のボリュームに関するエラーをトラブルシューティングする](troubleshoot-volumes.md)
