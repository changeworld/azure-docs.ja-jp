---
title: "Azure Backup Server を使用した VMware サーバーの保護 | Microsoft Docs"
description: "Azure Backup Server を使用して、Azure またはディスクに VMware サーバーをバックアップします。 この記事では、VMware ワークロードを保護します。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/28/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 779841483e72b42725354d85a0f593aee7db8278
ms.lasthandoff: 04/11/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Azure への VMware サーバーのバックアップ

この記事では、VMware サーバーを Azure Backup Server に接続して、VMware サーバーのコンテンツをクラウドにバックアップできるようにする方法について説明します。 Azure Backup Server が既にインストールされていることを前提としています。

## <a name="create-secure-connection-to-vmware-server"></a>VMware サーバーへのセキュリティで保護された接続の作成

VMware サーバーを保護するには、Azure Backup Server から VMware サーバーに安全に接続できる必要があります。 セキュリティで保護された接続を実現するには、VMware サーバーと Azure Backup Server に有効な証明書をインストールします。

VMware サーバーに接続する際に URL が安全でない場合は、サイトへの接続をセキュリティで保護するために、証明書をエクスポートする必要があります。
![VMware サーバーへのセキュリティで保護されていない接続の例](./media/backup-azure-backup-server-vmware/unsecure-url.png)

1. (取り消し線の付いた) https をクリックし、ポップアップ メニューの [詳細] リンクをクリックします。

  ブラウザーによっては、**[設定]** > **[その他のツール]** > **[開発者ツール]** の順にクリックし、[セキュリティ] タブをクリックする必要があります。

  ![セキュリティで保護されていない接続のエラーメッセージの例](./media/backup-azure-backup-server-vmware/security-tab.png)

2. [セキュリティ] タブの詳細情報で、**[証明書の表示]** をクリックします。

  ![セキュリティで保護されていない接続のエラーメッセージの例](./media/backup-azure-backup-server-vmware/security-tab-view-certificate.png)

  [証明書] ダイアログ ボックスが開きます。

3. [証明書] ダイアログ ボックスで、[証明のパス] タブをクリックします。  

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/certificate-certification-path.png)

  この証明書の場合は、発行者が見つからなかったため、強調表示されている証明書は信頼されていません。 他の理由で証明書が信頼されない場合もあります。

4. 証明書をローカル コンピューターにエクスポートするには、[詳細] タブをクリックし、[ファイルにコピー] をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/certificate-details-tab.png)

  証明書のエクスポート ウィザードが開きます。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/certificate-wizard1.png)

  **[次へ]** をクリックして、ウィザードを進めます。

5. [エクスポート ファイルの形式] 画面で、証明書の形式を指定します。 希望の形式がない場合は、証明書の既定のファイル形式をそのまま使用し、**[次へ]** をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/certificate-wizard1b.png)

6. [エクスポートするファイル] 画面で、証明書の名前を入力し、[参照] をクリックして、証明書を格納するローカル コンピューター上の場所を選択します。 見つけることができる場所に証明書を保存します。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/certificate-wizard2.png)

7. 証明書をエクスポートしたら、保存した場所に移動します。証明書を右クリックし、メニューの **[証明書のインストール]** を選択します。

  証明書のインポート ウィザードが開きます。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/cert-import-wizard1.png)

8. 証明書のインポート ウィザードで、証明書のインポート先として **[ローカル コンピューター]** を選択し、**[次へ]** をクリックして、続行します。

9. [証明書ストア] 画面で、**[証明書をすべて次のストアに配置する]** を選択し、**[参照]** をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  [証明書ストアの選択] ダイアログ ボックスが開きます。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/cert-store.png)

  証明書のインポート先フォルダーを選択し、**[OK]** をクリックします。 どのフォルダーを使用すべきかわからない場合は、[信頼されたルート証明機関] を選択します。 選択したインポート先フォルダーが [証明書ストア] ダイアログ ボックスに表示されます。 **[次へ]** をクリックして、証明書をインポートします。

10. [証明書のインポート ウィザードの完了] 画面で、目的のフォルダーに証明書があることを確認し、[完了] をクリックして、ウィザードを完了します。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  インポートに成功したかどうかを通知するダイアログが表示されます。

11. VMware VM にログインして、VMware サーバーへの接続がセキュリティで保護されていることを確認します。 Azure Backup Server は、セキュリティで保護された HTTPS チャネルを介して VMware サーバーに接続します。 組織内で境界をセキュリティで保護しており、HTTPS プロトコルを有効にしたくない場合は、レジストリを使用して、セキュリティで保護された通信を無効にします。 ただし、Azure Backup Server と VMware サーバーに証明書をインストールして、セキュリティで保護された通信を有効にすることをお勧めします。


## <a name="create-role-and-user-account-on-vmware-server"></a>VMware サーバーでのロールとユーザー アカウントの作成

Azure Backup Server は、指定された VMware ユーザーの資格情報を認証することで、リモートの VMware サーバーと通信します。 Azure Backup Server では、すべてのバックアップ操作で VMware ユーザーの資格情報を認証します。 Azure Backup Server が VMware サーバーと安全に通信できるようにするには、Azure Backup Server の運用サーバーの追加ウィザードを使用します。 Azure Backup Server との関係を設定する 3 つのフェーズがあります。 

- 権限が割り当てられたユーザー ロールの作成
- 資格情報 (ユーザー名とパスワード) を持つユーザー アカウントの作成
- Azure Backup Server への VMware サーバー ユーザー アカウントの追加

運用サーバーの追加ウィザードを使用して、これらを行います。 ユーザー名とパスワードの組み合わせは、資格情報として格納されます。


### <a name="create-user-role-and-add-privileges"></a>ユーザー ロールの作成および権限の追加
Azure Backup Server の資格情報で指定する VMware ユーザー アカウントには特定の権限を関連付ける必要があります。 ただし、権限はユーザー ロールに関連付けられます。そのため、最初にユーザー ロールを作成し、次に特定の権限をそのロールに追加します。 ユーザー ロールには、バックアップ管理者の権限を関連付けます。

1. 新しい VMware ユーザー ロールを作成するには、VMware サーバーにログインし、**[Navigator (ナビゲーター)]** パネルの **[Administration (管理)]** をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. 新しいロールを作成するには、**[Administration (管理)]** セクションの **[Roles (ロール)]** を選択し、**[Roles (ロール)]** パネルの [Add role (ロールの追加)] アイコン (+ 記号) をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  **[Create Role (ロールの作成)]** ダイアログ ボックスが開きます。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. **[Create Role (ロールの作成)]** ダイアログ ボックスの **[Role name (ロール名)]** フィールドにロールの名前を入力します。 この例では、*BackupAdminRole* という名前を使用します。 ロール名は任意の名前でかまいませんが、ロールを識別できる名前である必要があります。

4. ユーザー ロールに適用する権限を選択します。 次の一覧から権限を選択します。 権限を選択するときは、親ラベルのシェブロンをクリックして、親を展開し、子権限を表示します。 親権限のすべての子権限を選択する必要はありません。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  - Privilege.Datastore.AllocateSpace.label
  - Privilege.Global.ManageCustomerFields.label
  - privilege.Network.Assign.label
  - Privilege.VirtualMachine.Config.AddNewDisk.label
  - Privilege.VirtualMachine.Config.AdvanceConfig.label
  - Privilege.VirtualMachine.Config.ChangeTracking.label
  - Privilege.VirtualMachine.Config.HostUSBDevice.label
  - Privilege.VirtualMachine.Config.SwapPlacement.label  
  - Privilege.VirtualMachine.Interact.PowerOff.label
  - Privilege.VirtualMachine.Inventory.Create.label
  - Privilege.VirtualMachine.Provisioning.DiskRandomRead.summary
  - Privilege.VirtualMachine.State.CreateSnapshot.label
  - Privilege.VirtualMachine.State.RemoveSnapshot.label
</br>

  権限を選択したら、**[OK]** をクリックします。 [Roles (ロール)] パネルの一覧に新しいロールが表示されます。

### <a name="create-a-user-account-and-assign-permissions"></a>ユーザー アカウントの作成および権限の割り当て

権限を持つユーザー ロールを定義したら、ユーザー アカウントを作成します。 ユーザー アカウントを作成する際に、特定のユーザー ロールを割り当てて、アカウントに権限を関連付けます。 ユーザー アカウントにはパスワードと名前が含まれます。これが認証に使用する資格情報となります。

1. 新しいユーザー アカウントを作成するには、VMware サーバーの [Navigator (ナビゲーター)] ウィンドウで **[Users and Groups (ユーザーとグループ)]** をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  [Users and Groups (ユーザーとグループ)] パネルが表示されます。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. VMware の [Users and Groups (ユーザーとグループ)] パネルの [Users (ユーザー)] タブで [Add users (ユーザーの追加)] アイコン (+ 記号) をクリックします。

  [New User (新しいユーザー)] ダイアログ ボックスが開きます。

3. 作成したユーザー アカウントには、資格情報として使用するユーザー名とパスワードの組み合わせが含まれます。 [New User (新しいユーザー)] ダイアログ ボックスで、フィールドに入力し、**[OK]** をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  一覧に新しいユーザー アカウントが表示されます。

4. ユーザー アカウントを作成したので、(目的のアクセス許可を持つ) ユーザー ロールに関連付けます。 [Navigator (ナビゲーター)] ウィンドウで、**[Global Permissions (グローバル アクセス許可)]** をクリックします。 [Global Permissions (グローバル アクセス許可)] パネルの **[Manage (管理)]** タブをクリックし、[Add (追加)] アイコン (+ 記号) をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  [Global Permissions Root - Add Permission (グローバル アクセス許可のルート - アクセス許可の追加)] ダイアログ ボックスが表示されます。

5. **[Global Permissions Root - Add Permission (グローバル アクセス許可のルート - アクセス許可の追加)]** ダイアログ ボックスで、**[Add (追加)]** をクリックして、ユーザーまたはグループを選択します。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  [Select Users/Groups (ユーザー/グループの選択)] ダイアログ ボックスが開きます。

6. **[Select Users/Groups (ユーザー/グループの選択)]** ダイアログ ボックスで、作成したユーザー アカウントを選択し、**[Add (追加)]** をクリックします。 [Users (ユーザー)] フィールドに選択したユーザー アカウントが表示されます。 ユーザー名は、<*ドメイン*>`\`<*ユーザー名*> という形式で [Users (ユーザー)] フィールドに表示されます。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  **[OK]** をクリックして、選択したユーザーを [Add Permission (アクセス許可の追加)] ダイアログ ボックスに追加します。

7. ユーザーを特定したので、ユーザーをロールに割り当てます。 [Assigned Role (割り当て済みのロール)] 領域のドロップダウン メニューからロールを選択し、**[OK]** をクリックします。

  ![エラーのある [証明書] ダイアログ ボックス ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  [Global Permissions (グローバル アクセス許可)] の [Manage (管理)] タブの一覧に新しいユーザー アカウントと、関連付けられているロールが表示されます。


### <a name="add-the-vmware-user-account-credentials-to-azure-backup-server"></a>Azure Backup Server への VMware ユーザー アカウントの資格情報の追加

VMware サーバーを Azure Backup Server に追加する前に、必ず [Update 1 for Microsoft Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server) をインストールしてください。

1. (サーバーのデスクトップにある) 次のアイコンをクリックして、Azure Backup Server コンソールを開きます。

  ![Azure Backup Server アイコン](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  デスクトップにアイコンが見つからない場合は、インストールされているアプリの一覧から Azure Backup Server を開くことができます。 インストールされているアプリの一覧では、Azure Backup Server アプリは Microsoft Azure Backup という名前になっています。

2. Azure Backup Server コンソールで、**[管理]**、**[運用サーバー]** の順にクリックし、ツール リボンの **[VMware の管理]** をクリックします。

  ![MABS コンソール](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  [資格情報の管理] ダイアログ ボックスが開きます。

  ![MABS の [資格情報の管理] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. [資格情報の管理] ダイアログ ボックスで、**[追加]** をクリックして、[資格情報の追加] ダイアログ ボックスを開きます。

4. [資格情報の追加] ダイアログ ボックスで、新しい資格情報の名前と説明を入力します。 ユーザー名とパスワードは、VMware サーバーでユーザー アカウントを作成するときに使用したものと同じである必要があります。

  ![MABS の [資格情報の管理] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog.png)

  **[追加]** をクリックして、新しい資格情報を Azure Backup Server に追加します。 [資格情報の管理] ダイアログ ボックスの一覧に新しい資格情報が表示されます。
  ![MABS の [資格情報の管理] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. 右上隅にある **[X]** をクリックして、[資格情報の管理] ダイアログ ボックスを閉じます。


## <a name="add-vmware-server-to-azure-backup-server"></a>Azure Backup Server への VMware サーバーの追加

運用サーバーの追加ウィザードを開くには

1. Azure Backup Server コンソールで、**[管理]**、**[運用サーバー]** の順にクリックし、**[追加]** をクリックします。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  運用サーバーの追加ウィザードが開きます。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. [運用サーバーの種類を選択] 画面で、[VMware サーバー] を選択し、**[次へ]** をクリックします。

3. [サーバー名/IP アドレス] で、VMware サーバーの完全修飾ドメイン名 (FQDN) または IP アドレスを指定します。 すべての ESXi サーバーを同じ vCenter で管理している場合は、VMware の名前を入力できます。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. **[SSL ポート]** ダイアログ ボックスで、VMware サーバーと通信するために使用するポートを入力します。 別のポートが必要なことがわかっている場合を除き、既定のポートであるポート 443 を使用します。

5. **[資格情報の指定]** ダイアログ ボックスで、新しい資格情報を作成するか、既存の資格情報を選択することができます。 前のセクションで資格情報を作成しました。 その資格情報を選択します。

  使用できる資格情報がない場合や、新しい資格情報を作成する必要がある場合は、**[新しい資格情報を追加]** をクリックし、新しい資格情報を作成して、**[OK]** をクリックします。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/specify-new-cred.png)

6. **[追加]** をクリックして、VMware サーバーを **[追加された VMware サーバー]** の一覧に追加し、**[次へ]** をクリックして、ウィザードの次の画面に移動します。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. **[タスク]** 画面で、**[追加]** をクリックして、指定した VMware サーバーを Azure Backup Server に追加します。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  VMware サーバーのバックアップはエージェントレスのバックアップであるため、新しいサーバーの追加には数秒かかります。 このセクションの上記の手順を繰り返すことにより、複数の VMware サーバーを Azure Backup Server に追加できます。

VMware サーバーを Azure Backup Server に追加したので、次の手順では保護グループを作成します。 保護グループでは、短期または長期的なリテンション期間のさまざまな詳細を指定し、バックアップ ポリシーを定義および適用します。 バックアップ ポリシーでは、バックアップが実行される時間とバックアップ対象を設定するためのスケジュールです。


## <a name="configure-a-protection-group-to-back-up-vmware-server"></a>VMware サーバーをバックアップする保護グループの構成

以前に System Center Data Protection Manager または Azure Backup Server を使用したことがない場合は、「[ディスク バックアップの計画](https://technet.microsoft.com/library/hh758026.aspx)」を参照して、ハードウェア環境を準備してください。 適切なストレージがあることを確認したら、新しい保護グループの作成ウィザードを使用して、特定の VM を追加します。

1. Azure Backup Server コンソールで **[保護]** をクリックし、ツール リボンの **[新規]** をクリックして、新しい保護グループの作成ウィザードを開きます。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  新しい保護グループの作成ウィザードが開きます。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  **[次へ]** をクリックして、**[保護グループの種類の選択]** 画面に進みます。

2. [保護グループの種類の選択] 画面で、**[サーバー]** を選択し、**[次へ]** をクリックします。

3. [グループ メンバーの選択] 画面に、利用可能なメンバーと選択されているメンバーが表示されます。 保護するメンバーを選択し、**[次へ]** をクリックします。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/server-add-to-selected-members.png)

  メンバーを選択する際に、他のフォルダーや VM を含むフォルダーを選択すると、それらのフォルダーと VM も選択されます。 親フォルダー内のフォルダーと VM を含めることは、フォルダー レベルの保護と呼ばれます。 チェック ボックスをオフにすることで、任意のフォルダーや VM を除外できます。

  VM または VM を含むフォルダーが既に Azure で保護されている場合、その VM をもう一度選択することはできません。 つまり、Azure で保護されている VM は再度保護することができません。これは、1 つの VM に対して復旧ポイントが重複して作成されないようにするためです。 Azure Backup Server で既に保護されているメンバーを確認する場合は、メンバーの上にマウス ポインターを移動します。そうすると、保護サーバーの名前が表示されます。

4. [データの保護方法の選択] 画面で、保護グループの名前を入力します。 次に、**[保護方法]** で、データのバックアップ先を選択します。 短期的な保護の場合、データはディスクにバックアップされます。 長期的な保護の場合、データはクラウド (Azure) にバックアップされます。 **[次へ]** をクリックして、短期的な保護の範囲に進みます。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. [短期的な目標値の指定] 画面の **[保有期間の範囲]** で、*ディスクに保存された*復旧ポイントを保持する日数を指定します。 復旧ポイントを作成する日時を変更する場合は、**[変更]** をクリックします。 短期的な復旧ポイントは、完全バックアップです。 増分バックアップではありません。 短期的な目標値を設定したら、**[次へ]** をクリックします。

  ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. [ディスク割り当ての確認] 画面で、VM のディスク領域を確認し、必要に応じて変更します。 推奨されるディスク割り当ては、前の画面で指定した保有期間の範囲、ワークロードの種類、および (手順 3. で特定された) 保護データのサイズに基づきます。  

  - データ サイズ - 保護グループ内のデータのサイズ。
  - ディスク領域 - 保護グループの推奨ディスク領域の量。 この設定を変更する場合は、どのデータ ソースも拡大するので、予想よりも少し大きめに合計領域を割り当てる必要があります。
  - データを併置 - 併置を有効にすると、保護対象の複数のデータ ソースを 1 つのレプリカと復旧ポイントのボリュームにマップできます。 すべてのワークロードで併置がサポートされているわけではありません。
  - 自動的に拡大 - この設定を有効にした場合、保護グループ内のデータが最初に割り当てたサイズよりも大きくなると、DPM がディスク サイズを 25% 増やそうとします。
  - 記憶域プールの詳細 - 合計や残りのディスク サイズなど、記憶域プールの現在の状態を表示します。

    ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

  完了したら、**[次へ]** をクリックします。

7. [レプリカの作成方法の選択] 画面で、Azure Backup Server で保護されたデータの最初のコピー (レプリカ) を生成する方法を指定します。

  既定値は、**[自動 (ネットワーク経由)]** と **[今すぐ]** です。 既定値を使用する場合は、ピーク時以外を指定することをお勧めします。 **[時間指定]** を選択し、日時を指定します。

  データが大量にある場合や、ネットワークの状態が最適でない場合は、リムーバブル メディアを使用してオフラインでデータをレプリケートすることを検討してください。

  選択が終わったら、**[次へ]** をクリックします。

  ![新しい保護グループの作成ウィザード](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. **[整合性チェック オプション]** 画面で、整合性チェックを自動化する方法とタイミングを選択します。 整合性チェックは、レプリカ データが不整合になったときに実行することや、設定したスケジュールに従って実行することができます。

  自動整合性チェックを構成しない場合は、Azure Backup Server コンソールの [保護] 領域で保護グループを右クリックし、[整合性チェックの実行] を選択することで、いつでもチェックを手動で実行できます。

  **[次へ]** をクリックして、次の画面に進みます。

9. **[オンライン保護するデータの指定]** 画面で、保護するデータ ソースを選択します。 メンバーを個別に選択するか、**[すべて選択]** をクリックしてすべてのメンバーを選択することができます。 メンバーを選択したら、**[次へ]** をクリックします。

  ![新しい保護グループの作成ウィザード](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. **[オンライン バックアップ スケジュールの指定]** 画面で、ディスク バックアップから復旧ポイントを生成するためのスケジュールを指定します。 復旧ポイントは生成されると、Azure の Recovery Services コンテナーに転送されます。 オンライン バックアップ スケジュールを設定したら、**[次へ]** をクリックします。

  ![新しい保護グループの作成ウィザード](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. [オンライン保持ポリシーの指定] 画面で、Azure にバックアップ データを保持する期間を指定します。 ポリシーを定義したら、**[次へ]** をクリックします。

  ![新しい保護グループの作成ウィザード](./media/backup-azure-backup-server-vmware/retention-policy.png)

  Azure にデータを保持できる期間に制限はありません。 Azure に復旧ポイント データを格納する際の唯一の制限は、保護するインスタンスあたりの復旧ポイントの数が 9999 までであることです。 この例では、保護するインスタンスは VMware サーバーです。

12. [概要] 画面で、保護グループの詳細を確認します。 グループ メンバーと設定に注意します。 設定が終わったら、**[グループの作成]** をクリックします。

  ![新しい保護グループの作成ウィザード](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>次のステップ
Azure Backup Server を使用して、VMware ワークロードを保護する場合は、Azure Backup Server を使用した [Microsoft Exchange Server](./backup-azure-exchange-mabs.md)、[Microsoft SharePoint ファーム](./backup-azure-backup-sharepoint-mabs.md)、または [SQL Server](./backup-azure-sql-mabs.md) の保護も参考にしてください。

エージェントの登録、保護グループの構成、およびバックアップ ジョブに関する問題については、「[Azure Backup Server のトラブルシューティング](./backup-azure-mabs-troubleshoot.md)」を参照してください。

