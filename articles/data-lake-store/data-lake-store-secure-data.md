---
title: Azure Data Lake Store に格納されているデータのセキュリティ保護 | Microsoft Docs
description: グループおよびアクセス制御リストを使用して Azure Data Lake Store 内のデータをセキュリティ保護する方法を説明する
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 0ac6b90f2efc525cfb9767843c741f1e3cfc6de7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449409"
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Azure Data Lake Store に格納されているデータのセキュリティ保護
Azure Data Lake Store のデータをセキュリティで保護するには 3 つの手順が必要です。  ユーザーとセキュリティ グループに対してデータへのアクセスを完全に有効にするには、ロールベースのアクセス制御 (RBAC) とアクセス制御リスト (ACL) の両方を設定する必要があります。

1. まず、Azure Active Directory (AAD) でセキュリティ グループを作成します。 これらのセキュリティ グループを使用して、Azure Portal でロールベースのアクセス制御 (RBAC) を実装します。 詳細については、[Microsoft Azure でのロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。
2. AAD セキュリティ グループを Azure Data Lake Store アカウントに割り当てます。 これにより、ポータルから Data Lake Store アカウントへのアクセス、およびポータルまたは API による管理操作が制御されます。
3. AAD セキュリティ グループをアクセス制御リスト (ACL) として Data Lake Store ファイル システムに割り当てます。
4. さらに、Data Lake Store 内のデータにアクセスできるクライアントの IP アドレスの範囲を設定することもできます。

この記事では、Azure ポータルを使用して上記タスクを実行する方法について説明します。 Data Lake Store でアカウントおよびデータのレベルでどのようにセキュリティが実装されるかについては、「 [Security in Azure Data Lake Store (Azure Data Lake Store のセキュリティ)](data-lake-store-security-overview.md)」を参照してください。 Azure Data Lake Store で ACL がどのように実装されているかについては、[Azure Data Lake Store のアクセス制御の概要](data-lake-store-access-control.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Azure Active Directory でセキュリティ グループを作成する
AAD セキュリティ グループを作成する手順および AAD セキュリティ グループにユーザーを追加する手順については、「 [Azure Active Directory のセキュリティ グループの管理](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

> [!NOTE] 
> Azure Portal を使用すると、Azure AD のグループにユーザーとその他のグループの両方を追加できます。 ただし、サービス プリンシパルをグループに追加するには、[Azure AD の PowerShell モジュール](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)を使用します。
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>ユーザーまたはセキュリティ グループを Azure Data Lake Store アカウントに割り当てる
ユーザーまたはセキュリティ グループを Azure Data Lake Store アカウントに割り当てる場合は、Azure ポータルと Azure リソース マネージャー API を使用して、アカウントに関する管理操作へのアクセスを制御します。 

1. Azure Data Lake Store アカウントを開きます。 左側のウィンドウで **[すべてのリソース]** をクリックし、[すべてのリソース] ブレードで、ユーザーまたはセキュリティ グループを割り当てるアカウント名をクリックします。

2. Data Lake Store アカウントのブレードで、**[アクセス制御 (IAM)]** をクリックします。 ブレードには、所有者としてサブスクリプション所有者が既定で表示されます。
   
    ![セキュリティ グループを Azure Data Lake Store アカウントに割り当てる](./media/data-lake-store-secure-data/adl.select.user.icon.png "セキュリティ グループを Azure Data Lake Store アカウントに割り当てる")

3. **[アクセス制御 (IAM)]** ブレードで、**[追加]** をクリックして **[アクセス許可の追加]** ブレードを開きます。 **[アクセス許可の追加]** ブレードで、ユーザー/グループの**ロール**を選択します。 Azure Active Directory で以前に作成したセキュリティ グループを検索して選択します。 多数のユーザーおよびグループの中から検索する場合は、**[選択]** ボックスを使用してグループ名をフィルター処理します。 
   
    ![ユーザーのロールを追加する](./media/data-lake-store-secure-data/adl.add.user.1.png "ユーザーのロールを追加する")
   
    **[所有者]** ロールと **[共同作成者]** ロールは、Data Lake アカウントでさまざまな管理機能へのアクセスを許可します。 Data Lake 内のデータを操作し、アカウント管理情報も引き続き表示する必要があるユーザーについては、それらのユーザーを **[閲覧者]** ロールに追加できます。 これらのロールの適用範囲は、Azure Data Lake Store アカウントに関連する管理操作に限定されます。
   
    データ操作の場合、ユーザーが実行できる操作はファイル システムの個々のアクセス許可によって定義されます。 そのため、閲覧者ロールを持つユーザーはアカウントに関連付けられた管理設定しか表示できません。しかし、場合によっては、そのユーザーに割り当てられているファイル システムのアクセス許可に基づいて、データの読み取りおよび書き込みができます。 Data Lake Store ファイル システムのアクセス許可については、[セキュリティ グループを ACL として Azure Data Lake Store ファイル システムに割り当てる](#filepermissions)方法に関するページを参照してください。

    > [!IMPORTANT]
    > ファイル システムへのアクセスが自動的に有効になるのは**所有者**ロールだけです。 **共同作成者**、**閲覧者**、および他のすべてのロールでは、ACL でフォルダーとファイルへの任意のアクセス レベルを有効にする必要があります。  **所有者**ロールでは、ACL によってオーバーライドできない、ファイルとフォルダーのスーパー ユーザーのアクセス許可が付与されます。 RBAC ポリシーをデータ アクセスにマップする方法の詳細については、「[RBAC を使用したアカウント管理](data-lake-store-security-overview.md#rbac-for-account-management)」をご覧ください。

4. **[アクセス許可の追加]** ブレードに表示されていないグループ/ユーザーを追加する場合は、**[選択]** ボックスにそのグループ/ユーザーの電子メール アドレスを入力し、一覧からグループ/ユーザーを選択することで招待できます。
   
    ![セキュリティ グループを追加する](./media/data-lake-store-secure-data/adl.add.user.2.png "セキュリティ グループを追加する")
   
5. **[Save]** をクリックします。 次に示すように追加したセキュリティ グループが表示されます。
   
    ![追加されたセキュリティ グループ](./media/data-lake-store-secure-data/adl.add.user.3.png "追加されたセキュリティ グループ")

6. これで、ユーザーまたはセキュリティ グループは、Azure Data Lake Store アカウントにアクセスできるようになりました。 特定のユーザーにアクセス権を付与する場合は、対象ユーザーをセキュリティ グループに追加します。 同様に、ユーザーのアクセス権を取り消す場合は、セキュリティ グループから対象ユーザーを削除します。 アカウントには複数のセキュリティ グループを割り当てることもできます。 

## <a name="filepermissions"></a>ユーザーまたはセキュリティ グループを ACL として Azure Data Lake Store ファイル システムに割り当てる
Azure Data Lake ファイル システムにユーザーまたはセキュリティ グループを割り当てて、Azure Data Lake Store に格納されたデータに対するアクセス制御を設定します。

1. Data Lake Store アカウントのブレードで、 **[データ エクスプローラー]** をクリックします。
   
    ![データ エクスプローラーを使用してデータを表示する](./media/data-lake-store-secure-data/adl.start.data.explorer.png "データ エクスプローラーを使用してデータを表示する")
2. **[データ エクスプローラー]** ブレードで、ACL を構成するフォルダーをクリックし、**[アクセス]** をクリックします。 ACL をファイルに割り当てるには、まず、対象のファイルをクリックしてプレビューし、**[ファイルのプレビュー]** ブレードで **[アクセス]** をクリックする必要があります。
   
    ![Data Lake ファイル システムに ACL を設定する](./media/data-lake-store-secure-data/adl.acl.1.png "Data Lake ファイル システムに ACL を設定する")
3. **[アクセス]** ブレードには、所有者と、既にルートに割り当てられているアクセス許可が表示されます。 **[追加]** アイコンをクリックして、アクセス ACL を追加します。
    > [!IMPORTANT]
    > 1 つのファイルのアクセス許可を設定しても、そのファイルへのアクセス権がユーザー/グループに必ずしも付与されるわけではありません。 割り当てられたユーザー/グループが、そのファイルのパスにアクセスできる必要があります。 詳細と例については、「[アクセス許可に関連する一般的なシナリオ](data-lake-store-access-control.md#common-scenarios-related-to-permissions)」をご覧ください。
   
    ![標準アクセスとカスタム アクセスを一覧表示する](./media/data-lake-store-secure-data/adl.acl.2.png "標準アクセスとカスタム アクセスを一覧表示する")
   
   * **[所有者]** と **[他のすべてのユーザー]** では、UNIX 形式のアクセス権を付与します。この場合、読み取り、書き込み、実行 (rwx) を 3 つのユーザー クラス (所有者、グループ、その他) に指定します。
   * **[割り当て済みのアクセス許可]** は POSIX ACL に対応します。この場合、ファイルの所有者またはグループだけでなく、特定の名前付きユーザーまたはグループにもアクセス許可を設定できます。 
     
     詳細については、 [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)を参照してください。 Azure Data Lake Store で ACL がどのように実装されているかについては、[Azure Data Lake Store のアクセス制御](data-lake-store-access-control.md)に関するページを参照してください。
4. **[追加]** アイコンをクリックして、**[アクセス許可の割り当て]** ブレードを開きます。 このブレードの **[ユーザーまたはグループの選択]** をクリックし、**[ユーザーまたはグループの選択]** ブレードで、Azure Active Directory で以前に作成したセキュリティ グループを検索します。 検索対象のグループが多数存在する場合は、上部にあるテキスト ボックスを使用してグループ名をフィルター処理できます。 追加するグループをクリックして、 **[選択]** をクリックします。
   
    ![グループを追加する](./media/data-lake-store-secure-data/adl.acl.3.png "グループを追加する")
5. **[アクセス許可の選択]** をクリックし、アクセス許可、アクセス許可を再帰的に適用するかどうか、アクセス許可をアクセス ACL、既定の ACL、その両方のどれとして割り当てるかを選択します。 Click **OK**.
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-secure-data/adl.acl.4.png "グループにアクセス許可を割り当てる")
   
    Data Lake Store でのアクセス許可と既定/アクセス ACL の詳細については、「[Data Lake Store のアクセス制御](data-lake-store-access-control.md)」を参照してください。
6. **[アクセス許可の選択]** ブレードで **[OK]** をクリックすると、新しく追加されたグループと、関連付けられたアクセス許可が **[アクセス]** ブレードに表示されます。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-secure-data/adl.acl.5.png "グループにアクセス許可を割り当てる")
   
   > [!IMPORTANT]
   > 現在のリリースでは、**[割り当て済みのアクセス許可]** で設定できるエントリは最大 28 個です。 28 を超えるユーザーを追加する場合は、セキュリティ グループを作成し、セキュリティ グループにユーザーを追加して、それらのセキュリティ グループに Data Lake Store アカウントに対するアクセス権を付与します。
   > 
   > 
7. 必要に応じて、グループを追加した後で、アクセス許可を変更することもできます。 各種類のアクセス許可 (Read、Write、Execute) のチェック ボックスをオフにするかオンにするかは、セキュリティ グループにアクセス許可を割り当てるか、セキュリティ グループからアクセス許可を削除するかに応じて決定します。 **[保存]** をクリックして変更を保存するか、**[破棄]** をクリックして変更を元に戻します。

## <a name="set-ip-address-range-for-data-access"></a>データ アクセス用の IP アドレスの範囲を設定する
Azure Data Lake Store では、データ ストアへのアクセスをネットワーク レベルでさらにロック ダウンできます。 信頼されたクライアントに対して、ファイアウォールを有効にし、IP アドレスを指定し、IP アドレス範囲を定義することができます。 ファイアウォールが有効になると、IP アドレスが定義済みの範囲内にあるクライアントだけがストアに接続できます。

![ファイアウォール設定と IP アクセス](./media/data-lake-store-secure-data/firewall-ip-access.png "ファイアウォール設定と IP アドレス")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントのセキュリティ グループを削除する
Azure Data Lake Store アカウントからセキュリティ グループを削除する場合は、Azure Portal と Azure リソース マネージャー API を使用して、アカウントに関する管理操作へのアクセスを変更するだけです。  

データへのアクセスは変更されず、アクセス ACL によって引き続き管理されます。  この例外は、所有者ロールのユーザー/グループです。  所有者ロールから削除されたユーザー/グループはスーパー ユーザーではなくなり、アクセスはアクセス ACL の設定に戻されます。 

1. Data Lake Store アカウントのブレードで、**[アクセス制御 (IAM)]** をクリックします。 
   
    ![セキュリティ グループを Azure Data Lake アカウントに割り当てる](./media/data-lake-store-secure-data/adl.select.user.icon.png "セキュリティ グループを Azure Data Lake アカウントに割り当てる")
2. **[アクセス制御 (IAM)]** ブレードで、削除するセキュリティ グループをクリックします。 **[削除]** をクリックします。
   
    ![削除されたセキュリティ グループ](./media/data-lake-store-secure-data/adl.remove.group.png "削除されたセキュリティ グループ")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Azure Data Lake Store ファイル システムからセキュリティ グループ ACL を削除する
Azure Data Lake Store ファイル システムからセキュリティ グループの ACL を削除する場合は、Data Lake Store 内のデータへのアクセスを変更します。

1. Data Lake Store アカウントのブレードで、 **[データ エクスプローラー]** をクリックします。
   
    ![Data Lake アカウントにディレクトリを作成する](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Data Lake アカウントにディレクトリを作成する")
2. **[データ エクスプローラー]** ブレードで、ACL を削除するフォルダーをクリックし、**[アクセス]** をクリックします。 ファイルの ACL を削除するには、まず、対象のファイルをクリックしてプレビューし、**[ファイルのプレビュー]** ブレードで **[アクセス]** をクリックする必要があります。 
   
    ![Data Lake ファイル システムに ACL を設定する](./media/data-lake-store-secure-data/adl.acl.1.png "Data Lake ファイル システムに ACL を設定する")
3. **[アクセス]** ブレードで、削除するセキュリティ グループをクリックします。 **[アクセスの詳細]** ブレードで **[削除]** をクリックします。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-secure-data/adl.remove.acl.png "グループにアクセス許可を割り当てる")

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Azure Storage BLOB から Data Lake Store へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
* [PowerShell で Data Lake Store の使用を開始する](data-lake-store-get-started-powershell.md)
* [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store の診断ログへのアクセス](data-lake-store-diagnostic-logs.md)

