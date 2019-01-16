---
title: Azure Stack での Python による API バージョンのプロファイルの使用 | Microsoft Docs
description: Azure Stack での Python による API バージョンのプロファイルの使用について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: cafae6d71401bc44813b2e366f8e72f7b806236b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062777"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Azure Stack での Python による API バージョンのプロファイルの使用

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Python と API バージョン プロファイル

Python SDK では、Azure Stack とグローバル Azure などの異なるクラウド プラットフォームをターゲットとする API バージョン プロファイルをサポートします。 ハイブリッド クラウド向けのソリューションの作成時に API プロファイルを使用できます。 Python SDK では、以下の API プロファイルをサポートします。

1. **latest**  
    プロファイルは、Azure Platform 内のすべてのサービス プロバイダーの最新の API バージョンをターゲットにします。
2. **2017-03-09-profile**  
   **2017-03-09-profile**  
   このプロファイルは、Azure Stack がサポートするリソース プロバイダーの API バージョンをターゲットにします。

   API プロファイルと Azure Stack の詳細については、「[Azure Stack での API バージョンのプロファイルの管理](azure-stack-version-profiles.md)」を参照してください。

## <a name="install-the-azure-python-sdk"></a>Azure Python SDK をインストールする

1. Git を[公式サイト](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)からインストールします。
2. Python SDK のインストール方法については、[Python 開発者向け Azure](/python/azure/python-sdk-azure-install?view=azure-python) に関する記事を参照してください。
3. 使用できない場合は、サブスクリプションを作成し、サブスクリプション ID を保存して後で使用します。 サブスクリプションの作成の詳細については、「[Azure Stack でオファーのサブスクリプションを作成する](../azure-stack-subscribe-plan-provision-vm.md)」を参照してください。
4. サービス プリンシパルを作成し、その ID とシークレットを保存します。 Azure Stack 用のサービス プリンシパルの作成方法については、「[Azure Stack へのアクセスをアプリケーションに提供する](../azure-stack-create-service-principals.md)」を参照してください。
5. サブスクリプションでサービス プリンシパルのロールが共同作成者/所有者であることを確認します。 サービス プリンシパルへのロールの割り当て手順については、「[Azure Stack へのアクセスをアプリケーションに提供する](../azure-stack-create-service-principals.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure Stack で Python Azure SDK を使用するには、次の値を指定した後、環境変数に値を設定する必要があります。 環境変数を設定する方法については、表の後にある、オペレーティング システム別の手順を参照してください。

| 値 | 環境変数 | 説明 |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| テナント ID | AZURE_TENANT_ID | Azure Stack の[テナント ID](../azure-stack-identity-overview.md) の値。 |
| クライアント ID | AZURE_CLIENT_ID | この記事の前のセクションでサービス プリンシパルが作成されたときに保存した、サービス プリンシパル アプリケーション ID。 |
| サブスクリプション ID | AZURE_SUBSCRIPTION_ID | [サブスクリプション ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) は Azure Stack 内のオファーにアクセスするために必要です。 |
| クライアント シークレット | AZURE_CLIENT_SECRET | サービス プリンシパルの作成時に保存した、サービス プリンシパル アプリケーション シークレット。 |
| Resource Manager エンドポイント | ARM_ENDPOINT | 「[Azure Stack Resource Manager エンドポイント](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)」を参照してください。 |

## <a name="python-samples-for-azure-stack"></a>Azure Stack 向けの Python 例

次のサンプルコードを利用することで､Azure Stack で仮想マシンに対する一般的な管理タスクを行うことができます｡ サンプルコードは以下を行う方法を示します｡

- 仮想マシンを作成する
  - Linux 仮想マシンの作成
  - Windows 仮想マシンの作成
- 仮想マシンを更新する
  - ドライブの拡張
  - 仮想マシンへのタグ付け
  - データ ディスクを接続する
  - データ ディスクをデタッチする
- 仮想マシンを操作する
  - 仮想マシンの起動
  - 仮想マシンの停止
  - 仮想マシンの再起動
- 仮想マシンの列挙
- 仮想マシンの削除

これらの操作を実行するコードを確認するには、GitHub リポジトリ [virtual-machines-python-manage](https://github.com/Azure-Samples/virtual-machines-python-manage) にある Python スクリプト **Hybrid/unmanaged-disks/example.py** の **run_example()** 関数を参照してください。

それぞれの操作には､それと分かるコメントと print 関数が付いています｡ これらの例は、この一覧の順序どおりではない場合があります。

## <a name="run-the-python-sample"></a>Python のサンプルコードを実行する

1. まだ [Python をインストールしていない場合はインストールします](https://www.python.org/downloads/)。 このサンプル (と SDK) は､Python 2.7 と 3.4､3.5､3.6 に対応しています｡

2. Python 開発に対する一般的なレコメンデーションとしては､仮想環境を使用するということがあります｡ 詳細については、[Python のドキュメント](https://docs.python.org/3/tutorial/venv.html)を参照してください。

3. Python 3 の "venv" モジュール (Python 2.7 の場合は [virtualenv](https://pypi.python.org/pypi/virtualenv)) を使用して､仮想環境をインストールして初期化します｡

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. リポジトリを複製します。

    ```bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ```

5. pip を使用して依存関係をインストールします｡

    ```bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ```

6. Azure Stack と連携させる[サービス プリンシパル](../azure-stack-create-service-principals.md)を作成します｡ サブスクリプションでサービスプリンシパルのロールが[共同作成者/所有者](../azure-stack-create-service-principals.md#assign-a-role)であることを確認します｡

7. 次の変数を設定して､これらの環境変数を現在のシェルにエクスポートします｡

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

8. このサンプルを実行するには､Azure Stack マーケットプレースに Ubuntu 16.04-LTS と WindowsServer 2012-R2-Datacenter のイメージが存在している必要があります｡ これらは [Azure からダウンロード](../azure-stack-download-azure-marketplace-item.md)するか､[Platform Image Repository](../azure-stack-add-vm-image.md) に追加することができます｡

9. サンプルを実行します。

    ```python
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>メモ

`virtual_machine.storage_profile.os_disk` を使用して仮想マシンの OS ディスクを取得することができます｡ その場合､目的の処理が実行される可能性はありますが、**OSDisk** オブジェクトが提供されることに注意してください｡ OS ディスクのサイズを更新するには､`example.py` の場合と同様に、**OSDisk** オブジェクトではなく､**Disk** オブジェクトを使用します。 `example.py` では、次のプロパティを持つ **Disk** オブジェクトが取得されます。

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>次の手順

- [Azure Python 開発センター](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines 関係のドキュメント](https://azure.microsoft.com/services/virtual-machines/)
- [Virtual Machines のラーニング パス](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Microsoft Azure のサブスクリプションをお持ちでない場合は､[ここ](https://go.microsoft.com/fwlink/?LinkId=330212)から無料の試用アカウントを入手できます｡
