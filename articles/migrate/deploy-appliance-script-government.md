---
title: Azure Government で Azure Migrate アプライアンスを設定する
description: Azure Government で Azure Migrate アプライアンスを設定する方法について説明します
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 9586685bc5fd482bf3e87dcfd6848a08c74dfc4f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509669"
---
# <a name="set-up-an-appliance-for-azure-government-cloud"></a>Azure Government クラウドでアプライアンスを設定する

[Azure Migrate アプライアンス](./migrate-appliance-architecture.md)を Azure Government クラウドにデプロイして以下を行うには、この記事に従ってください。

- VMware 環境で実行されているサーバーの検出、評価、エージェントレス レプリケーション
- Hyper-V 環境で実行されているサーバーの検出と評価
- 物理サーバーまたは AWS、GCP、Xen などの他のクラウドで実行されているサーバーの検出と評価

パブリック クラウドでアプライアンスを設定する場合は、[こちらの記事](deploy-appliance-script.md)に従ってください。

> [!NOTE]
> テンプレートを使用して (VMware または Hyper-V 環境で実行しているサーバー用の) アプライアンスをデプロイする方法は、Azure Government ではサポートされません。 インストーラー スクリプトのみを使用する必要があります。

## <a name="prerequisites"></a>前提条件

スクリプトを使用して、既存の物理サーバーまたは仮想化されたサーバーに Azure Migrate アプライアンスをデプロイできます。

- アプライアンスとして機能するサーバーは、Windows Server 2016 を実行し、[VMware](migrate-appliance.md#appliance---vmware)、[Hyper-V](migrate-appliance.md#appliance---hyper-v)、および[物理サーバー](migrate-appliance.md#appliance---physical)の他の要件を満たしている必要があります。
- Azure Migrate アプライアンスが既に設定されているサーバーでスクリプトを実行する場合は、既存の構成をクリーンアップし、必要な構成の新しいアプライアンスを設定することができます。 スクリプトを実行すると、次に示すような通知が表示されます。
  
    :::image type="content" source="./media/deploy-appliance-script/script-reconfigure-appliance.png" alt-text="アプライアンスを再構成する方法を示すスクリーンショット。":::

## <a name="set-up-the-appliance-for-vmware"></a>VMware 用のアプライアンスを設定する

1. アプライアンスを設定するには、ポータルまたは[こちら](https://go.microsoft.com/fwlink/?linkid=2140334)から AzureMigrateInstaller.zip という名前の zip ファイルをダウンロードします。
1. アプライアンスをデプロイするサーバー上にコンテンツを抽出します。
1. PowerShell スクリプトを実行して、アプライアンス構成マネージャーを起動します。
1. 最初に、アプライアンスを設定して構成します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1. **[移行の目標]**  >  **[Windows、Linux、SQL Server]**  >  **[Azure Migrate: Discovery and Assessment]** で、 **[検出]** をクリックします。
2. **[サーバーの検出]**  >  **[お使いのサーバーは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3. ポータルで、アプライアンス名を指定してプロジェクト キーを生成します。
3. **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。

### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    **ダウンロード** | **ハッシュ値**
    --- | ---
    [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140337) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029


### <a name="run-the-script"></a>スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。  既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。

2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。

3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。

4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. シナリオ、クラウド、接続性からそれぞれオプションを選択して、必要な構成でアプライアンスをデプロイします。 たとえば、以下に示す選択内容の場合は、**Azure Government クラウド** 上の **既定の _(パブリック エンドポイント)_ 接続性** を使用して、**VMware 環境で実行されているサーバー** を検出、評価して Azure Migrate プロジェクトに移行するようにアプライアンスが設定されます。

   :::image type="content" source="./media/deploy-appliance-script-government/script-vmware-gov-inline.png" alt-text="必要な Vmware 向けの構成でアプライアンスを設定する方法を示すスクリーンショット。" lightbox="./media/deploy-appliance-script-government/script-vmware-gov-expanded.png":::

6. インストーラー スクリプトでは以下が実行されます。

   - エージェントと Web アプリケーションをインストールします。
   - Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
   - IIS 書き込み可能モジュールをダウンロードしてインストールする。
   - Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
   - パスに次のファイルを作成する。
     - **構成ファイル**: %Programdata%\Microsoft Azure\Config
     - **ログ ファイル**: %Programdata%\Microsoft Azure\Logs

スクリプトが正常に実行されると、アプライアンス構成マネージャーが自動的に起動します。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V のアプライアンスを設定する

1. アプライアンスを設定するには、ポータルまたは[こちら](https://go.microsoft.com/fwlink/?linkid=2140334)から AzureMigrateInstaller.zip という名前の zip ファイルをダウンロードします。
1. アプライアンスをデプロイするサーバー上にコンテンツを抽出します。
1. PowerShell スクリプトを実行して、アプライアンス構成マネージャーを起動します。
1. 最初に、アプライアンスを設定して構成します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1.  **[移行の目標]**  >  **[Windows、Linux、SQL Server]**  >  **[Azure Migrate: Discovery and Assessment]** で、 **[検出]** をクリックします。
2.  **[サーバーの検出]**  >  **[お使いのサーバーは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
3. ポータルで、アプライアンス名を指定してプロジェクト キーを生成します。
3. **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。 

### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    **ダウンロード** | **ハッシュ値**
    --- | ---
    [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140424) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

### <a name="run-the-script"></a>スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。  既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。

2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。

3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。

4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. シナリオ、クラウド、接続性からそれぞれオプションを選択して、必要な構成でアプライアンスをデプロイします。 たとえば、以下に示す選択内容の場合は、**Azure Government クラウド** 上の **既定の _(パブリック エンドポイント)_ 接続性** を使用して、**Hyper-V 環境で実行されているサーバー** を検出し、Azure Migrate プロジェクトに評価するようにアプライアンスが設定されます。

    :::image type="content" source="./media/deploy-appliance-script-government/script-hyperv-gov-inline.png" alt-text="必要な Hyper-V 向けの構成でアプライアンスを設定する方法を示すスクリーンショット。" lightbox="./media/deploy-appliance-script-government/script-hyperv-gov-expanded.png":::

6. インストーラー スクリプトでは以下が実行されます。

    - エージェントと Web アプリケーションをインストールします。
    - Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
    - IIS 書き込み可能モジュールをダウンロードしてインストールする。
    - Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
    - パスに次のファイルを作成する。
    - **構成ファイル**: %Programdata%\Microsoft Azure\Config
    - **ログ ファイル**: %Programdata%\Microsoft Azure\Logs

スクリプトが正常に実行されると、アプライアンス構成マネージャーが自動的に起動します。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。


## <a name="set-up-the-appliance-for-physical-servers"></a>物理サーバーのアプライアンスを設定する

1. アプライアンスを設定するには、ポータルまたは[こちら](https://go.microsoft.com/fwlink/?linkid=2140334)から AzureMigrateInstaller.zip という名前の zip ファイルをダウンロードします。
1. アプライアンスをデプロイするサーバー上にコンテンツを抽出します。
1. PowerShell スクリプトを実行して、アプライアンス構成マネージャーを起動します。
1. 最初に、アプライアンスを設定して構成します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1. **[移行の目標]**  >  **[Windows、Linux、SQL Server]**  >  **[Azure Migrate: Discovery and Assessment]** で、 **[検出]** をクリックします。
2. **[サーバーの検出]**  >  **[お使いのサーバーは仮想化されていますか?]** で、 **[物理またはその他 (AWS、GCP、Xen など)]** を選択します。
3. **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。

### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    **ダウンロード** | **ハッシュ値**
    --- | ---
    [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140338) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> 同じスクリプトを使用して、パブリックまたはプライベート エンドポイント接続性を備えた Azure Government クラウドに対して物理アプライアンスを設定できます。

### <a name="run-the-script"></a>スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。  既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。

2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。

3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。

4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。

    `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. シナリオ、クラウド、接続性からそれぞれオプションを選択して、必要な構成でアプライアンスをデプロイします。 たとえば、以下に示す選択内容の場合は、**Azure Government クラウド** 上の **既定の _(パブリック エンドポイント)_ 接続性** を使用して、**物理サーバー** _(または AWS、GCP、Xen などの他のクラウドで実行されているサーバー)_ を検出し、Azure Migrate プロジェクトに評価するようにアプライアンスが設定されます。

    :::image type="content" source="./media/deploy-appliance-script-government/script-physical-gov-inline.png" alt-text="必要な物理サーバー向けの構成でアプライアンスを設定する方法を示すスクリーンショット。" lightbox="./media/deploy-appliance-script-government/script-physical-gov-expanded.png":::

6. インストーラー スクリプトでは以下が実行されます。

    - エージェントと Web アプリケーションをインストールします。
    - Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
    - IIS 書き込み可能モジュールをダウンロードしてインストールする。
    - Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
    - パスに次のファイルを作成する。
        - **構成ファイル**: %Programdata%\Microsoft Azure\Config
        - **ログ ファイル**: %Programdata%\Microsoft Azure\Logs

スクリプトが正常に実行されると、アプライアンス構成マネージャーが自動的に起動します。

> [!NOTE]
> 問題が発生した場合は、トラブルシューティングのために、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log のスクリプト ログにアクセスできます。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="next-steps"></a>次のステップ

アプライアンスをデプロイしたら、それを最初に構成し、プロジェクトに登録する必要があります。

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance) のアプライアンスを設定します。
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) のアプライアンスを設定します。
- [物理サーバー](how-to-set-up-appliance-physical.md)のアプライアンスを設定します。
