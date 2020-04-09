---
title: スクリプトを使用して Azure Migrate アプライアンスを設定する
description: スクリプトを使用して Azure Migrate アプライアンスを設定する方法について
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337369"
---
# <a name="set-up-an-appliance-with-a-script"></a>スクリプトを使用してアプライアンスを設定する

この記事では、PowerShell インストーラー スクリプトを使用して [Azure Migrate アプライアンス](deploy-appliance.md)を設定する方法について説明します。

このスクリプトが提供するもの：
- VMware VM の評価とエージェントレスな移行のための、OVA テンプレートを使用したアプライアンスの設定オプション。
- Hyper-V VM の評価と移行のための、OVA テンプレートを使用したアプライアンスの設定オプション。
- 物理サーバー (または物理サーバーに移行させたい VM) の評価。このスクリプトが、アプライアンスの設定ための唯一の方法です。

## <a name="prerequisites"></a>前提条件

このスクリプトは、既存の物理マシンまたは VM 上に Azure Migrate アプライアンスを設定します。

- アプライアンスとして機能するマシンは、32 GB のメモリ、8 個の vCPUs、80 GB のディスク　ストレージ、および外部仮想スイッチを搭載し、Windows Server 2016 を実行している必要があります。 ここには、静的または動的 IP アドレスと、インターネットへのアクセスが必要です。
- アプライアンスを展開する前に、[VMware VM](migrate-appliance.md#appliance---vmware)、[Hyper-V VM](migrate-appliance.md#appliance---hyper-v)、[物理サーバー](migrate-appliance.md#appliance---physical)の詳細なアプライアンス要件をレビューしてください。
- 既存の Azure Migrate アプライアンスでスクリプトを実行しないでください。


## <a name="download-the-script"></a>スクリプトのダウンロード

1. Azure Migrate アプライアンスとして機能するマシンまたは VM を特定します。
2. マシンで次の操作を行います。

    - VMware VM または Hyper-v VM でアプライアンスを使用するには、インストーラー スクリプトと MSI を含む zip 形式のフォルダーを[ダウンロード](https://go.microsoft.com/fwlink/?linkid=2105112)します。
    - 物理サーバーでアプライアンスを使用するには、こちらの[チュートリアル](tutorial-assess-physical.md#set-up-the-appliance)を参考に、Azure Migrate ポータルからスクリプトをダウンロードします。

## <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. 生成されたハッシュ値が次の設定と一致することを確認します (最新のアプライアンス バージョンの場合)。

    **アルゴリズム** | **ハッシュ値**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>スクリプトを実行する

スクリプトの機能：

- エージェントと Web アプリケーションをインストールします。
- Windows のロール (Windows Activation Service、IIS、PowerShell ISE など) をインストールします。
- IIS 再書き込み可能モジュールをダウンロードしてインストールします。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定を使用して、レジストリ キー (HKLM) を更新します。
- ログファイルと構成ファイルを次のように作成します。
    - **構成ファイル**: %ProgramData%\Microsoft Azure\Config
    - **ログ ファイル**: %ProgramData%\Microsoft Azure\Logs

スクリプトを実行するには、次の手順を実行します。

1. アプライアンスをホストするマシン上のフォルダーに ZIP ファイルを展開します。
2. （昇格した）管理者権限を使用し、マシンで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。

    - VMware の場合： 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Hyper-V の場合：
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - 物理サーバーの場合：
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log. で、スクリプトログを確認できます。

## <a name="next-steps"></a>次のステップ

スクリプトを使用してアプライアンスを設定したら、次の手順に従います。

- [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance) のアプライアンスを設定します。
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) のアプライアンスを設定します。
- [物理サーバー](how-to-set-up-appliance-physical.md)のアプライアンスを設定します。