---
title: vCenter の外部 ID ソースを構成する
description: vCenter のために、LDAP または LDAPS を介して、Active Directory を外部 ID ソースとして構成する方法について説明します。
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 37478b65572fba2815121b0712d1a0f2e43376dc
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506298"
---
# <a name="configure-external-identity-source-for-vcenter"></a>vCenter の外部 ID ソースを構成する



[!INCLUDE [vcenter-access-identity-description](includes/vcenter-access-identity-description.md)]

>[!NOTE]
>実行コマンドは、送信された順序で一度に 1 つずつ実行されます。

この方法ガイドでは、以下を行う方法について説明します。

> [!div class="checklist"]
> * vCenter SSO と統合された既存の外部 ID ソースをすべて一覧表示する
> * SSL を使用して、または使用せずに、LDAP を介して Active Directory を追加する 
> * 既存の AD グループを cloudadmin グループに追加する
> * cloudadmin ロールから AD グループを削除する
> * 既存の外部 ID ソースを削除する



## <a name="prerequisites"></a>前提条件

- オンプレミス ネットワークからお使いのプライベート クラウドへの接続を確立します。

- SSL を介して AD を使用している場合は、AD 認証用の証明書をダウンロードし、それを BLOB ストレージとして Azure Storage アカウントにアップロードします。 次に、[Shared Access Signatures (SAS) を使用して Azure Storage リソースへのアクセスを付与する](../storage/common/storage-sas-overview.md)必要があります。  

- FQDN を使用する場合は、オンプレミスの AD で DNS 解決を有効にします。

 

## <a name="list-external-identity"></a>外部 ID を一覧表示する



`Get-ExternalIdentitySources` コマンドレットを実行して、vCenter SSO に既に統合されているすべての外部 ID ソースを一覧表示します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[コマンドの実行]**  >  **[パッケージ]**  >  **[Get-ExternalIdentitySources]** を選択します。

   :::image type="content" source="media/run-command/run-command-overview.png" alt-text="使用可能な実行コマンドにアクセスする方法を示すスクリーンショット。" lightbox="media/run-command/run-command-overview.png":::

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   :::image type="content" source="media/run-command/run-command-get-external-identity-sources.png" alt-text="外部 ID ソースを一覧表示する方法を示すスクリーンショット。":::
   
   | **フィールド** | **Value** |
   | --- | --- |
   | **最大保有期間**  |コマンドレット出力の保有期間。 既定値は 60 日です。   |
   | **実行の名前を指定**  | 英数字の名前。**getExternalIdentity** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** または **[Run Execution Status]\(実行の実行状態\)** ウィンドウをチェックします。


## <a name="add-active-directory-over-ldap-with-ssl"></a>SSL を使用する LDAP を介して Active Directory を追加する

`New-AvsLDAPSIdentitySource` コマンドレットを実行して、vCenter への SSO と共に使用する外部 ID ソースとして、SSL を使用する LDAP を介して AD を追加します。 

1. AD 認証用の証明書をダウンロードし、それを BLOB ストレージとして Azure Storage アカウントにアップロードします。 複数の証明書が必要な場合は、各証明書を個別にアップロードします。  

1. 各証明書について、[Shared Access Signatures (SAS) を使用して Azure Storage リソースへのアクセスを付与する](../storage/common/storage-sas-overview.md)必要があります。 これらの SAS 文字列は、パラメーターとして コマンドレットに提供されます。 

   >[!IMPORTANT]
   >このページを離れると、SAS 文字列を表示できなくなるため、各 SAS 文字列を必ずコピーしておいてください。  
   
1. **[コマンドの実行]**  >  **[パッケージ]**  >  **[New-AvsLDAPSIdentitySource]** を選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **名前**  | 外部 ID ソースのユーザー フレンドリ名。**avslap.local** などです。  |
   | **DomainName**  | ドメインの FQDN。   |
   | **DomainAlias**  | Active Directory ID ソースの場合は、ドメインの NetBIOS 名です。 SSPI 認証を使用している場合は、AD ドメインの NetBIOS 名を ID ソースのエイリアスとして追加します。     |
   | **PrimaryUrl**  | 外部 ID ソースのプライマリ URL。**ldap://yourserver:636** などです。  |
   | **SecondaryURL**  | プライマリの障害が発生した場合のセカンダリ フォールバック URL。  |
   | **BaseDNUsers**  |  有効なユーザーを探す場所。**CN=users,DC=yourserver,DC=internal** などです。  LDAP 認証を使用するにはベース DN が必要です。  |
   | **BaseDNGroups**  | グループを探す場所。**CN=group1, DC=yourserver,DC= internal** などです。 LDAP 認証を使用するにはベース DN が必要です。  |
   | **資格情報**  | AD ソースでの認証に使用される (cloudadmin ではない) ユーザー名とパスワード。  |
   | **CertificateSAS** | AD ソースに対する認証用の証明書が含まれる SAS 文字列へのパス。 複数の証明書を使用する場合は、各 SAS 文字列をコンマで区切ります。 たとえば、**pathtocert1,pathtocert2** のように指定します。  |
   | **GroupName**  | cloudadmin アクセス権を与える外部 ID ソース内のグループ。 たとえば、**avs-admins** のように指定します。  |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 日です。   |
   | **実行の名前を指定**  | 英数字の名前。**addexternalIdentity** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** または **[Run Execution Status]\(実行の実行状態\)** ウィンドウをチェックします。



## <a name="add-active-directory-over-ldap"></a>LDAP を介して Active Directory を追加する

>[!NOTE]
>この方法は推奨されません。 代わりに、[SSL を使用する LDAP を介して Active Directory を追加する](#add-active-directory-over-ldap-with-ssl)方法を使用してください。

`New-AvsLDAPIdentitySource` コマンドレットを実行して、vCenter への SSO と共に使用する外部 ID ソースとして、LDAP を介して AD を追加します。 

1. **[コマンドの実行]**  >  **[パッケージ]**  >  **[New-AvsLDAPIdentitySource]** を選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。
   
   | **フィールド** | **Value** |
   | --- | --- |
   | **名前**  | 外部 ID ソースのユーザー フレンドリ名。**avslap.local** などです。  |
   | **DomainName**  | ドメインの FQDN。    |
   | **DomainAlias**  | Active Directory ID ソースの場合は、ドメインの NetBIOS 名です。 SSPI 認証を使用している場合は、AD ドメインの NetBIOS 名を ID ソースのエイリアスとして追加します。      |
   | **PrimaryUrl**  | 外部 ID ソースのプライマリ URL。**ldap://yourserver:389** などです。  |
   | **SecondaryURL**  | プライマリの障害が発生した場合のセカンダリ フォールバック URL。  |
   | **BaseDNUsers**  |  有効なユーザーを探す場所。**CN=users,DC=yourserver,DC=internal** などです。  LDAP 認証を使用するにはベース DN が必要です。  |
   | **BaseDNGroups**  | グループを探す場所。**CN=group1, DC=yourserver,DC= internal** などです。 LDAP 認証を使用するにはベース DN が必要です。  |
   | **資格情報**  | AD ソースでの認証に使用される (cloudadmin ではない) ユーザー名とパスワード。  |
   | **GroupName**  | 外部 ID ソースでクラウド管理者アクセスを付与するグループ。**avs-admins** などです。  |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 日です。   |
   | **実行の名前を指定**  | 英数字の名前。**addexternalIdentity** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** または **[Run Execution Status]\(実行の実行状態\)** ウィンドウをチェックします。


## <a name="add-existing-ad-group-to-cloudadmin-group"></a>既存の AD グループを cloudadmin グループに追加する

`Add-GroupToCloudAdmins` コマンドレットを実行して、cloudadmin グループに既存の AD グループを追加します。 このグループのユーザーは、vCenter SSO で定義されている cloudadmin (cloudadmin@vsphere.local) ロールと同じ特権を持っています。

1. **[コマンドの実行]**  >  **[パッケージ]**  >  **[Add-GroupToCloudAdmins]** を選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **GroupName**  | 追加するグループの名前。**VcAdminGroup** などです。  |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 日です。   |
   | **実行の名前を指定**  | 英数字の名前。**addADgroup** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** または **[Run Execution Status]\(実行の実行状態\)** ウィンドウをチェックします。



## <a name="remove-ad-group-from-the-cloudadmin-role"></a>cloudadmin ロールから AD グループを削除する

`Remove-GroupFromCloudAdmins` コマンドレットを実行して、指定した AD グループを cloudadmin ロールから削除します。 

1. **[コマンドの実行]**  >  **[パッケージ]**  >  **[Remove-GroupFromCloudAdmins]** を選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **GroupName**  | 削除するグループの名前。**VcAdminGroup** などです。  |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 日です。   |
   | **実行の名前を指定**  | 英数字の名前。**removeADgroup** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** または **[Run Execution Status]\(実行の実行状態\)** ウィンドウをチェックします。






## <a name="remove-existing-external-identity-sources"></a>既存の外部 ID ソースを削除する

`Remove-ExternalIdentitySources` コマンドレットを実行して、既存のすべての外部 ID ソースを一括で削除します。 

1. **[コマンドの実行]**  >  **[パッケージ]**  >  **[Remove-ExternalIdentitySources]** を選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 日です。   |
   | **実行の名前を指定**  | 英数字の名前。**remove_externalIdentity** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** または **[Run Execution Status]\(実行の実行状態\)** ウィンドウをチェックします。


## <a name="next-steps"></a>次のステップ

これで LDAP と LDAPS を構成する方法を学びました。以下に関する詳細を確認することができます。

- [ストレージ ポリシーを構成する方法](configure-storage-policy.md) - vSAN データストアにデプロイされた各 VM には、少なくとも 1 つの VM ストレージ ポリシーが割り当てられます。 VM ストレージ ポリシーは、VM の初期デプロイで割り当てることも、複製や移行などの他の VM 操作を実行するときに割り当てることもできます。

- [Azure VMware Solution の ID の概念](concepts-identity.md) - vCenter は仮想マシン (VM) のワークロードを管理するために使用し、NSX-T Manager はプライベート クラウドの管理と拡張を行うために使用します。 アクセスと ID 管理では、vCenter の場合は CloudAdmin ロールが、NSX-T Manager の場合は制限付き管理者権限が使用されます。 

 
