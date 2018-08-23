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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: c55dcf0736642690f245f680db5cb1620c2175e7
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41946485"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Azure Stack での Python による API バージョンのプロファイルの使用

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

## <a name="python-and-api-version-profiles"></a>Python と API バージョン プロファイル

Python SDK では、Azure Stack とグローバル Azure などの異なるクラウド プラットフォームをターゲットとする API バージョン プロファイルをサポートします。 ハイブリッド クラウド向けのソリューションの作成時に API プロファイルを使用できます。 Python SDK では、以下の API プロファイルをサポートします。

1. **latest**  
    プロファイルは、Azure Platform 内のすべてのサービス プロバイダーの最新の API バージョンをターゲットにします。
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    このプロファイルは、Azure Stack がサポートするリソース プロバイダーの API バージョンをターゲットにします。

    API プロファイルと Azure Stack の詳細については、「[Azure Stack での API バージョンのプロファイルの管理](azure-stack-version-profiles.md)」を参照してください。

## <a name="install-azure-python-sdk"></a>Azure Python SDK をインストールする

1.  Git を[公式サイト](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)からインストールします。
2.  Python SDK インストール手順については、[Python 開発者向け Azure](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python)に関する記事を参照してください。
3.  使用できない場合は、サブスクリプションを作成し、サブスクリプション ID を保存して後で使用します。 サブスクリプションの作成手順については、「[Azure Stack でオファーのサブスクリプションを作成する](../azure-stack-subscribe-plan-provision-vm.md)」をご覧ください。 
4.  サービス プリンシパルを作成し、その ID とシークレットを保存します。 Azure Stack 用のサービス プリンシパルの作成手順については、「[Azure Stack へのアクセスをアプリケーションに提供する](../azure-stack-create-service-principals.md)」を参照してください。 
5.  サブスクリプションでサービス プリンシパルのロールが共同作成者/所有者であることを確認します。 サービス プリンシパルへのロールの割り当て手順については、「[Azure Stack へのアクセスをアプリケーションに提供する](../azure-stack-create-service-principals.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure Stack で Python Azure SDK を使用するには、次の値を指定した後、環境変数に値を設定する必要があります。 環境変数を設定する方法については、表の後にある、オペレーティング システム別の手順を参照してください。 

| 値 | 環境変数 | 説明 |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| テナント ID | AZURE_TENANT_ID | Azure Stack の[テナント ID](../azure-stack-identity-overview.md) の値。 |
| クライアント ID | AZURE_CLIENT_ID | このドキュメントの前のセクションでサービス プリンシパルが作成されたときに保存した、サービス プリンシパル アプリケーション ID。 |
| サブスクリプション ID | AZURE_SUBSCRIPTION_ID | [サブスクリプション ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) は Azure Stack 内のオファーにアクセスするために必要です。 |
| クライアント シークレット | AZURE_CLIENT_SECRET | サービス プリンシパルの作成時に保存した、サービス プリンシパル アプリケーション シークレット 。 |
| Resource Manager エンドポイント | ARM_ENDPOINT | 「[Azure Stack Resource Manager エンドポイント](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)」をご覧ください。 |


## <a name="python-samples-for-azure-stack"></a>Azure Stack 向けの Python 例 

次のサンプルコードを利用することで､Azure Stack で仮想マシンに対する一般的な管理タスクを行うことができます｡

サンプルコードは以下を行う方法を示します｡

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

GitHub レポジトリ [virtual-machines-python-manage](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88) にある Python スクリプト **Hybrid/unmanaged-disks/example.py** の **run_example()** をご覧ください｡これらの操作を行うためのコードを確認できます｡

それぞれの操作には､それと分かるコメントと print 関数が付いています｡
これらの例は必ずしも上記一覧の順序通りでありません｡


## <a name="run-the-python-sample"></a>Python のサンプルコードを実行する

1.  [Python](https://www.python.org/downloads/) をインストールしていない場合は、インストールします。

    このサンプル (と SDK) は､Python 2.7 と 3.4､3.5､3.6 に対応しています｡

2.  Python 開発に対する一般的なレコメンデーションとしては､仮想環境を使用するということがあります｡ 
    詳細については、https://docs.python.org/3/tutorial/venv.html を参照してください。
    
    Python 3 の "venv" モジュール (Python 2.7 の場合は [virtualenv](https://pypi.python.org/pypi/virtualenv)) を使用して､仮想環境をインストールして初期化します｡

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  リポジトリの複製を作成します｡

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  pip を使用して依存関係をインストールします｡

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Azure Stack と連携させる[サービス プリンシパル](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)を作成します｡ サブスクリプションでサービスプリンシパルのロールが[共同作成者/所有者](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)であることを確認します｡

6.  次の環境変数を設定して､現在のシェルにエクスポートします｡ 

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  このサンプルを実行するには､Azure Stack マーケット プレイスに Ubuntu 16.04-LTS と WindowsServer 2012-R2-Datacenter イメージが存在している必要があります｡ これらは [Azure からダウンロード](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item)することも､あるいは [プラットフォーム イメージ リポジトリに追加](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image)することもできます｡

8. サンプルを実行します。

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>メモ

`virtual_machine.storage_profile.os_disk` を使用して仮想マシンの OS ディスクを取得することができます｡
その場合､望んでいることを行えるかもしれませんが､`OSDisk` オブジェクトが提供されることに注意してください｡
OS ディスクのサイズを更新する場合､必要になるのは､`example.py` のとき同様 `OSDisk` オブジェクトではなく､`Disk` オブジェクトです｡
`example.py` は以下を使用して `Disk` オブジェクトを取得します｡

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>次の手順

- [Azure Python 開発センター](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines 関係のドキュメント](https://azure.microsoft.com/services/virtual-machines/)
- [Virtual Machines のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Microsoft Azure のサブスクリプションをお持ちでない場合は､[ここ](http://go.microsoft.com/fwlink/?LinkId=330212)から無料の試用アカウントを入手できます｡
