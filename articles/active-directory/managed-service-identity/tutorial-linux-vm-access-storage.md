---
title: Linux VM マネージド ID を使用して Azure Storage にアクセスする
description: Linux VM 上でシステム割り当てマネージド ID を使用して Azure Storage にアクセスするプロセスについて説明するチュートリアル。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: daveba
ms.openlocfilehash: d4daccfdcb2bc11831e960aa20533e32801db946
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049339"
---
# <a name="tutorial-use-a-linux-vms-managed-identity-to-access-azure-storage"></a>チュートリアル: Linux VM マネージド ID を使用して Azure Storage にアクセスする 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


このチュートリアルは、Linux VM マネージド ID を作成し、それを使用して Azure Storage にアクセスする方法を示します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 新しいリソース グループに Linux 仮想マシンを作成する
> * Linux 仮想マシン (VM) でマネージド ID を有効にする
> * ストレージ アカウントに BLOB コンテナーを作成する
> * Linux VM のマネージド ID に Azure Storage コンテナーへのアクセス権を付与します
> * アクセス トークン取得し、それを使用して Azure Storage を呼び出す

> [!NOTE]
> Azure Storage の Azure Active Directory 認証は、パブリック プレビューの段階です。

## <a name="prerequisites"></a>前提条件

まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com)してから先に進んでください。

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

このチュートリアルの CLI スクリプトの例を実行するには、次の 2 つの方法があります。

- Azure Portal から、または各コード ブロックの右上隅にある **[試してみる]** ボタンを使用して、[Azure Cloud Shell](~/articles/cloud-shell/overview.md) を使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョン (2.0.23 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Linux 仮想マシンを作成する

このセクションでは、マネージド ID が後で付与される Linux VM を作成します。

1. Azure Portal の左上隅にある **[新規]** ボタンを選択します。
2. **[コンピューティング]**、**[Ubuntu Server 16.04 LTS]** の順に選択します。
3. 仮想マシンの情報を入力します。 **[認証の種類]** で、**[SSH 公開キー]** または **[パスワード]** を選択します。 作成した資格情報を使用して VM にログインできます。

   ![仮想マシンを作成するための [基本] ウィンドウ](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. **[サブスクリプション]** ボックスの一覧で、仮想マシンのサブスクリプションを選択します。
5. 仮想マシンを作成する新しいリソース グループを選択するには、**[リソース グループ]** > **[新規作成]** を選択します。 終了したら、**[OK]** を選択します。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 [設定] ウィンドウで、既定値をそのままにして **[OK]** を選択します。

## <a name="enable-managed-identity-on-your-vm"></a>VM のマネージド ID を有効にする

仮想マシンのマネージド ID を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 Azure Portal で仮想マシンのマネージド ID を有効にすると、内部では VM が Azure AD に登録されてマネージド ID が作成され、VM で ID が構成されます。

1. 新しい仮想マシンのリソース グループに移動し、前の手順で作成した仮想マシンを選択します。
2. **[設定]** カテゴリで、**[構成]** をクリックします。
3. マネージド ID を有効にするには、**[はい]** を選択します。
4. **[保存]** をクリックして構成を適用します。 

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

このセクションでは、ストレージ アカウントを作成します。 

1. Azure Portal の左上隅にある **[+ リソースの作成]** ボタンをクリックします。
2. **[ストレージ]**、**[ストレージ アカウント - Blob、File、Table、Queue]** の順にクリックします。
3. **[名前]** で、ストレージ アカウントの名前を入力します。  
4. **[デプロイ モデル]** と **[アカウントの種類]** がそれぞれ **[Resource manager]** と **[ストレージ (汎用 v1)]** に設定されている必要があります。 
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。
6. **Create** をクリックしてください。

    ![新しいストレージ アカウントを作成する](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>BLOB コンテナーを作成し、ファイルをストレージ アカウントにアップロードする

ファイルには Blob Storage が必要であるため、ファイルを格納する BLOB コンテナーを作成する必要があります。 次に、新しいストレージ アカウントで、BLOB コンテナーにファイルをアップロードします。

1. 新たに作成したストレージ アカウントに戻ります。
2. **[Blob service]** の **[コンテナー]** をクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックします。
4. **[新しいコンテナー]** で、コンテナーの名前を入力し、**[パブリック アクセス レベル]** で既定値を保持します。

    ![ストレージ コンテナーの作成](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 任意のエディターを使用して、ローカル コンピューターに *hello world.txt* という名前のファイルを作成します。  ファイルを開き、"Hello world! :)" というテキストを (引用符なしで) 追加し、保存します。 

6. 新しく作成したコンテナーにファイルをアップロードします。コンテナー名をクリックしてから **[アップロード]** をクリックします。
7. **[BLOB のアップロード]** ウィンドウの **[ファイル]** で、フォルダー アイコンをクリックし、ローカル コンピューターの **hello_world.txt** を参照してファイルを選択して、**[アップロード]** をクリックします。

    ![テキスト ファイルをアップロードする](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>VM に Azure Storage コンテナーへのアクセスを許可する 

VM のマネージド ID を使用して、Azure Storage Blob のデータを取得できます。   

1. 新たに作成したストレージ アカウントに戻ります。  
2. 左側のパネルの **[アクセス制御 (IAM)]** リンクをクリックします。  
3. ページの上部にある **[+ 追加]** をクリックして、VM 用に新しいロールの割り当てを追加します。
4. **[ロール]** で、ドロップダウンから **[ストレージ BLOB データ閲覧者 (プレビュー)]** を選択します。 
5. 次のドロップダウンで、**[アクセスの割り当て先]** として **[仮想マシン]** を選択します。  
6. 次に、適切なサブスクリプションが **[サブスクリプション]** ドロップダウンにリストされていることを確認してから、**[リソース グループ]** を **[すべてのリソース グループ]** に設定します。  
7. **[選択]** で、VM を選択し、**[保存]** をクリックします。

    ![アクセス許可の割り当て](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>アクセス トークン取得し、それを使用して Azure Storage を呼び出す

Azure Storage は Azure AD 認証をネイティブにサポートするため、マネージド ID を使用して取得したアクセス トークンを直接受け入れることができます。 これは Azure Storage の Azure AD との統合の一部であり、接続文字列に資格情報を提供することとは異なります。

次の手順を完了するには、前に作成した VM から行う必要があり、それに接続するには SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](~/articles/virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. Azure portal で **[Virtual Machines]** にナビゲートして Linux 仮想マシンに移動し、**[概要]** ページの **[接続]** をクリックします。 VM に接続する文字列をコピーします。
2. 任意の SSH クライアントを使用して、VM に**接続**します。 
3. ターミナル ウィンドウで、CURL を使用して、ローカルのマネージド ID エンドポイントに対して Azure Storage のアクセス トークンを取得するよう要求します。
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. このアクセス トークンを使用して Azure Storage にアクセスします。たとえば、コンテナーに事前にアップロードされたサンプル ファイルの内容を読み取るなどです。 `<STORAGE ACCOUNT>`、`<CONTAINER NAME>`、`<FILE NAME>` の値を、以前に指定した値で置き換えます。`<ACCESS TOKEN>` は、前の手順で返されたトークンに置き換えます。

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   応答には、次のようなファイルの内容が含まれています。

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Linux 仮想マシンのマネージド ID を使用して Azure Storage にアクセスする方法を説明しました。  Azure Storage の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
