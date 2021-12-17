---
title: Azure NetApp Files NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する | Microsoft Docs
description: Azure NetApp Files を使用して NFS ボリュームを作成するときに、拡張グループで LDAP を有効にする際の考慮事項と手順について説明します。
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
ms.date: 11/11/2021
ms.author: b-juche
ms.openlocfilehash: 1619ca5932e9be06261a273d640139b1a9c499be
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709806"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する

[NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)ときに、ボリュームの拡張グループ機能 (**LDAP** オプション) を使用して LDAP を有効にするオプションがあります。 この機能により、Active Directory LDAP ユーザーおよび拡張グループ (最大 1024 グループ) がボリューム内のファイルおよびディレクトリにアクセスできるようになります。 NFSv 4.1 と NFSv3 の両方のボリュームで、拡張グループ機能で LDAP を使用できます。 

この記事では、NFS ボリュームの作成時に拡張グループで LDAP を有効にする際の考慮事項と手順について説明します。  

## <a name="considerations"></a>考慮事項

* 拡張グループ機能を使用して LDAP を有効にできるのは、ボリュームの作成時のみです。 この機能は、既存のボリューム上でさかのぼって有効にすることはできません。  

* 拡張グループを使用する LDAP は、Active Directory Domain Services (ADDS) または Azure Active Directory ドメインサービス (AADDS) でのみサポートされます。 OpenLDAP またはその他のサードパーティの LDAP ディレクトリサービスはサポートされていません。 

* Azure Active Directory Domain Services (AADDS) を使用している場合は、TLS 経由の LDAP を有効に *しない* でください。  

* ボリュームを作成した後で、LDAP オプションの設定 (有効または無効) を変更することはできません。  

* 次の表では、LDAP キャッシュの Time to Live (TTL) 設定について説明します。 クライアントを使用してファイルまたはディレクトリにアクセスしようとする前に、キャッシュが更新されるまで待機する必要があります。 そうでないと、アクセスまたはアクセス許可の拒否メッセージがクライアントに表示されます。 

    |     エラー状態    |     解像度    |
    |-|-|
    | キャッシュ |  [Default Timeout]\(既定のタイムアウト\) |
    | グループ メンバーシップの一覧  | 24 時間の TTL  |
    | Unix グループ  | 24 時間の TTL、1 分の負の TTL  |
    | Unix ユーザー  | 24 時間の TTL、1 分の負の TTL  |

    キャッシュには、*Time to Live* と呼ばれる特定のタイムアウト期間があります。 タイムアウト期間が経過すると、古いエントリが残らないようにエントリが期限切れになります。 *負の TTL* の値は、存在しない可能性があるオブジェクトの LDAP クエリに起因するパフォーマンスの問題を回避するために、失敗した参照が存在する場所です。        

## <a name="steps"></a>手順

1. 拡張グループ機能を使用する LDAP は現在プレビューの段階です。 この機能を初めて使用する前に、機能を登録する必要があります。  

    1. 機能を登録します。   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. 機能の登録の状態を確認します。 

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

2. LDAP ボリュームには、LDAP サーバー設定の Active Directory 構成が必要です。 「[Active Directory 接続の要件](create-active-directory-connections.md#requirements-for-active-directory-connections)」と「[Active Directory 接続を作成する](create-active-directory-connections.md#create-an-active-directory-connection)」の手順に従い、Azure portal で Active Directory 接続を構成します。  

    > [!NOTE]
    > Active Directory 接続の設定が構成されていることを確認します。 コンピューター アカウントは、Active Directory の接続の設定で指定された組織単位 (OU) で作成されます。 この設定は、Active Directory を使用して認証するために LDAP クライアントによって使用されます。

3. Active Directory で Active Directory LDAP サーバーが稼働していることを確認します。 

4. LDAP NFS ユーザーは、LDAP サーバー上で特定の POSIX 属性を持っている必要があります。 次のように、LDAP ユーザーと LDAP グループの属性を設定します。 

    * LDAP ユーザーに必要な属性:   
        `uid: Alice`,  
        `uidNumber: 139`,  
        `gidNumber: 555`,  
        `objectClass: user, posixAccount`
    * LDAP グループに必要な属性:   
        `objectClass: group, posixGroup`,  
        `gidNumber: 555`

    `objectClass` に指定された値は、個別のエントリです。 たとえば、複数値の文字列エディターでは、`objectClass` には、次のように LDAP ユーザーとして個別の値 (`user` と `posixAccount`) が指定されます。   

    ![オブジェクト クラスに指定された複数の値を示している複数値の文字列エディターのスクリーンショット。](../media/azure-netapp-files/multi-valued-string-editor.png) 

    Active Directory ユーザーとコンピューター MMC スナップインを使用して、POSIX 属性を管理できます。 次の例は、Active Directory の属性エディターを示しています。 詳細については、[Active Directory 属性エディターへのアクセス](create-volumes-dual-protocol.md#access-active-directory-attribute-editor)に関するセクションを参照してください。  

    ![Active Directory の属性エディター](../media/azure-netapp-files/active-directory-attribute-editor.png) 

5. LDAP 統合 NFSv4.1 Linux クライアントを構成する場合は、「[Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)」をご覧ください。

6. LDAP 対応ボリュームで NFSv4.1 を使用する場合は、「[NFSv4.1 ドメインを構成する](azure-netapp-files-configure-nfsv41-domain.md#configure-nfsv41-domain)」の手順に従って `/etc/idmapd.conf` ファイルを構成します。

    `/etc/idmapd.conf` 内の `Domain` は、NetApp アカウントの Active Directory 接続で構成されているドメインに設定する必要があります。 たとえば、`contoso.com` が NetApp アカウントで構成されているドメインの場合は、`Domain = contoso.com` を設定します。

    その後、ホストで `rpcbind` サービスを再起動するか、ホストを再起動します。 

7.  「[Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)」の手順に従って、NFS ボリュームを作成します。 ボリュームの作成処理中に、 **[プロトコル]** タブで **[LDAP]** オプションを有効にします。   

    ![[LDAP オプションを使用してボリューム ページを作成する] を示すスクリーンショット。](../media/azure-netapp-files/create-nfs-ldap.png)  

8. 省略可能 - Windows LDAP サーバー上に存在しないローカル NFS クライアント ユーザーが、拡張グループが有効になっている LDAP を持つ NFS ボリュームにアクセスできるようにすることができます。 これを行うには、次のように、 **[LDAP を使用するローカル NFS ユーザーを許可する]** オプションを有効にします。
    1. **[Active Directory 接続]** をクリックします。  既存の Active Directory 接続で、コンテキスト メニュー (3 つのドット `…`) をクリックし、 **[編集]** を選択します。  
    2. 表示される **[Active Directory 設定の編集]** ウィンドウで、 **[LDAP を使用するローカル NFS ユーザーを許可する]** オプションを選択します。  

    ![[LDAP を使用するローカル NFS ユーザーを許可する] オプションを示すスクリーンショット](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Active Directory 接続の作成と管理](create-active-directory-connections.md)
* [NFSv4.1 ドメインを構成する](azure-netapp-files-configure-nfsv41-domain.md#configure-nfsv41-domain)
* [Azure NetApp Files のボリュームに関するエラーをトラブルシューティングする](troubleshoot-volumes.md)
