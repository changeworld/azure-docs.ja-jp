---
title: Azure NetApp Files 用に NFS クライアントを構成する | Microsoft Docs
description: Azure NetApp Files で使用するために NFS クライアントを構成する方法について説明します。
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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449652"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Azure NetApp Files 用に NFS クライアントを構成する

この記事で説明する NFS クライアントの構成は、[NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)または[デュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)際のセットアップの一環です。 Azure NetApp Files では、さまざまな Linux ディストリビューションを使用できます。 この記事では、一般的によく使用される RHEL 8 と Ubuntu 18.04 の 2 つの環境の構成について説明します。 

使用する Linux フレーバーに関係なく、次の構成が必要です。
* 時間のずれに関する問題を回避するように NTP クライアントを構成します。
* 名前解決を行うように Linux クライアントの DNS エントリを構成します。  
    この構成には、"A" (前方) レコードと PTR (逆引き) レコードが含まれます。 
* ドメイン参加の場合は、対象の Active Directory にコンピューター アカウントを作成します (これは、realm join コマンドの実行時に作成されます)。 
    > [!NOTE] 
    > 以下のコマンドで使用される `$SERVICEACCOUNT` 変数は、対象となる組織単位でコンピューター アカウントを作成するためのアクセス許可または委任を持つユーザー アカウントである必要があります。
* クライアントが NFS ボリュームやその他の関連する監視ツールをマウントできるようにします。

## <a name="rhel-8-configuration"></a>RHEL 8 の構成 

1. パッケージをインストールします。   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. NTP クライアントを構成します。  
    RHEL 8 では、既定で `chrony` が使用されます。  「[Chrony スイートを使用した NTP の構成](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)」の構成ガイドラインに従います。

3. Active Directory ドメインに参加します。  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu の構成 
このセクションでは、NFS クライアントの Ubuntu の構成について説明します。  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>NFSv4.1 の Kerberos 暗号化を使用している場合 

1. パッケージをインストールします。  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. NTP クライアントを構成します。  
    Ubuntu 18.04 では、既定で `chrony` が使用されます。  「[Ubuntu Bionic: Chrony を使用して NTP を構成する](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)」の構成ガイドラインに従います。

3. Active Directory ドメインに参加します。  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>デュアル プロトコルを使用している場合  

1. 次のコマンドを実行して、インストール済みパッケージをアップグレードします。  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    例:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. 次のコマンドを実行して、サービスを再起動して有効にします。   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

次の例では、LDAP ユーザー `ldapu1` について、Ubuntu LDAP クライアントから AD LDAP サーバーに照会します。   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files のデュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)

