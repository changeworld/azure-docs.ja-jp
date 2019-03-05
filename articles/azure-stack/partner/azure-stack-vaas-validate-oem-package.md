---
title: Azure Stack のサービスとしての検証で OEM (相手先ブランド供給) パッケージを検証する | Microsoft Docs
description: サービスとしての検証で OEM (相手先ブランド供給) パッケージを検証する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 02/19/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f9ed10c84be86304722020606873b0c7866df1e8
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594051"
---
# <a name="validate-oem-packages"></a>OEM パッケージの検証

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

完了しているソリューションの検証に関してファームウェアやドライバーに変更が生じたときは、新しい OEM パッケージをテストすることができます。 テストに合格したパッケージは、Microsoft によって署名されます。 テストには、Windows Server ロゴと PCS テストに合格したドライバーとファームウェアと共に、更新された OEM 拡張機能パッケージが含まれている必要があります。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> パッケージをアップロードまたは送信する前に、「[Create an OEM package (OEM パッケージの作成)](azure-stack-vaas-create-oem-package.md)」を参照して、期待されるパッケージ形式と内容について確認してください。

## <a name="managing-packages-for-validation"></a>検証のためのパッケージの管理

**パッケージの検証**ワークフローを使用してパッケージを検証する場合は、**Azure Storage Blob** への URL を指定する必要があります。 この BLOB は、更新プロセスの一環としてインストールされる、テスト署名済みの OEM パッケージです。 設定中に作成した Azure Storage アカウントを使用して BLOB を作成します ([サービスとしての検証のリソースの設定](azure-stack-vaas-set-up-resources.md)に関するページを参照してください)。

### <a name="prerequisite-provision-a-storage-container"></a>前提条件:ストレージ コンテナーをプロビジョニングする

パッケージ BLOB のストレージ アカウントにコンテナーを作成します。 このコンテナーは、すべてのパッケージの検証の実行に使用できます。

1. [Azure portal](https://portal.azure.com) で、[サービスとしての検証のリソースの設定](azure-stack-vaas-set-up-resources.md)に関する記事で作成したストレージ アカウントに移動します。
2. 左側のブレードの **[Blob service]** で、**[コンテナー]** を選択します。
3. メニュー バーの **[+ コンテナー]** を選択し、コンテナーの名前を入力します (例: `vaaspackages`)。

### <a name="upload-package-to-storage-account"></a>ストレージ アカウントへのパッケージのアップロード

1. 検証するパッケージを準備します。 パッケージに複数のファイルがある場合は、`.zip` ファイルに圧縮します。
2. [Azure portal](https://portal.azure.com) で、パッケージ コンテナーを選択し、メニュー バーの **[アップロード]** を選択してパッケージをアップロードします。
3. アップロードするパッケージの `.zip` ファイルを選択します。 **[BLOB の種類]** (**[ブロック BLOB]**) と **[ブロック サイズ]** は既定値のままにしておきます。

> [!NOTE]
> `.zip` の内容が `.zip` ファイルのルートに置かれていることを確認してください。 パッケージにサブフォルダーを含めないでください。

### <a name="generate-package-blob-url-for-vaas"></a>VaaS のパッケージ BLOB の URL の生成

VaaS ポータルで**パッケージの検証**ワークフローを作成する場合、パッケージが含まれている Azure Storage BLOB への URL を指定する必要があります。

#### <a name="option-1-generating-a-blob-sas-url"></a>オプション 1:BLOB の SAS URL を生成する

ストレージ コンテナーまたは BLOB へのパブリック読み取りアクセスを有効にしたくない場合は、このオプションを使用します。

1. [Azure portal](https://portal.azure.com/) でストレージ アカウントに移動し、パッケージが含まれている .zip に移動します

2. コンテキスト メニューで **[SAS の生成]** を選択します

3. **[アクセス許可]** の **[読み取り]** を選択します

4. **[開始時間]** を現在の時刻に設定し、**[終了時間]** を少なくとも **[開始時間]** から 48 時間後に設定します。 同じパッケージに対して別のテストも行う場合は、**[終了時間]** をテストの時間分遅らせることを検討します。 VaaS を通じて **[終了時間]** より後にスケジュールされたテストは失敗し、新しい SAS の生成が必要になります。

5. **[BLOB SAS トークンおよび URL を生成]** を選択します。

ポータルでパッケージ BLOB URL を指定する場合は、**BLOB SAS URL** を使用してください。

#### <a name="option-2-grant-public-read-access"></a>オプション 2:パブリック読み取りアクセスを許可する

> [!CAUTION]
> このオプションでは、匿名の読み取り専用アクセス用に BLOB を開放します。

1. 「[コンテナーと BLOB への匿名ユーザーのアクセス許可を付与します](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)」の手順に従って、**BLOB のパブリック読み取りアクセス**をパッケージ コンテナーにのみ許可します。

> [!NOTE]
> パッケージ URL を "*対話型テスト*" (たとえば、Monthly AzureStack Update Verification (月次 Azure Stack 更新プログラムの検証)、OEM Extension Package Verification (OEM 拡張機能パッケージの検証) など) に提供する場合は、テストを続行するために、**完全パブリック読み取りアクセス**を付与する必要があります。

2. パッケージ コンテナーで、パッケージ BLOB を選択してプロパティ ウィンドウを開きます。

3. **URL** をコピーします。 ポータルでパッケージ BLOB URL を指定する場合は、この値を使用してください。

## <a name="apply-monthly-update"></a>毎月の更新プログラムの適用

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>パッケージの検証ワークフローの作成

1. [VaaS ポータル](https://azurestackvalidation.com)にサインインします。

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. **[パッケージの検証]** タイルの **[開始]** を選択します。

    ![パッケージの検証ワークフローのタイル](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Microsoft の署名が必要なテスト署名済み OEM パッケージに、Azure Storage BLOB URL を入力します。 手順については、「[VaaS のパッケージ BLOB の URL の生成](#generate-package-blob-url-for-vaas)」を参照してください。

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 環境パラメーターは、ワークフローを作成した後は変更できません。

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    テストの概要ページにリダイレクトされます。

## <a name="required-tests"></a>必須のテスト

OEM パッケージの検証には、以下のテストが必要です。

- OEM Extension Package Verification (OEM 拡張機能パッケージの検証)
- Cloud Simulation Engine (クラウド シミュレーション エンジン)

## <a name="run-package-validation-tests"></a>パッケージの検証テストの実行

1. **パッケージの検証テストの概要**に関するページでは、ご自分のシナリオに適した、一覧表示されているテストの一部を実行します。

    検証ワークフローでは、テストを**スケジュール設定**するときに、ワークフローの作成時に指定したワークフロー レベルの一般的なパラメーターを使用します (「[Azure Stack Validation as a Service に使用される一般的なワークフロー パラメーター](azure-stack-vaas-parameters.md)」を参照してください)。 テスト パラメーター値のいずれかが無効になった場合は、[ワークフロー パラメーターの変更](azure-stack-vaas-monitor-test.md#change-workflow-parameters)に関するセクションの手順に従ってパラメーター値を再度指定する必要があります。

    > [!NOTE]
    > 既存のインスタンスに対して検証テストをスケジュール設定すると、ポータルの古いインスタンスに代わる新しいインスタンスが作成されます。 古いインスタンスのログは保持されますが、ポータルからアクセスできません。  
    テストが正常に完了すると、**[スケジュール]** アクションが無効になります。

2. テストを実行するエージェントを選択します。 ローカル テストの実行エージェントの追加については、「[ローカル エージェントをデプロイする](azure-stack-vaas-local-agent.md)」を参照してください。

3. OEM Extension Package Verification (OEM 拡張機能パッケージの検証) を完了するには、コンテキスト メニューの **[スケジュール]** を選択して、テスト インスタンスをスケジュール設定するためのプロンプトを開きます。

4. テスト パラメーターを確認し、**[送信]** を選択して、OEM Extension Package Verification (OEM 拡張機能パッケージの検証) の実行をスケジュール設定します。

5. OEM Extension Package Verification (OEM 拡張機能パッケージの検証) の結果を確認します。 テストが成功したら、Cloud Simulation Engine (クラウド シミュレーション エンジン) の実行をスケジュール設定します。

すべてのテストが正常に完了したら、VaaS ソリューションとパッケージの検証の名前を [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) に送信して、パッケージの署名を要求してください。

## <a name="next-steps"></a>次の手順

- [VaaS ポータルでのテストの監視と管理](azure-stack-vaas-monitor-test.md)