---
title: Kubernetes を Azure Stack にデプロイする場合のトラブルシューティング | Microsoft Docs
description: Kubernetes を Azure Stack にデプロイする場合のトラブルシューティング方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 472dfc04cea65cab39d177bb214c417d229b71d2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956722"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Kubernetes (K8) を Azure Stack にデプロイする場合のトラブルシューティング

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

> [!Note]  
> Azure Stack 上の Kubernetes はプレビュー段階にあります。

以下の記事では、Kubernetes クラスターのトラブルシューティングについて説明します。 デプロイのアラートを確認し、デプロイに必要な要素によってデプロイの状態を確認することができます。 場合によっては、Azure Stack または Kubernetes をホストする Linux VM からデプロイ ログを収集する必要があります。 また、場合によっては、管理エンドポイントからログを取得するために Azure Stack 管理者と連携する必要があります。

## <a name="overview-of-deployment"></a>デプロイの概要

クラスターのトラブルシューティングを開始する前に、Azure Stack Kubernetes クラスターのデプロイ プロセスを確認することをお勧めします。 デプロイは、Azure Resource Manager ソリューション テンプレートを使用して VM を作成し、クラスター用の ACS Engine をインストールします。

### <a name="deployment-workflow"></a>デプロイのワークフロー

次の図は、クラスターをデプロイする一般的なプロセスを示しています。

![Kubernetes のデプロイ プロセス](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>デプロイメントの手順

1. マーケットプレース項目から入力パラメーターを収集します。

    次のような Kubernetes クラスターの設定に必要な値を入力します。
    -  **ユーザー名**: Kubernetes クラスターと DVM の一部である Linux Virtual Machines のユーザー名。
    -  **SSH 公開キー**: Kubernetes クラスターと DVM の一部として作成されたすべての Linux マシンの承認に使用されるキー。
    -  **サービス プリンシパル**:Kubernetes Azure クラウド プロバイダーによって使用される ID。 サービス プリンシパルを作成したときにアプリケーション ID として識別されたクライアント ID。 
    -  **クライアント シークレット**: サービス プリンシパルを作成するときに作成されたキー。

2. デプロイの VM とカスタム スクリプト拡張機能を作成します。
    -  マーケットプレースの Linux イメージ **Ubuntu Server 16.04-LTS** を使用して、デプロイの Linux VM を作成します。
    -  マーケットプレースからカスタム スクリプト拡張機能をダウンロードして実行します。 このスクリプトは **Custom Script for Linux 2.0** です。
    -  DVM カスタム スクリプトを実行します。 スクリプトでは次のタスクを実行します。
        1. Azure Resource Manager メタデータ エンドポイントからギャラリー エンドポイントを取得します。
        2. Azure Resource Manager メタデータ エンドポイントからアクティブなディレクトリ リソース ID を取得します。
        3. ACS Engine 用の API モデルを読み込みます。
        4. Kubernetes クラスターに ACS Engine をデプロイし、Azure Stack クラウド プロファイルを `/etc/kubernetes/azurestackcloud.json` に保存します。
3. マスター VM を作成します。

4. カスタム スクリプト拡張機能をダウンロードして実行します。

5. マスター スクリプトを実行します。

    スクリプトでは次のタスクを実行します。
    - kubelet などの etcd、Docker、および Kubernetes リソースをインストールします。 etcd は、複数のマシンのクラスターにデータを格納することができる分散型のキー値ストアです。 Docker は、コンテナーという最小限のオペレーティング システム レベルの仮想化をサポートしています。 Kubelet は、各 Kubernetes ノード上で動作するノード エージェントです。
    - etcd サービスを設定します。
    - kubelet サービスを設定します。
    - kubelet を開始します。 このタスクには、次の手順が含まれます。
        1. API サービスを開始します。
        2. コントローラー サービスを開始します。
        3. スケジューラ サービスを開始します。
6. エージェント VM を作成します。

7. カスタム スクリプト拡張機能をダウンロードして実行します。

7. エージェント スクリプトを実行します。 エージェントのカスタム スクリプトでは次のタスクを実行します。
    - etcd をインストールします
    - kubelet サービスを設定します
    - Kubernetes クラスターを結合します

## <a name="steps-for-troubleshooting"></a>トラブルシューティングの手順

Kubernetes クラスターをサポートしている VM に関するログを収集できます。 デプロイ ログを確認することもできます。 場合によっては、使用する必要がある Azure Stack のバージョンを確認し、デプロイに関するログを Azure Stack から取得するために、Azure Stack 管理者に相談する必要があります。

1. Kubernetes クラスターで、[デプロイの状態](#review-deployment-status)を確認し、マスター ノードの[ログを取得](#get-logs-from-a-vm)します。
2. Azure Stack の最新バージョンを使用していることを確認します。 使用しているバージョンがわからない場合は、Azure Stack の管理者にお問い合わせください。 Kubernetes クラスターのマーケットプレースの項目 0.3.0 には、Azure Stack バージョン 1808 以降が必要です。
3.  VM 作成ファイルを確認します。 次の問題が発生している可能性があります。  
    - 公開キーが無効な場合があります。 作成したキーを確認してください。  
    - VM 作成によって内部エラーまたは作成エラーがトリガーされることがあります。 エラーは、Azure Stack サブスクリプションの容量制限など、さまざまな要因で起こることがあります。
    - VM の完全修飾ドメイン名 (FQDN) は重複するプレフィックスで始まっていることを確認してください。
4.  VM が **[OK]** の場合は、DVM を評価します。 DVM にエラー メッセージがある場合:

    - 公開キーが無効な場合があります。 作成したキーを確認してください。  
    - 特権付きエンド ポイントを使用して Azure Stack のログを取得するには、Azure Stack 管理者に問い合わせる必要があります。 詳細については、「[Azure Stack の診断ツール](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics)」を参照してください。
5. デプロイに関して質問がある場合は、[Azure Stack フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)で質問を投稿するか、他の人が既に回答を受け取っていないか確認することができます。 

## <a name="review-deployment-status"></a>デプロイの状態を確認する

Kubernetes クラスターをデプロイする際には、デプロイの状態を確認して、問題をチェックすることができます。

1. [Azure Stack ポータル](https://portal.local.azurestack.external)を開きます。
2. **[リソース グループ]** を選択し、Kubernetes クラスターのデプロイ時に使用したリソース グループの名前を選択します。
3. **[デプロイ]** を選択し、**[デプロイ名]** を選択します。

    ![トラブルシューティング](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  トラブルシューティングのウィンドウを確認します。 デプロイ済みの各リソースから、次の情報が提供されます。
    
    | プロパティ | 説明 |
    | ----     | ----        |
    | リソース | リソースの名前。 |
    | type | リソース プロバイダーとリソースの種類。 |
    | Status | 項目の状態。 |
    | TimeStamp | 時刻のタイムスタンプ (UTC)。 |
    | 操作の詳細 | 操作に関係するリソース プロバイダー、リソースのエンド ポイント、リソースの名前などの操作の詳細。 |

    各項目には緑色または赤色の状態アイコンが表示されます。

## <a name="get-logs-from-a-vm"></a>VM からログを取得する

ログを生成するには、クラスターのマスター VM に接続し、bash プロンプトを開いた後、スクリプトを実行する必要があります。 マスター VM はクラスター リソース グループ内にあり、`k8s-master-<sequence-of-numbers>` という名前です。 

### <a name="prerequisites"></a>前提条件

Azure Stack の管理に使用するマシンには bash プロンプトが必要です。 bash を使用して、ログにアクセスするスクリプトを実行します。 Windows マシンでは、git と共にインストールした bash プロンプトを使用することができます。 git の最新バージョンを入手するには、[Git のダウンロード](https://git-scm.com/downloads)に関するページを参照してください。

### <a name="get-logs"></a>ログを取得する

ログを取得するには、次の手順を実行します。

1. bash プロンプトを開きます。 Windows マシンで Git を使用している場合、`c:\programfiles\git\bin\bash.exe` のパスから bash プロンプトを開くことができます。
2. 次の bash コマンドを実行します。

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > Windows では `sudo` を実行する必要はありません。 代わりに、`chmod 744 getkuberneteslogs.sh` を使用できます。

3. 同じセッションで、環境に合わせてパラメーターを更新して次のコマンドを実行します。

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

4. パラメーターを確認し、環境に基づいて値を設定します。
    | パラメーター           | 説明                                                                                                      | 例                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i、--identity-file | Kubernetes マスター VM に接続するための RSA 秘密キー ファイル。 これらのキーは `-----BEGIN RSA PRIVATE KEY-----` で始まる必要があります。 | C:\data\privatekey.pem                                                        |
    | -h、--host          | Kubernetes クラスター マスター VM のパブリック IP または完全修飾ドメイン名 (FQDN)。 VM 名は `k8s-master-` で始まります。                       | IP:192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u、--user          | Kubernetes クラスター マスター VM のユーザー名。 この名前は、マーケットプレース項目を構成するときに設定します。                                                                    | azureuser                                                                     |
    | -d、--vmdhost       | DVM のパブリック IP または FQDN。 VM 名は `vmd-` で始まります。                                                       | IP:192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   パラメーター値を追加すると、次のようなコードになります。

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    実行が成功するとログが作成されます。

    ![生成されたログ](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. コマンドによって作成されたフォルダー内のログを取得します。 このコマンドでは、新しいフォルダーが作成され、それらにタイムスタンプが付けられます。
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>次の手順

[Kubernetes を Azure Stack にデプロイする](azure-stack-solution-template-kubernetes-deploy.md)

[Kubernetes クラスターを Marketplace に追加する (Azure Stack のオペレーター)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure における Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
