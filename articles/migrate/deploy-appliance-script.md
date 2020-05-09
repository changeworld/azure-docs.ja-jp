---
title: スクリプトを使用して Azure Migrate アプライアンスを設定する
description: スクリプトを使用して Azure Migrate アプライアンスを設定する方法について
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676310"
---
# <a name="set-up-an-appliance-with-a-script"></a>スクリプトを使用してアプライアンスを設定する

この記事では、VMware VM および Hyper-V VM の評価または移行のために [Azure Migrate アプライアンス](deploy-appliance.md)を作成する方法について説明します。 スクリプトを実行してアプライアンスを作成し、それが Azure に接続可能であることを確認します。 

VMware および Hyper-V VM 用のアプライアンスをデプロイするには、スクリプトを使用するか、または Azure portal からダウンロードしたテンプレートを使用します。 ダウンロードしたテンプレートを使用して VM を作成できない場合は、スクリプトを使用する方法が有用です。

- テンプレートを使用するには、[VMware](tutorial-prepare-vmware.md) または [Hyper-V](tutorial-prepare-hyper-v.md) のチュートリアルに従ってください。
- 物理サーバー用のアプライアンスを設定する場合は、スクリプトのみ使用できます。 [こちらの記事](how-to-set-up-appliance-physical.md)に従ってください。
- Azure Government クラウドでアプライアンスを設定するには、[こちらの記事](deploy-appliance-script-government.md)に従ってください。

## <a name="prerequisites"></a>前提条件

このスクリプトは、既存の物理マシンまたは VM 上に Azure Migrate アプライアンスを設定します。

- アプライアンスとして機能するマシンは、32 GB のメモリ、8 個の vCPUs、80 GB のディスク　ストレージ、および外部仮想スイッチを搭載し、Windows Server 2016 を実行している必要があります。 ここには、静的または動的 IP アドレスと、インターネットへのアクセスが必要です。
- アプライアンスを展開する前に、[VMware VM](migrate-appliance.md#appliance---vmware)、[Hyper-V VM](migrate-appliance.md#appliance---hyper-v)、[物理サーバー](migrate-appliance.md#appliance---physical)の詳細なアプライアンス要件をレビューしてください。
- 既存の Azure Migrate アプライアンスでスクリプトを実行しないでください。

## <a name="set-up-the-appliance-for-vmware"></a>VMware 用のアプライアンスを設定する

VMware 用のアプライアンスを設定するには、Azure portal から ZIP ファイルをダウンロードし、その内容を抽出します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、Azure Migrate プロジェクトにアプライアンスを登録します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1.  **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2.  **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3.  **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。 


### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. 生成されたハッシュ値を確認します。 最新のアプライアンス バージョンの場合: 

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



### <a name="run-the-script"></a>スクリプトを実行する

スクリプトの機能：

- エージェントと Web アプリケーションをインストールします。
- Windows のロール (Windows Activation Service、IIS、PowerShell ISE など) をインストールします。
- IIS 再書き込み可能モジュールをダウンロードしてインストールします。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定を使用して、レジストリ キー (HKLM) を更新します。
- ログファイルと構成ファイルを次のように作成します。
    - **構成ファイル**: %ProgramData%\Microsoft Azure\Config
    - **ログ ファイル**: %ProgramData%\Microsoft Azure\Logs

スクリプトを実行するには、次の手順を実行します。

1. アプライアンスをホストするマシン上のフォルダーに ZIP ファイルを展開します。 既存の Azure Migrate アプライアンス マシンでスクリプトを実行しないようご注意ください。
2. （昇格した）管理者権限を使用し、マシンで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウド向けクラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V のアプライアンスを設定する

Hyper-V のアプライアンスを設定するには、Azure portal から ZIP ファイルをダウンロードし、その内容を抽出します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、Azure Migrate プロジェクトにアプライアンスを登録します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1.  **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2.  **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
3.  **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。 


### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. 生成されたハッシュ値を確認します。 最新のアプライアンス バージョンの場合: 

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-script"></a>スクリプトを実行する

スクリプトの機能：

- エージェントと Web アプリケーションをインストールします。
- Windows のロール (Windows Activation Service、IIS、PowerShell ISE など) をインストールします。
- IIS 再書き込み可能モジュールをダウンロードしてインストールします。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定を使用して、レジストリ キー (HKLM) を更新します。
- ログファイルと構成ファイルを次のように作成します。
    - **構成ファイル**: %ProgramData%\Microsoft Azure\Config
    - **ログ ファイル**: %ProgramData%\Microsoft Azure\Logs

スクリプトを実行するには、次の手順を実行します。

1. アプライアンスをホストするマシン上のフォルダーに ZIP ファイルを展開します。 既存の Azure Migrate アプライアンス マシンでスクリプトを実行しないようご注意ください。
2. （昇格した）管理者権限を使用し、マシンで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウド向けクラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="next-steps"></a>次のステップ

アプライアンスをデプロイしたら、それを最初に構成してから、Azure Migrate プロジェクトに登録する必要があります。

- [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance) のアプライアンスを設定します。
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) のアプライアンスを設定します。
