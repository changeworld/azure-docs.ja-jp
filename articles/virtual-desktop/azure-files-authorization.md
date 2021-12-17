---
title: Azure Files 用の Azure Virtual Desktop ホスト プールを認可する方法 - Azure
description: Azure Files を使用するために Azure Virtual Desktop ホスト プールを認可する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2593552fbad04ada5e903ba1eb6a2613e3ee55a0
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446688"
---
# <a name="authorize-an-account-for-azure-files"></a>Azure Files 用のアカウントを認可する

この記事では、Azure Files を使用するために Azure Virtual Desktop ホスト プールを認可する方法について説明します。

## <a name="requirements"></a>必要条件

開始する前に、以下のものが必要となります。

- Azure Active Directory (Azure AD) に同期されている Active Directory Domain Services (AD DS) アカウント
- AD DS でグループを作成するためのアクセス許可
- 必要に応じて、ストレージ アカウントと新しいストレージ アカウントの作成に必要なアクセス許可
- アクセス許可を持っている仮想マシン (VM) または AD DS に参加している物理マシン
- すべてのセッション ホストがドメインに参加している Azure Virtual Desktop ホスト プール

## <a name="create-a-security-group-in-active-directory-domain-services"></a>Active Directory Domain Services にセキュリティ グループを作成する

まず、AD DS にセキュリティ グループを作成する必要があります。 このセキュリティ グループは、後の手順で共有レベルと NTFS (New Technology File System) ファイル共有のアクセス許可を付与するために使用されます。

>[!NOTE]
>使用する既存のセキュリティ グループがある場合は、新しいグループを作成する代わりに、そのグループの名前を選択します。

セキュリティ グループを作成するには、次の手順に従います。

1. セキュリティ グループに追加する、AD DS に参加している VM または物理マシンでリモート セッションを開きます。

2. **[Active Directory ユーザーとコンピューター]** を開きます。

3. ドメイン ノードで、マシンの名前を右クリックします。 ドロップダウン メニューで、 **[新規]**  >  **[グループ]** を選択します。

4. **[新しいオブジェクト – グループ]** ウィンドウで、新しいグループの名前を入力し、次の値を選択します。

    - **[グループのスコープ]** で **[グローバル]** を選択します
    - **[グループの種類]** で、 **[セキュリティ]** を選択します

5. 新しいグループを右クリックし、 **[プロパティ]** を選択します。

6. **[プロパティ]** ウィンドウの **[メンバー]** タブを選択します。

7. **[追加]** を選択します。

8. **[ユーザー、連絡先、コンピューター、サービス アカウントまたはグループの選択]** で、 **[オブジェクトの種類]** >  **[コンピューター]** を選択します。 完了したら、 **[OK]** をクリックします。

9. **[選択するオブジェクト名を入力してください]** ウィンドウで、セキュリティ グループに含めるすべてのセッション ホストの名前を入力します。

10. **[名前の確認]** を選択し、表示される一覧から使用するセッション ホストの名前を選択します。

11. **[OK]** を選択し、 **[適用]** を選択します。

>[!NOTE]
>新しいセキュリティ グループを Azure AD と同期するために、最大で 1 時間かかる場合があります。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントをまだ作成していない場合は、最初に「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md)」の指示に従います。 新しいストレージ アカウントを作成する場合は、新しいファイル共有も作成してください。

>[!NOTE]
>**Premium** ストレージ アカウントを作成する場合は、 **[アカウントの種類]** が **[FileStorage]** に設定されていることを確認します。

## <a name="get-rbac-permissions"></a>RBAC のアクセス許可を取得する

RBAC のアクセス許可を取得するには、次の手順に従います。

1. 使用するストレージ アカウントを選択します。

2. **[アクセス制御 (IAM)]** を選択してから、 **[追加]** を選択します。 次に、ドロップダウン メニューから **[ロールの割り当ての追加]** を選択します。

3. **[ロールの割り当ての追加]** 画面で、次の値を選択します。

    - **[ロール]** で、 **[記憶域ファイル データの SMB 共有の共同作成者]** を選択します。
    - **[アクセスの割り当て先]** で、 **[ユーザー、グループ、またはサービス プリンシパル]** を選択します。
    - **[サブスクリプション]** で、 **[環境に基づく]** を選択します。
    - **[選択]** で、セッション ホストを含む Active Directory グループの名前を選択します。

4. **[保存]** を選択します。

## <a name="join-your-storage-account-to-ad-ds"></a>ストレージ アカウントを AD DS に参加させる

次に、ストレージ アカウントを AD DS に参加させる必要があります。 アカウントを AD DS 参加させるには、次の手順に従います。

1. AD DS に参加している VM または物理マシンでリモート セッションを開きます。

      >[!NOTE]
      > Azure AD に同期されているオンプレミスの AD DS 資格情報を使用して、スクリプトを実行します。 オンプレミスの AD DS 資格情報には、ストレージ アカウント所有者または共同作成者の Azure ロールのアクセス許可が必要です。

2. [AzFilesHybrid で最新バージョン](https://github.com/Azure-Samples/azure-files-samples/releases)をダウンロードして解凍します。

3. 管理者特権モードで **PowerShell** を開きます。

4. 次のコマンドレットを実行して、実行ポリシーを設定します。
    
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

5. 次に、AzfileHybrid を解凍したフォルダーに移動し、次のコマンドを実行します。

    ```powershell
    .\\CopyToPSPath.ps1
    ```

6. その後、次のコマンドレットを実行して AzFilesHybrid モジュールをインポートします。
   
   ```powershell
   Import-Module -Name AzFilesHybrid
   ```

7. 次に、次のコマンドレットを実行して Azure AD に接続します。
   
   ```powershell
   Connect-AzAccount
   ```

8. プレースホルダーを実際のシナリオの適切な値に置き換えて、次のパラメーターを設定します。

    ```powershell
    $SubscriptionId = "<your-subscription-id-here>"

    $ResourceGroupName = "<resource-group-name-here>"

    $StorageAccountName = "<storage-account-name-here>"
    ```

9.  最後に次のコマンドを実行します。

    ```powershell
    Join-AzStorageAccountForAuth `

    -ResourceGroupName $ResourceGroupName `

    -StorageAccountName $StorageAccountName `

    -DomainAccountType "ComputerAccount" `

    -OrganizationalUnitDistinguishedName "<ou-here>" `

    -EncryptionType "'RC4','AES256'"
    ```

## <a name="get-ntfs-level-permissions"></a>NTFS レベルのアクセス許可を取得する

Azure Files ストレージ アカウントに対して AD DS コンピューター アカウントで認証するには、先に設定した RBAC アクセス許可に加えて NTFS レベルのアクセス許可も割り当てる必要があります。

NTFS アクセス許可を割り当てるには、次の手順に従います。

1. Azure portal を開いて、AD DS に追加したストレージ アカウントに移動します。

2. **[アクセス キー]** を選択し、その値を **[キー 1]** フィールドにコピーします。

3. AD DS に参加している VM または物理マシンでリモート セッションを開始します。

4. 管理者特権モードでコマンド プロンプトを開きます。

5. プレースホルダーを実際のデプロイの適切な値に置き換えて、次のコマンドを実行します。

    ```cmd
    net use <desired-drive-letter>:
    \\<storage-account-name>.file.core.windows.net\<share-name>
    /user:Azure\<storage-account-name> <storage-account-key>
    ```

    >[!NOTE]
    >このコマンドを実行すると、出力に「コマンドが正常に完了しました」と表示されます。 表示されない場合は、入力を確認してもう一度やり直してください。

6. **エクスプローラー** を開いて、手順 5 のコマンドで使用したドライブ文字を見つけます。

7. ドライブ文字を右クリックし、ドロップダウン メニューから **[プロパティ]**  >  **[セキュリティ]** を選択します。

8. **[編集]** を選択してから、 **[追加]** を選択します。

    >[!NOTE]
    >ドメイン名が AD DS ドメイン名と一致していることを確認します。 一致していない場合は、ストレージ アカウントがドメインに参加していないことを意味します。 続行するには、ドメインに参加しているアカウントを使用する必要があります。

9. メッセージが表示されたら、管理者の資格情報を入力します。

10. **[ユーザー、コンピューター、サービス アカウント、またはグループの選択]** ウィンドウで、[[Active Directory Domain Services にセキュリティ グループを作成する]](#create-a-security-group-in-active-directory-domain-services) からグループの名前を入力します。

11. **[OK]** を選択します。 その後、グループに **[読み取りおよび実行]** アクセス許可があることを確認します。 グループにアクセス許可がある場合は、次の図に示すように、[許可] チェック ボックスがオンになっています。

    > [!div class="mx-imgBorder"]
    > ![セキュリティ] ウィンドウのスクリーンショット。 「アクセス許可」とマークされた一覧で、[読み取りおよび実行] アクセス許可の [許可] 列に緑色のチェック マークが付いています。](media/read-and-execute.png)

12. **[読み取りおよび実行]** アクセス許可を持つコンピューター アカウントがある Active Directory グループをセキュリティ グループに追加します。

13. **[適用]** を選択します。 Windows セキュリティ プロンプトが表示された場合、 **[はい]** を選択して変更を確定します。

## <a name="next-steps"></a>次の手順

セットアップ後に問題が発生した場合は、[Azure Files のトラブルシューティングに関する記事](troubleshoot-authorization.md)を確認してください。