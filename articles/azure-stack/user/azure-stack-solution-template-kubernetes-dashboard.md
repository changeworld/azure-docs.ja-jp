---
title: Azure Stack で Kubernetes ダッシュボードにアクセスする |Microsoft Docs
description: Azure Stack で Kubernetes ダッシュボードにアクセスする方法について説明します
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
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 4e9df0d413b964b4a14cf9ca48db8b7956b441f9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482591"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Azure Stack で Kubernetes ダッシュボードにアクセスする 

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit* 
> [!Note]   
> Azure Stack 上の Kubernetes はプレビュー段階にあります。 Azure Stack の切断されたシナリオは、プレビューでは現在サポートされていません。 

Kubernetes には、基本的な管理操作に使用できる Web ダッシュボードが含まれています。 このダッシュボードでは、アプリケーションの基本的な正常性状態とメトリックの表示、サービスの作成とデプロイ、既存のアプリケーションの編集を行うことができます。 この記事では、Azure Stack 上で Kubernetes ダッシュボードを設定する方法について説明します。

## <a name="prerequisites-for-kubernetes-dashboard"></a>Kubernetes ダッシュボードの前提条件

* Azure Stack Kubernetes クラスター

    Kubernetes クラスターが Azure Stack にデプロイされている必要があります。 詳細については、「[Kubernetes のデプロイ](azure-stack-solution-template-kubernetes-deploy.md)」を参照してください。

* SSH クライアント

    クラスター内のマスター ノードに安全に接続するために、SSH クライアントが必要です。 Windows を使用している場合は [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) を使用できます。 Kubernetes クラスターをデプロイしたときに使用した秘密キーが必要です。

* FTP (PSCP)

    証明書をマスター ノードから Azure Stack 管理マシンに転送するには、SSH と SSH ファイル転送プロトコルをサポートする FTP クライアントも必要になる場合があります。 [FileZilla](https://filezilla-project.org/download.php?type=client) を使用できます。 Kubernetes クラスターをデプロイしたときに使用した秘密キーが必要です。

## <a name="overview-of-steps-to-enable-dashboard"></a>ダッシュボードを有効にする手順の概要

1.  クラスター内のマスター ノードから、Kubernetes の証明書をエクスポートします。 
2.  証明書を Azure Stack 管理マシンにインポートします。
2.  Kubernetes Web ダッシュボードを開きます。 

## <a name="export-certificate-from-the-master"></a>マスターからの証明書のエクスポート 

クラスター内のマスター ノードから、ダッシュボードの URL を取得できます。

1. Azure Stack のダッシュボードから、クラスターのマスターのパブリック IP アドレスとユーザー名を取得します。 情報の取得方法は以下のとおりです。

    - [Azure Stack ポータル](https://portal.local.azurestack.external/)にサインインします。
    - **[すべてのサービス]** > **[すべてのリソース]** の順に選択します。 クラスター リソース グループで、マスターを見つけます。 マスターの名前は `k8s-master-<sequence-of-numbers>` です。 

2. ポータルで、マスター ノードを開きます。 **パブリック IP** アドレスをコピーします。 **[接続]** をクリックして、**[VM ローカル アカウントを使用してログインする]** ボックスのユーザー名を取得します。 これは、クラスターを作成するときに設定したのと同じユーザー名です。 [接続] ブレードの一覧に表示されたプライベート IP アドレスではなく、パブリック IP アドレスを使用します。

3.  マスターに接続するため、SSH クライアントを開きます。 Windows で作業している場合は、[Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) を使用して接続を作成できます。 マスター ノードとユーザー名にはパブリック IP アドレスを使用し、クラスターの作成時に使用した秘密キーを追加します。

4.  ターミナルが接続されたら、「`kubectl`」と入力して Kubernetes コマンドライン クライアントを開きます。

5. 次のコマンドを実行します。

    ```Bash   
    kubectl cluster-info 
    ``` 
    ダッシュボードの URL を確認します。 次に例を示します。`https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  自己署名証明書を抽出し、PFX 形式に変換します。 次のコマンドを実行します。

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  **kube-system** 名前空間のシークレットの一覧を取得します。 次のコマンドを実行します。

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    kubernetes-dashboard-token-\<XXXXX> の値をメモしておきます。 

8.  トークンを取得し、保存します。 前の手順で取得したシークレット値で `kubernetes-dashboard-token-<####>` を更新します。

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>証明書のインポート

1. FileZilla を開き、マスター ノードに接続します。 以下のものが必要になります。

    - マスター ノードのパブリック IP
    - ユーザー名
    - プライベート シークレット
    - **SFTP - SSH ファイル転送プロトコル**を使用

2. `/etc/kubernetes/certs/client.pfx` と `/etc/kubernetes/certs/ca.crt` を Azure Stack 管理マシンにコピーします。

3. ファイルの場所を書き留めておきます。 その場所でスクリプトを更新してから、管理者特権のプロンプトで PowerShell を開きます。 更新されたスクリプトを実行します。  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Kubernetes ダッシュボードを開く 

1. Web ブラウザーのポップアップ ブロックを無効にします。

2. ブラウザーで、コマンド `kubectl cluster-info` の実行時にメモしておいた URL を参照します。 例: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy 
3. クライアント証明書を選択します。
4. トークンを入力します。 
5. マスター ノード上の Bash コマンド ラインに再接続し、`kubernetes-dashboard` にアクセス許可を付与します。 次のコマンドを実行します。

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    このスクリプトにより、`kubernetes-dashboard` にクラウド管理者権限が付与されます。 詳細については、「[RBAC 対応クラスターの場合](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)」を参照してください。

ダッシュボードを使用できます。 Kubernetes ダッシュボードの詳細については、[Kubernetes の Web UI ダッシュボード](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)に関するページを参照してください。 

![Azure Stack Kubernetes ダッシュボード](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>次の手順 

[Kubernetes を Azure Stack にデプロイする](azure-stack-solution-template-kubernetes-deploy.md)  

[Kubernetes クラスターを Marketplace に追加する (Azure Stack のオペレーター)](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Azure における Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
