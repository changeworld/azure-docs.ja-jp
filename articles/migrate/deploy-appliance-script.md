---
title: スクリプトを使用して Azure Migrate アプライアンスを設定する
description: スクリプトを使用して Azure Migrate アプライアンスを設定する方法について
ms.topic: how-to
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.date: 11/02/2021
ms.openlocfilehash: 2c82b8c14f68ab11ad0585390e68de4864ee7169
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500167"
---
# <a name="set-up-an-appliance-with-a-script"></a>スクリプトを使用してアプライアンスを設定する

VMware 環境で実行されているサーバーの検出、評価、エージェントレス レプリケーション、および Hyper-V 環境で実行されているサーバーの検出と評価に PowerShell スクリプトを使用して [Azure Migrate アプライアンス](./migrate-appliance-architecture.md) をデプロイするには、この記事に従ってください。
- VMware 環境で実行されているサーバーの検出、評価、エージェントレス レプリケーション
- Hyper-V 環境で実行されているサーバーの検出と評価。

VMware および Hyper-V 上のサーバーに対してアプライアンスをデプロイするには、スクリプトを使用するか、または Azure portal からダウンロードしたテンプレート (OVA/VHD) を使用します。 ダウンロードしたテンプレートを使用してアプライアンスを作成できない場合は、スクリプトの使用が有効です。

- テンプレートを使用するには、[VMware](./tutorial-discover-vmware.md) および [Hyper-V](./tutorial-discover-hyper-v.md) のチュートリアルに従ってください。
- 物理サーバー用のアプライアンスを設定する場合は、スクリプトのみ使用できます。 [こちらの記事](how-to-set-up-appliance-physical.md)に従ってください。
- Azure Government クラウドでアプライアンスを設定する場合は、スクリプトのみを使用できます。 [こちらの記事](deploy-appliance-script-government.md)に従ってください。

## <a name="prerequisites"></a>前提条件

このスクリプトを使用すると、VMware または Hyper-V 環境内の既存のサーバーに Azure Migrate アプライアンスをデプロイできます。

- アプライアンスをホストするサーバーは、次のハードウェアおよび OS の要件を満たす必要があります。

シナリオ | 必要条件
--- | ---
VMware | メモリが 32 GB、vCPU が 8 個、ディスク ストレージが約 80 GB の Windows Server 2016。
Hyper-V | メモリが 16 GB、vCPU が 8 個、ディスク ストレージが約 80 GB の Windows Server 2016。

- サーバーには外部仮想スイッチも必要です。 これには、静的または動的 IP アドレスが必要です。 
- アプライアンスをデプロイする前に、[VMware](migrate-appliance.md#appliance---vmware) と [Hyper-V](migrate-appliance.md#appliance---hyper-v) の詳細なアプライアンス要件をご確認ください。
- Azure Migrate アプライアンスが既に設定されているサーバーでスクリプトを実行する場合は、既存の構成をクリーンアップし、必要な構成の新しいアプライアンスを設定することができます。 スクリプトを実行すると、次に示すような通知が表示されます。

    ![必要な構成でのアプライアンスの設定](./media/deploy-appliance-script/script-reconfigure-appliance.png)

## <a name="set-up-the-appliance-for-vmware"></a>VMware 用のアプライアンスを設定する

1. アプライアンスを設定するには、ポータルまたは[こちら](https://go.microsoft.com/fwlink/?linkid=2116601)から AzureMigrateInstaller.zip という名前の zip ファイルをダウンロードします。
1. アプライアンスをデプロイするサーバー上にコンテンツを抽出します。
1. PowerShell スクリプトを実行して、アプライアンス構成マネージャーを起動します。
1. 最初に、アプライアンスを設定して構成します。

### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    **ダウンロード** | **ハッシュ値**
    --- | ---
    [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2116601) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> 同じスクリプトを使用して、Azure パブリックまたは Azure Government クラウドに VMware アプライアンスを設定できます。

### <a name="run-the-script"></a>スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。
> [!NOTE]
> 既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。 Azure Migrate アプライアンスでスクリプトを実行すると、動作中の構成が削除され、新しく定義された構成に置き換えられます。

2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。

3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。

4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. シナリオ、クラウド、接続性からそれぞれオプションを選択して、必要な構成でアプライアンスをデプロイします。 たとえば、以下に示す選択内容の場合、アプライアンスは、**Azure パブリック クラウド** で **既定の _(パブリック エンドポイント)_ 接続** を使用して、**VMware 環境で実行されているサーバー** を検出、評価して Azure Migrate プロジェクトに移行するように設定されます。

   :::image type="content" source="./media/deploy-appliance-script/script-vmware-default-inline.png" alt-text="必要な構成で Vmware アプライアンスを設定する方法を示すスクリーンショット。" lightbox="./media/deploy-appliance-script/script-vmware-default-expanded.png":::

6. インストーラー スクリプトでは以下が実行されます。

 - エージェントと Web アプリケーションをインストールします。
 - Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
 - IIS 書き込み可能モジュールをダウンロードしてインストールする。
 - Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
 - パスに次のファイルを作成する。
    - **構成ファイル**: `%ProgramData%\Microsoft Azure\Config`
    - **ログ ファイル**: `%ProgramData%\Microsoft Azure\Logs`

スクリプトが正常に実行されると、アプライアンス構成マネージャーが自動的に起動します。

> [!NOTE]
> 問題が発生した場合は、トラブルシューティングのために、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log のスクリプト ログにアクセスできます。

### <a name="verify-access"></a>アクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウド向けクラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V のアプライアンスを設定する

1. アプライアンスを設定するには、ポータルまたは[こちら](https://go.microsoft.com/fwlink/?linkid=2116657)から AzureMigrateInstaller.zip という名前の zip ファイルをダウンロードします。
1. アプライアンスをデプロイするサーバー上にコンテンツを抽出します。
1. PowerShell スクリプトを実行して、アプライアンス構成マネージャーを起動します。
1. 最初に、アプライアンスを設定して構成します。

### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    **ダウンロード** | **ハッシュ値**
    --- | ---
    [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2116657) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> 同じスクリプトを使用して、Azure パブリックまたは Azure Government クラウドに Hyper-V アプライアンスを設定できます。

### <a name="run-the-script"></a>スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。
> [!NOTE]
> 既存の Azure Migrate アプライアンスでスクリプトを実行しないようにしてください。 Azure Migrate アプライアンスでスクリプトを実行すると、動作中の構成が削除され、新しく定義された構成に置き換えられます。

2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。
4. 次のコマンドを実行して `AzureMigrateInstaller.ps1` という名前のスクリプトを実行します。

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. シナリオ、クラウド、接続性からそれぞれオプションを選択して、必要な構成でアプライアンスをデプロイします。 たとえば、以下に示す選択内容の場合、アプライアンスは、**Azure パブリック クラウド** で **既定の _(パブリック エンドポイント)_ 接続** を使用して、**Hyper-V 環境で実行されているサーバー** を検出して Azure Migrate プロジェクトに対して評価するように設定されます。

    :::image type="content" source="./media/deploy-appliance-script/script-hyperv-default-inline.png" alt-text="必要な構成で Hyper-V アプライアンスを設定する方法を示すスクリーンショット。" lightbox="./media/deploy-appliance-script/script-hyperv-default-expanded.png":::

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

[パブリック](migrate-appliance.md#public-cloud-urls) クラウド向けクラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="next-steps"></a>次のステップ

アプライアンスをデプロイしたら、それを最初に構成し、プロジェクトに登録する必要があります。

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance) のアプライアンスを設定します。
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) のアプライアンスを設定します。
