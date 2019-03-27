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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aae3ec8ff713959c5cc2485951aba025a6f89a1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113284"
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

3. メニュー バーから **[+ コンテナー]** を選択します。
    1. `vaaspackages` などのコンテナーの名前を指定します。
    1. VaaS などの非認証クライアントに必要なアクセス レベルを選択します。 各シナリオでのパッケージに VaaS アクセスを付与する方法の詳細については、「[コンテナーのアクセス レベルを処理する](#handling-container-access-level)」を参照してください。

### <a name="upload-package-to-storage-account"></a>ストレージ アカウントへのパッケージのアップロード

1. 検証するパッケージを準備します。 これは `.zip` ファイルであり、その内容は「[OEM パッケージを作成する](azure-stack-vaas-create-oem-package.md)」で説明されている構造と一致している必要があります。

    > [!NOTE]
    > `.zip` の内容が `.zip` ファイルのルートに置かれていることを確認してください。 パッケージにサブフォルダーを含めないでください。

1. [Azure portal](https://portal.azure.com) で、パッケージ コンテナーを選択し、メニュー バーの **[アップロード]** を選択してパッケージをアップロードします。

1. アップロードするパッケージの `.zip` ファイルを選択します。 **[BLOB の種類]** (**[ブロック BLOB]**) と **[ブロック サイズ]** は既定値のままにしておきます。

### <a name="generate-package-blob-url-for-vaas"></a>VaaS のパッケージ BLOB の URL の生成

VaaS ポータルで**パッケージの検証**ワークフローを作成する場合、パッケージが含まれている Azure Storage BLOB への URL を指定する必要があります。 **Monthly AzureStack Update Verification (月次 Azure Stack 更新プログラムの検証)** と **OEM Extension Package Verification (OEM 拡張機能パッケージの検証)** などの、一部の*対話型*テストではパッケージ BLOB の URL が必要です。

#### <a name="handling-container-access-level"></a>コンテナーのアクセス レベルを処理する

VaaS に必要な最低限のアクセス レベルは、パッケージの検証ワークフローを作成するか、*対話型*テストをスケジュール設定するかによって異なります。

**プライベート**と **Blob** アクセス レベルの場合、[Shared Access Signature (SAS)](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) を VaaS に設定することによって、パッケージ blob へのアクセス権を一時的に付与する必要があります。 **コンテナー** アクセス レベルでは SAS URL を生成する必要はありませんが、コンテナーとその BLOB への非認証アクセスを許可します。

|アクセス レベル | ワークフローの要件 | テストの要件 |
|---|---------|---------|
|プライベート | パッケージ BLOB ごとに SAS URL を生成します ([オプション 1](#option-1-generate-a-blob-sas-url))。 | アカウント レベルで SAS URL を生成し、パッケージ BLOB 名を手動で追加します ([オプション 2](#option-2-construct-a-container-sas-url))。 |
|BLOB | BLOB の URL プロパティを提供します ([オプション 3](#option-3-grant-public-read-access))。 | アカウント レベルで SAS URL を生成し、パッケージ BLOB 名を手動で追加します ([オプション 2](#option-2-construct-a-container-sas-url))。 |
|コンテナー | BLOB の URL プロパティを提供します ([オプション 3](#option-3-grant-public-read-access))。 | BLOB の URL プロパティを提供します ([オプション 3](#option-3-grant-public-read-access))。

パッケージへのアクセス権を付与するオプションでは、アクセス権は最小から最大に順序指定されています。

#### <a name="option-1-generate-a-blob-sas-url"></a>オプション 1:BLOB の SAS URL を生成する

ストレージ コンテナーのアクセス レベルが**プライベート**に設定されている場合は、このオプションを使用します。この場合、コンテナーまたはその BLOB へのパブリック読み取りアクセスを有効にしません。

> [!NOTE]
> このメソッドは*対話型*テストでは機能しません。 [オプション 2:コンテナーの SAS URL の構築](#option-2-construct-a-container-sas-url)を参照してください。

1. [Azure portal](https://portal.azure.com/) でストレージ アカウントに移動し、パッケージが含まれている .zip に移動します。

2. コンテキスト メニューで **[SAS の生成]** を選択します。

3. **[アクセス許可]** の **[読み取り]** を選択します。

4. **[開始時間]** を現在の時刻に設定し、**[終了時間]** を少なくとも **[開始時間]** から 48 時間後に設定します。 同じパッケージに対して別のワークフローも作成する場合は、**[終了時間]** をテストの時間分遅らせることを検討します。

5. **[BLOB SAS トークンおよび URL を生成]** を選択します。

ポータルでパッケージ BLOB URL を指定する場合は、**BLOB SAS URL** を使用してください。

#### <a name="option-2-construct-a-container-sas-url"></a>オプション 2:コンテナーの SAS URL の構築

ストレージ コンテナーのアクセス レベルが**プライベート**に設定されており、*対話型*テストにパッケージの BLOB URL を指定する必要がある場合、このオプションを使用します。 この URL は、ワークフロー レベルでも使用できます。

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. **[使用できるサービス]** のオプションから **[BLOB]** を選択します。 残りのオプションについてはすべて選択を解除します。

1. **[使用できるリソースの種類]** で、**[サービス]**、**[コンテナー]、**[オブジェクト]** を選択します。

1. **[与えられているアクセス許可]** で、**[読み取り]** と **[List]\(一覧\)** を選択します。 残りのオプションについてはすべて選択を解除します。

1. **[開始時間]** として現在の時刻を選択し、**[終了時間]** として少なくとも **[開始時間]** から 14 日後を選択します。 同じパッケージに対して別のテストも行う場合は、**[終了時間]** をテストの時間分遅らせることを検討します。 VaaS を通じて **[終了時間]** より後にスケジュールされたテストは失敗し、新しい SAS の生成が必要になります。

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    次のような形式になります。`https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. 次のように、生成された SAS URL を変更して、パッケージ コンテナー `{containername}` とパッケージ BLOB の名前 `{mypackage.zip}` を含めます。`https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    ポータルでパッケージ BLOB URL を指定する場合は、この値を使用してください。

#### <a name="option-3-grant-public-read-access"></a>オプション 3: パブリック読み取りアクセスを許可する

認証されていないクライアントから個々の BLOB へのアクセス、またはコンテナーへのアクセス (*対話型*テストの場合) を許可することが許容される場合は、このオプションを使用します。

> [!CAUTION]
> このオプションでは、匿名の読み取り専用アクセス用に BLOB を開放します。

1. 「[コンテナーと BLOB への匿名ユーザーのアクセス許可を付与します](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)」の手順に従って、**BLOB** または**コンテナー**にパッケージ コンテナーのアクセス レベルを設定します。

    > [!NOTE]
    > パッケージ URL を*対話型*テストに指定している場合、テストを続行するにはコンテナーへの**パブリック読み取りフル アクセス**を付与する必要があります。

1. パッケージ コンテナーで、パッケージ BLOB を選択してプロパティ ウィンドウを開きます。

1. **URL** をコピーします。 ポータルでパッケージ BLOB URL を指定する場合は、この値を使用してください。

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
    > テストが正常に完了すると、**[スケジュール]** アクションが無効になります。

2. テストを実行するエージェントを選択します。 ローカル テストの実行エージェントの追加については、「[ローカル エージェントをデプロイする](azure-stack-vaas-local-agent.md)」を参照してください。

3. OEM Extension Package Verification (OEM 拡張機能パッケージの検証) を完了するには、コンテキスト メニューの **[スケジュール]** を選択して、テスト インスタンスをスケジュール設定するためのプロンプトを開きます。

4. テスト パラメーターを確認し、**[送信]** を選択して、OEM Extension Package Verification (OEM 拡張機能パッケージの検証) の実行をスケジュール設定します。

    OEM 拡張機能パッケージの検証は、次の 2 つの手動ステップに分かれています。Azure Stack の更新プログラム、および OEM の更新プログラム。

   1. UI で [実行] を**選択**して事前チェック スクリプトを実行します。 これは完了までに約 5 分かかる自動テストであり、アクションは不要です。

   1. 事前チェック スクリプトが完了したら、手動ステップを実行します。Azure Stack ポータルを使用して、使用可能な最新の Azure Stack 更新プログラムを**インストールします**。

   1. スタンプで Test-AzureStack を**実行します**。 何かの障害が発生した場合は、テストを続行せずに [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) に連絡してください。

       Test-AzureStack コマンドを実行する方法については、「[Azure Stack システムの状態を検証する](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)」を参照してください。

   1. [次へ] を**選択**して postcheck スクリプトを実行します。 これは、自動テストであり、Azure Stack の更新プロセスが終了したことを示します。

   1. [実行] を**選択**して OEM 更新プログラムに対して事前チェック スクリプトを実行します。

   1. 事前チェックが完了すると、手動ステップを実行します。ポータルを通じて、OEM 拡張機能パッケージを**インストール**します。

   1. スタンプで Test-AzureStack を**実行**します。

      > [!NOTE]
      > 先ほどと同様に、何かの障害が発生した場合は、テストを続行せずに [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) に連絡してください。 この手順は再デプロイを保存するために重要です。

   1. [次へ] を**選択**して postcheck スクリプトを実行します。 これは OEM 更新プログラムのステップが終了したことを示します。

   1. テストの最後にある残りの質問に回答して、[送信] を**選択**します。

   1. これは、対話型テストが終了したことを示します。

5. OEM Extension Package Verification (OEM 拡張機能パッケージの検証) の結果を確認します。 テストが成功したら、Cloud Simulation Engine (クラウド シミュレーション エンジン) の実行をスケジュール設定します。

パッケージ署名要求を送信するには、この実行に関連付けられているソリューション名とパッケージ検証名を [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) に送信します。

## <a name="next-steps"></a>次の手順

- [VaaS ポータルでのテストの監視と管理](azure-stack-vaas-monitor-test.md)
