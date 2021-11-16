---
title: Azure NetApp Files のボリュームに関するエラーをトラブルシューティングする | Microsoft Docs
description: Azure NetApp Files ボリュームに関するトラブルシューティングに役立つエラー メッセージと解決策について説明します。
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
ms.topic: troubleshooting
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: f46b6f0c0648dcc354f170548a4273188c4871e6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219737"
---
# <a name="troubleshoot-volume-errors-for-azure-netapp-files"></a>Azure NetApp Files のボリュームに関するエラーをトラブルシューティングする

この記事では、Azure NetApp Files ボリュームに関するトラブルシューティングに役立つエラー メッセージと解決策について説明します。 

## <a name="errors-for-smb-and-dual-protocol-volumes"></a>SMB とデュアルプロトコル ボリュームのエラー

|     エラー条件    |     解決策    |
|-|-|
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | このエラーは、DNS に到達できないことを示しています。 <br> 次の解決策を検討してください。 <ul><li>ADDS およびこのボリュームが同じリージョンにデプロイされているか確認します。</li> <li>ADDS およびこのボリュームが同じ VNet を使用しているか確認します。 異なる VNet を使用している場合は、これらの VNet が相互にピアリングしていることを確認します。 「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md)」を参照してください。 </li> <li>DNS サーバーにネットワーク セキュリティ グループ (NSG) が適用されている可能性があります。  そのため、トラフィック フローが許可されません。 この場合は、この NSG を、各種ポートに接続するために DNS または AD に対して開きます。 ポート要件については、「[Active Directory 接続の要件](create-active-directory-connections.md#requirements-for-active-directory-connections)」を参照してください。 </li></ul> <br>Azure ADDS についても同じ解決策が適用されます。 Azure ADDS は同じリージョンにデプロイする必要があります。 VNet は同じリージョン内に存在するか、ボリュームで使用される VNet とピアリングしている必要があります。 | 
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>入力したユーザー名が正しいことを確認します。 </li> <li>ユーザーが Administrator グループのメンバーであり、マシンのアカウントを作成する権限が付与されていることを確認します。 </li> <li> Azure ADDS を使用している場合は、ユーザーが Azure AD グループ `Azure AD DC Administrators` のメンバーであることを確認します。 </li></ul> | 
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.x.x.x, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | AD 接続への参加時に入力したパスワードが正しいことを確認します。 |
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | AD 接続への参加時に指定した組織単位パスが正しいことを確認します。 Azure ADDS を使用している場合は、組織単位パスが `OU=AADDC Computers` であることを確認します。 |
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL. Reason: LDAP Error: Local error occurred Details: Error: Machine account creation procedure failed. [nnn] Loaded the preliminary configuration. [nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn] Successfully connected to ip 10.x.x.x, port 389 using [nnn] Entry for host-address: 10.x.x.x not found in the current source: FILES. Ignoring and trying next available source [nnn] Source: DNS unavailable. Entry for host-address:10.x.x.x found in any of the available sources\n*[nnn] FAILURE: Unable to SASL bind to LDAP server using GSSAPI: local error [nnn] Additional info: SASL(-1): generic failure: GSSAPI Error: Unspecified GSS failure. Minor code may provide more information (Cannot determine realm for numeric host address) [nnn] Unable to connect to LDAP (Active Directory) service on contoso.com (Error: Local error) [nnn] Unable to make a connection (LDAP (Active Directory):contosa.com, result: 7643. `  | AD ホスト マシンのポインター (PTR) レコードが DNS サーバーに存在しない可能性があります。 DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンの PTR レコードを追加する必要があります。 <br> たとえば、AD マシンの IP アドレスが `10.x.x.x` であり、AD マシンのホスト名 (`hostname` コマンドを使用して確認されたもの) が `AD1` であり、ドメイン名が `contoso.com` であるとします。  逆引き参照ゾーンに追加される PTR レコードは `10.x.x.x` -> `contoso.com` になります。   | 
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL\". Reason: Kerberos Error: KDC has no support for encryption type Details: Error: Machine account creation procedure failed [nnn]Loaded the preliminary configuration. [nnn]Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn]FAILURE: Could not authenticate as 'contosa.com': KDC has no support for encryption type  (KRB5KDC_ERR_ETYPE_NOSUPP) ` | Active Directory 接続とサービス アカウントの両方で [AES 暗号化](./create-active-directory-connections.md#create-an-active-directory-connection)が有効になっていることを確認します。 |
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `Failed to create the Active Directory machine account \"SMB-NTAP-VOL\". Reason: LDAP Error: Strong authentication is required Details: Error: Machine account creation procedure failed\n [ 338] Loaded the preliminary configuration.\n [ nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ nnn ] Successfully connected to ip 10.x.x.x, port 389 using TCP\n [ 765] Unable to connect to LDAP (Active Directory) service on\n dc51.area51.com (Error: Strong(er) authentication\n required)\n*[ nnn] FAILURE: Unable to make a connection (LDAP (Active\n* Directory):contoso.com), result: 7609\n. "` | LDAP 署名オプションは選択されていませんが、AD クライアントは LDAP 署名を持っています。  [LDAP 署名を有効化](create-active-directory-connections.md#create-an-active-directory-connection)してから、再試行してください。 | 

## <a name="errors-for-dual-protocol-volumes"></a>デュアルプロトコル ボリュームのエラー

|     エラー条件    |     解決策    |
|-|-|
| LDAP over TLS が有効になっていて、デュアルプロトコル ボリュームの作成に失敗し、エラー `This Active Directory has no Server root CA Certificate` が発生します。    |     このエラーがデュアルプロトコル ボリュームの作成中に発生した場合は、お使いの NetApp アカウントでルート CA 証明書が アップロードされていることを確認してください。    |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Failed to validate LDAP configuration, try again after correcting LDAP configuration` が発生します。    |  AD ホスト マシンのポインター (PTR) レコードが DNS サーバーに存在しない可能性があります。 DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンの PTR レコードを追加する必要があります。 <br> たとえば、AD マシンの IP アドレスが `10.x.x.x` であり、AD マシンのホスト名 (`hostname` コマンドを使用して確認されたもの) が `AD1` であり、ドメイン名が `contoso.com` であるとします。  逆引き参照ゾーンに追加される PTR レコードは `10.x.x.x` -> `contoso.com` になります。   |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.x.x.x, port 88 using TCP\\n**[ 950] FAILURE` が発生します。 |     このエラーは、NetApp アカウントで Active Directory に参加するときの AD パスワードが正しくないことを示しています。 正しいパスワードで AD 接続を更新してから、もう一度お試しください。 |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` が発生します。 |   このエラーは、DNS に到達できないことを示しています。 DNS IP が正しくないか、ネットワークに関する問題が発生していることが原因である可能性があります。 AD 接続で入力された DNS IP を調べて、その IP が正しいことを確認します。 <br> また、AD とボリュームが同じリージョンにあり、さらに同じ VNet 内にあることを確認します。 それらが異なる VNet 内にある場合は、必ずその 2 つの VNet 間に VNet ピアリングが確立されているようにします。 <br> 詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md#azure-native-environments)」を参照してください。 |
| デュアルプロトコル ボリュームのマウント時に、アクセス許可が拒否されます。 | デュアルプロトコル ボリュームでは、NFS プロトコルと SMB プロトコルの両方がサポートされます。  UNIX システムのマウントされたボリュームにアクセスしようとすると、システムは使用している UNIX ユーザーを Windows ユーザーにマップしようとします。 マッピングが見つからない場合は、"アクセス許可が拒否されました" というエラーが発生します。 <br> この状況は、アクセスに ‘root’ ユーザーを使用する場合にも当てはまります。 <br> "アクセス許可が拒否されました" の問題を回避するには、マウント ポイントにアクセスする前に、Windows Active Directory に `pcuser` が含まれていることを確認します。 "アクセス許可が拒否されました" の問題が発生した後に `pcuser` を追加する場合は、キャッシュ エントリがクリアされるまで 24 時間待機してからアクセスを再試行してください。 |

## <a name="errors-for-nfsv41-kerberos-volumes"></a>NFSv4.1 Kerberos ボリュームのエラー

|     エラー条件    |     解決策    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files は、NFSv3 ボリュームに対して Kerberos をサポートしていません。 Kerberos は、NFSv4.1 プロトコルでのみサポートされます。  |
|`This NetApp account has no configured Active Directory   connections`  |  **[KDC IP]** および **[AD サーバー名]** の各フィールドを使用して、NetApp アカウントに対して Active Directory を構成します。 手順については、「[Azure portal を構成する](configure-kerberos-encryption.md#configure-the-azure-portal)」を参照してください。 |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files は、プレーン NFSv4.1 ボリュームから Kerberos NFSv4.1 ボリュームへの変換、およびその逆の変換をサポートしていません。 |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  例 : `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> A/PTR レコードが適切に設定され、サーバー名 `smb-test-64d9.contoso.com` の Active Directory に存在することを確認します。 <br> NFS クライアントでは、`smb-test-64d9.contoso.com` の `nslookup` が IP アドレス IP1 (つまり `10.1.1.68`) に解決される場合、IP1 の `nslookup` は 1 つのレコード (つまり、`smb-test-64d9.contoso.com`) にのみ解決される必要があります。 IP1 の `nslookup` が、複数の名前に解決されないようにする "*必要があります*"。 </li>  <li>PowerShell または UI のいずれかを使用して、AD で `NFS-<Smb NETBIOS NAME>-<few random characters>` タイプの NFS マシン アカウントに対して AES-256 を設定します。 <br> コマンドの例: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>NFS クライアント、AD、Azure NetApp Files ストレージ ソフトウェアの時刻が相互に同期され、時刻のずれが 5 分以内であることを確認します。 </li>  <li>`kinit <administrator>` コマンドを使用して、NFS クライアントで Kerberos チケットを取得します。</li> <li>NFS クライアントのホスト名を 15 文字未満に削減し、領域の結合をもう一度実行します。 </li><li>次のように、NFS クライアントと `rpcgssd` サービスを再起動します。 このコマンドは OS によって異なる場合があります。<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu: <br> (`rpc-gssd` サービスを再起動します。) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | この問題は、NFS クライアントの問題に関連している可能性があります。 NFS クライアントを再起動します。    |
|`Hostname lookup failed`   | DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンの PTR レコードを追加する必要があります。 <br> たとえば、AD マシンの IP アドレスが `10.1.1.4` であり、AD マシンのホスト名 (hostname コマンドを使用して確認されたもの) が `AD1` であり、ドメイン名が `contoso.com` であるとします。 逆引き参照ゾーンに追加される PTR レコードは `10.1.1.4 -> AD1.contoso.com` になります。 |
|`Volume creation fails due to unreachable DNS server`  | 2 つの解決策を使用できます。 <br> <ul><li> このエラーは、DNS に到達できないことを示しています。 原因として、DNS IP の誤りやネットワークの問題が考えられます。 AD 接続で入力された DNS IP を調べて、その IP が正しいことを確認します。 </li> <li> AD とボリュームが同じリージョンにあり、さらに同じ VNet 内にあることを確認します。 それらが異なる VNet 内にある場合は、その 2 つの VNet 間に VNet ピアリングが確立されていることを確認します。 </li></ul> |
|NFSv4.1 Kerberos ボリュームの作成は、次の例のようなエラーが原因で失敗します。 <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP が誤っていて、Kerberos ボリュームが作成されています。 正しいアドレスで KDC IP を更新します。 <br> KDC IP を更新しても、エラーは解消されません。 ボリュームを再作成する必要があります。 |

## <a name="errors-for-ldap-volumes"></a>LDAP ボリュームのエラー

|     エラー条件    |     解決策    |
|-|-|
| ldapEnabled を true に設定して SMB ボリュームを作成する際にエラーが発生しました: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | LDAP を有効にして SMB ボリュームを作成することはできません。 <br> LDAP を無効にして SMB ボリュームを作成します。 |
| 既存のボリュームの ldapEnabled パラメーター値を更新中にエラーが発生しました: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  ボリュームの作成後に、LDAP オプションの設定を変更することはできません。 <br> 作成されたボリュームでは、LDAP オプションの設定を更新しないでください。 詳細については、「[NFS ボリューム アクセスに拡張グループで ADDS LDAP を構成する](configure-ldap-extended-groups.md)」を参照してください。 |
| LDAP 対応の NFS ボリュームの作成中にエラーが発生しました: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  このエラーは、DNS にアクセスできないために発生します。 <br> <ul><li> Azure NetApp Files 用に正しいサイト (サイトのスコープ) が構成されているかどうかを確認します。 </li><li> DNS にアクセスできない理由は、DNS IP アドレスが正しくないか、ネットワークの問題である可能性があります。 AD 接続で入力された DNS IP アドレスを調べて、それが正しいことを確認します。 </li><li> AD とボリュームが同じリージョンにあり、さらに同じ VNet 内にあることを確認します。 それらが異なる VNet 内にある場合は、その 2 つの VNet 間に VNet ピアリングが確立されていることを確認します。</li></ul> |
| スナップショットからのボリュームの作成時にエラーが発生しました: <br> `Aggregate does not exist` | Azure NetApp Files では、LDAP が無効になっているボリュームに属するスナップショットから、LDAP が有効になっている新しいボリュームのプロビジョニングがサポートされていません。 <br> 指定されたスナップショットから、LDAP が無効になっている新しいボリュームを作成してみてください。 |

## <a name="errors-for-volume-allocation"></a>ボリューム割り当てのエラー 

Azure NetApp Files で新しいボリュームを作成するか、既存のボリュームのサイズを変更すると、Microsoft Azure により、ストレージとネットワーク リソースがサブスクリプションに割り当てられます。 特定のリージョンでは、Azure サービスの需要がかつてないほど増加しているために、リソース割り当てのエラーが発生する場合があります。

このセクションでは、いくつかの一般的な割り当てエラーの原因を説明し、考えられる対処方法を提案します。

|     エラー条件    |     解決策    |
|-|-|
|新しいボリュームの作成時、または既存のボリュームのサイズ変更時にエラーが発生しました。 <br> エラー メッセージ: `There was a problem locating [or extending] storage  for the volume. Please retry the operation. If the problem persists, contact Support.` | このエラーは、この要求にサービスがリソースを割り当てようとしているときにエラーが発生したことを示します。 <br> しばらくしてから操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。|
|通常のボリュームのリージョンのストレージまたはネットワークの容量の不足。 <br> エラー メッセージ: `There are currently insufficient resources available to create [or extend] a volume in this region. Please retry the operation. If the problem persists, contact Support.` | このエラーは、リージョンで使用可能なリソースが不足しているために、ボリュームの作成またはサイズ変更を行うことができないことを示します。 <br> 次のいずれかの回避策を使用します。 <ul><li>新しい VNet の下にボリュームを作成します。 こうすると、ネットワーク関連のリソース制限の超過が回避されます。</li> <li>しばらくしてからやり直してください。 その間にクラスター、リージョン、またはゾーンでリソースが解放されている可能性があります。</li></ul> |
|ネットワーク機能を `Standard` に設定してボリュームを作成しているときのストレージ容量の不足。 <br> エラー メッセージ: `No storage available with Standard network features, for the provided VNet.` | このエラーは、リージョンで使用可能なリソースが不足しているために、`Standard` ネットワーク機能を使用してボリュームを作成できないことを示します。 <br> 次のいずれかの回避策を使用します。 <ul><li>`Standard` ネットワーク機能が必要でない場合は、`Basic` ネットワーク機能を使用してボリュームを作成します。</li> <li>新しい VNet でボリュームを作成してみてください。 こうすると、ネットワーク関連のリソース制限の超過が回避されます。</li><li>しばらくしてからやり直してください。  その間にクラスター、リージョン、またはゾーンでリソースが解放されている可能性があります。</li></ul> |

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files のデュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md) 
* [ボリュームのネットワーク機能を構成する](configure-network-features.md)
