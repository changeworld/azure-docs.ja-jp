---
title: 開発用コンピューターと AKS クラスターを接続する方法
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces を使用して開発用コンピューターを Azure Kubernetes Service クラスターに接続するプロセスについて説明します。
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241341"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>開発用コンピューターと AKS クラスターを接続する方法

Azure Dev Spaces を使用すると、開発用コンピューターを AKS クラスターに接続し、AKS クラスター上で実行されているかのように開発用コンピューター上でコードを実行したりデバッグしたりすることができます。 Azure Dev Spaces は、開発マシンとクラスターとの間でリモート エージェントとしてトラフィックをリダイレクトするポッドをクラスター上で実行することにより、接続された AKS クラスターとの間でトラフィックをリダイレクトします。 このようにトラフィックをリダイレクトすることで、開発用コンピューター上のコードと、AKS クラスターで実行されているサービスとが、同じ AKS クラスター内にあるかのように通信できます。 また、この接続により、開発用コンピューター上のコンテナーの有無にかかわらず、コードを実行、デバッグすることができます。 開発用コンピューターをクラスターに接続することで、アプリケーションの開発期間を短縮し、エンドツーエンドのテストが実行しやすくなります。

## <a name="connecting-to-your-cluster"></a>クラスターに接続する

既存の AKS クラスターへの接続には、MacOS または Windows 10 上で実行されている、[Azure Dev Spaces][azds-vs-code] 拡張機能がインストールされた [Visual Studio Code][vs-code] を使用します。 接続を確立する際に、クラスター内の新しいポッドまたは既存のポッドとの間でやり取りされるすべてのトラフィックを開発用コンピューターにリダイレクトするかどうかを選択できます。

> [!NOTE]
> Visual Studio Code を使用してクラスターに接続しようとすると、開発用コンピューターにサービスをリダイレクトするオプションが Azure Dev Spaces 拡張機能によって表示されます。 このオプションを利用して、リダイレクト用のポッドを簡単に見分けることができます。 AKS クラスターと開発用コンピューターとの間のリダイレクトはすべて、ポッドを対象としています。

クラスターに接続するために、クラスター上で Azure Dev Spaces を有効にする必要はありません。 その代わりに、Azure Dev Spaces 拡張機能が、クラスターへの接続を確立する際に次の処理を実行します。

* AKS クラスター上のポッド内のコンテナーを、開発用コンピューターにトラフィックをリダイレクトするリモート エージェント コンテナーに置き換えます。 Azure Dev Spaces は、新しいポッドをリダイレクトする際、リモート エージェントを含んだ新しいポッドを AKS クラスターに作成します。
* 開発用コンピューターからのトラフィックを、クラスター内で実行されるリモート エージェントに転送するために、開発用コンピューター上で [kubectl port-forward][kubectl-port-forward] を実行します。
* リモート エージェントを使用してクラスターから環境情報を収集します。 この環境情報には、環境変数、表示可能なサービス、ボリューム マウント、シークレット マウントが含まれます。
* 開発用コンピューター上のサービスが、クラスター上で実行されているかのように同じ変数にアクセスできるよう、Visual Studio Code ターミナルで環境をセットアップします。  
* hosts ファイルを更新して、AKS クラスター上のサービスを開発用コンピューター上のローカル IP アドレスにマップします。 開発用コンピューター上で実行されているコードは、これらの hosts ファイルのエントリを通じて、クラスター内で実行されている別のサービスに要求を送信できるようになります。 Azure Dev Spaces がクラスターに接続する際、hosts ファイルを更新できるよう、開発用コンピューター上の管理者アクセス権が求められます。

クラスター上で Azure Dev Spaces を有効にした場合は、[Azure Dev Spaces によって提供されるトラフィック リダイレクト][how-it-works-routing]を使用することもできます。 Azure Dev Spaces によって提供されるトラフィック リダイレクトを使用すると、子開発スペースで実行されるサービスのコピーに接続することができます。 子開発スペースを使用すれば、親開発スペースで行われている他の処理に支障を来さずに済みます。リダイレクトするのは、子スペースのサービス インスタンスを対象とするトラフィックだけで、親スペースのサービス インスタンスは一切変更されないためです。

クラスターへの接続後は、サービスを開発用コンピューター上で実行しているかどうかに関係なく、トラフィックが開発用コンピューターにルーティングされます。

## <a name="running-code-on-your-development-computer"></a>開発用コンピューター上でコードを実行する

AKS クラスターへの接続を確立した後は、あらゆるコードを、コンテナー化することなくネイティブに自分のコンピューター上で実行できます。 リモート エージェントが受信するネットワーク トラフィックは、ネイティブに実行されているコードがそのトラフィックを受け入れて処理できるよう、接続時に指定されたローカル ポートにリダイレクトされます。 ご利用のクラスターにある環境変数、ボリューム、シークレットは、開発用コンピューター上で実行されているコードから利用できるようになります。 また、Azure Dev Spaces が開発用コンピューターに追加した hosts ファイルのエントリとポート フォワーディングによって、コードは、クラスター側のサービス名を使用して、クラスター上で実行されているサービスにネットワーク トラフィックを送信することができます。そのトラフィックは、クラスターで実行されているサービスに転送されます。

コードは開発用コンピューター上で実行されるため、普段開発に使っている任意のツールを使用して自由に、コードを実行したりデバッグしたりすることができます。 接続されている間は常に、開発用コンピューターとクラスターの間でトラフィックがルーティングされます。 この永続的接続によって、必要に応じて何回でも、接続を再確立することなくコードを起動、停止、再起動することができます。

また、Azure Dev Spaces には、AKS クラスター内のポッドからアクセスできる環境変数とマウントされたファイルを、*azds-local.env* ファイルを通じて開発用コンピューターにレプリケートする手段が用意されています。 このファイルを使用して、新しい環境変数やボリューム マウントを作成することもできます。

## <a name="additional-configuration-with-azds-localenv"></a>azds-local.env を使用したその他の構成

*azds-local.env* ファイルを使用すると、AKS クラスター内のポッドからアクセスできる環境変数とマウントされたファイルをレプリケートすることができます。 *azds-local.env* ファイルでは、次のアクションを指定できます。

* ボリュームをダウンロードし、そのボリュームへのパスを環境変数として設定する。
* 任意のボリュームから個々のファイルまたは一連のファイルをダウンロードし、それを開発用コンピューター上でマウントする。
* 接続先のクラスターに関係なくサービスを利用できるようにする。

*azds-local.env* ファイルの例を次に示します。

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

既定の *azds-local.env* ファイルは自動的には作成されません。プロジェクトのルートに手動でファイルを作成する必要があります。

## <a name="diagnostics-and-logging"></a>診断とログ記録

AKS クラスターに接続しているときは、クラスターからの診断ログが、開発用コンピューターの[一時ディレクトリ][azds-tmp-dir]に記録されます。 Visual Studio Code を使用し、*Show diagnostic info* コマンドを使用して、AKS クラスターから現在の環境変数と DNS エントリを出力することもできます。

## <a name="next-steps"></a>次のステップ

実際にローカル開発用コンピューターを AKS クラスターに接続する方法については、「[開発用マシンを AKS クラスターに接続する][connect]」を参照してください。

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
