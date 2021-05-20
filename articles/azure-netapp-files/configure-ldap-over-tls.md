---
title: Azure NetApp Files 用に ADDS LDAP over TLS を構成する | Microsoft Docs
description: ルート CA 証明書管理など、Azure NetApp Files 用に ADDS LDAP over TLS を構成する方法について説明します。
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
ms.date: 05/05/2021
ms.author: b-juche
ms.openlocfilehash: e9a1b9148948319ebf05e2bcc9f5f306b2a21ec0
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108796017"
---
# <a name="configure-adds-ldap-over-tls-for-azure-netapp-files"></a>Azure NetApp Files 用に ADDS LDAP over TLS を構成する

LDAP over TLS を使用すれば、Azure NetApp Files ボリュームと Active Directory LDAP サーバーの間の通信をセキュリティで保護できます。  Azure NetApp Files の NFS、SMB、およびデュアルプロトコルのボリュームに対して LDAP over TLS を有効にすることができます。  

## <a name="considerations"></a>考慮事項

* Azure Active Directory Domain Services (AADDS) を使用している場合は、TLS 経由の LDAP を有効にしないでください。 AADDS では、LDAP over TLS (ポート 389) ではなく、LDAPS (ポート 636) を使用して、LDAP トラフィックのセキュリティが保護されます。  

## <a name="register-the-ldap-over-tls-feature"></a>LDAP over TLS 機能を登録する 

LDAP over TLS 機能は、現在プレビューの段階にあります。 この機能を初めて使用する場合は、使用する前にまず機能を登録する必要があります。

1.  機能を登録します。

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapOverTls
    ```

2. 機能の登録の状態を確認します。 

    > [!NOTE]
    > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapOverTls
    ```
また、[Azure CLI のコマンド](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

## <a name="generate-and-export-root-ca-certificate"></a>ルート CA 証明書を生成してエクスポートする 

ルート CA 証明書をお持ちではない場合は、それを生成し、LDAP over TLS 認証で使用するようにエクスポートする必要があります。 

1. 「[証明機関をインストールする](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority)」に従って、ADDS 証明機関をインストールして構成します。 

2. 「[MMC スナップインを使用して証明書を参照する](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)」に従って、MMC スナップインと証明書マネージャー ツールを使用します。  
    証明書マネージャー スナップインを使用して、ローカル デバイスのルートまたは発行元の証明書を検索します。 証明書管理スナップイン コマンドは、次のいずれかの設定から実行する必要があります。  
    * ドメインに参加し、ルート証明書がインストールされている Windows ベースのクライアント 
    * ルート証明書を含むドメイン内の別のマシン  

3. ルート CA 証明書をエクスポートします。  
    次の例に示すように、ルート CA 証明書は、[個人用] または [信頼されたルート証明機関] ディレクトリからエクスポートできます。   
    ![個人用証明書を示すスクリーンショット](../media/azure-netapp-files/personal-certificates.png)   
    ![信頼されたルート証明機関を示すスクリーンショット](../media/azure-netapp-files/trusted-root-certification-authorities.png)    

    証明書が Base 64 encoded x.509 (.CER) 形式でエクスポートされていることを確認します。 

    ![証明書のエクスポート ウィザード](../media/azure-netapp-files/certificate-export-wizard.png)

## <a name="enable-ldap-over-tls-and-upload-root-ca-certificate"></a>LDAP over TLS を有効にし、ルート CA 証明書をアップロードする 

1. ボリュームに使用される NetApp アカウントに移動し、 **[Active Directory 接続]** をクリックします。 次に、 **[参加]** をクリックして新しい AD 接続を作成するか、 **[編集]** をクリックして既存の AD 接続を編集します。  

2. **[Active Directory に参加します]** ウィンドウまたは **[Active Directory の編集]** ウィンドウが表示されたら、 **[LDAP over TLS]** チェックボックスを選択して、ボリュームに対して LDAP over TLS を有効にします。 次に、 **[サーバー ルート CA 証明書]** をクリックし、[生成されたルート CA 証明書](#generate-and-export-root-ca-certificate)をアップロードして、LDAP over TLS に使用します。  

    ![[LDAP over TLS] オプションを示すスクリーンショット](../media/azure-netapp-files/ldap-over-tls-option.png)

    証明機関名が DNS で解決できることを確認します。 この名前は、証明書の [発行元] または [発行者] フィールドです。  

    ![証明書に関する情報を示すスクリーンショット](../media/azure-netapp-files/certificate-information.png)

無効な証明書をアップロードした場合、既存の AD 構成、SMB ボリューム、または Kerberos ボリュームがあると、次のエラーが発生します。

`Error updating Active Directory settings The LDAP client configuration "ldapUserMappingConfig" for Vservers is an invalid configuration.`

エラー状態を解決するには、LDAP 認証用の Windows Active Directory LDAP サーバーからの要求に応じて、NetApp アカウントに有効なルート CA 証明書をアップロードします。

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files のデュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)

