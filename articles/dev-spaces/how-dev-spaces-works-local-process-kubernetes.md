---
title: Local Process with Kubernetes のしくみ
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Local Process with Kubernetes を使用して開発用コンピューターを Kubernetes クラスターに接続するプロセスについて説明します
keywords: Local Process with Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 443783eb7f5359318cf8efbec8b6466a80fa1e85
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316263"
---
# <a name="how-local-process-with-kubernetes-works"></a>Local Process with Kubernetes のしくみ

Local Process with Kubernetes を使用すると、開発用のコンピューター上でコードの実行とデバッグを行いながら、残りのアプリケーションやサービスが置かれている Kubernetes クラスターとの接続を保つことができます。 たとえば、多数のサービスやデータベースが相互に依存している大規模なマイクロサービス アーキテクチャがある場合、開発用コンピューター上のそれらの依存関係をレプリケートするのは困難な場合があります。 さらに、内部ループの開発中にコードを変更するたびにコードをビルドして Kubernetes クラスターにデプロイすると、遅くなり、時間がかかり、デバッガーで使用するのが難しくなることがあります。

Local Process with Kubernetes を使用すると、代わりに開発用コンピューターとクラスターの間に直接接続が作成されることにより、コードをビルドしてクラスターにデプロイする必要がなくなります。 デバッグ中に開発用コンピューターをクラスターに接続すると、Docker または Kubernetes の構成を作成することなく、完全なアプリケーションのコンテキストで、サービスを迅速にテストおよび開発できます。

Local Process with Kubernetes では、接続された Kubernetes クラスターと開発用コンピューターの間でトラフィックがリダイレクトされます。 このトラフィックのリダイレクトにより、開発用コンピューター上のコードと、Kubernetes クラスターで実行されているサービスは、同じ Kubernetes クラスター内に存在するかのように通信できます。 Local Process with Kubernetes には、開発用コンピューターの Kubernetes クラスター内のポッドに利用できる環境変数とマウントされたボリュームをレプリケートする方法も用意されています。 開発用コンピューター上の環境変数およびマウントされたボリュームにアクセスできると、それらの依存関係を手動でレプリケートすることなく、コードの作業をすばやく行うことができます。

## <a name="using-local-process-with-kubernetes"></a>Local Process with Kubernetes の使用

Local Process with Kubernetes を使用するには、[Azure Dev Spaces][azds-vs-code] と [Azure Kubernetes Service][az-aks-vs-code] の拡張機能をインストールした [Visual Studio Code][vs-code] を macOS または Windows 10 で実行し、[Azure Dev Spaces CLI をインストールする][azds-cli]必要があります。 また、Windows 10 上の [Visual Studio 2019][visual-studio] に *ASP.NET および Web の開発* および *Azure の開発*ワークロードをインストールして *AzureDevSpacesTools.LocalKubernetesDebugging* プレビュー機能フラグを有効にし、[Azure Dev Spaces CLI をインストールする][azds-cli]のでもかまいません。 Local Process with Kubernetes を使用して Kubernetes クラスターへの接続を確立するときに、クラスター内の既存のポッドとの間でやり取りされるすべてのトラフィックを開発用コンピューターにリダイレクトするかどうかを選択できます。

> [!NOTE]
> Local Process with Kubernetes を使用するときは、開発用コンピューターにリダイレクトするサービスの名前の指定を求められます。 このオプションを利用して、リダイレクト用のポッドを簡単に見分けることができます。 Kubernetes クラスターと開発用コンピューターの間のすべてのリダイレクトは、ポッドを対象としています。

クラスターへの接続が確立された Local Process with Kubernetes では、次のことが行われます。

* ユーザーは、クラスター上で置き換えるサービス、コードに使用する開発用コンピューター上のポート、1 回限りのアクションとしてのコードに対する起動タスクを構成するよう求められます。
* クラスター上のポッド内のコンテナーが、開発用コンピューターにトラフィックをリダイレクトするリモート エージェント コンテナーに置き換えられます。
* 開発用コンピューターからのトラフィックを、クラスター内で実行されるリモート エージェントに転送するために、開発用コンピューター上で [kubectl port-forward][kubectl-port-forward] を実行します。
* リモート エージェントを使用してクラスターから環境情報を収集します。 この環境情報には、環境変数、表示可能なサービス、ボリューム マウント、シークレット マウントが含まれます。
* 開発用コンピューター上のサービスが、クラスター上で実行されているかのように同じ変数にアクセスできるよう、Visual Studio または Visual Studio Code の環境が設定されます。  
* クラスター上のサービスが開発用コンピューター上のローカル IP アドレスにマップされるように、hosts ファイルが更新されます。 開発用コンピューター上で実行されているコードは、これらの hosts ファイルのエントリを通じて、クラスター内で実行されている別のサービスに要求を送信できるようになります。 Local Process with Kubernetes がクラスターに接続するときに、hosts ファイルを更新できるよう、開発用コンピューター上の管理者アクセス権が求められます。
* 開発用コンピューター上でコードの実行とデバッグが開始されます。 必要な場合は、開発用コンピューターで必要なポートを現在使用しているサービスまたはプロセスが停止されて、ポートが解放されます。

クラスターへの接続が確立された後、ユーザーは、コンテナー化を使用せずに、コンピューター上でネイティブにコードを実行してデバッグすることができ、コードではクラスターの残りの部分と直接対話できます。 リモート エージェントが受信するネットワーク トラフィックは、ネイティブに実行されているコードがそのトラフィックを受け入れて処理できるよう、接続時に指定されたローカル ポートにリダイレクトされます。 ご利用のクラスターにある環境変数、ボリューム、シークレットは、開発用コンピューター上で実行されているコードから利用できるようになります。 また、Local Process with Kubernetes によって開発用コンピューターに追加された hosts ファイルのエントリとポート転送により、コードでは、クラスター側のサービス名を使用して、クラスター上で実行されているサービスにネットワーク トラフィックを送信することができます。そのトラフィックは、クラスターで実行されているサービスに転送されます。 接続されている間は常に、開発用コンピューターとクラスターの間でトラフィックがルーティングされます。

## <a name="diagnostics-and-logging"></a>診断とログ記録

Local Process with Kubernetes を使用してクラスターに接続しているときは、クラスターからの診断ログが、開発用コンピューターの[一時ディレクトリ][azds-tmp-dir]に記録されます。 Visual Studio Code を使用している場合は、*Show diagnostic info* コマンドを使用して、クラスターから現在の環境変数と DNS エントリを出力することもできます。

## <a name="next-steps"></a>次のステップ

Local Process with Kubernetes を使用してローカル環境の開発用コンピューターをクラスターに接続する方法については、「[Visual Studio Code を使用して Local Process with Kubernetes を利用する][local-process-kubernetes-vs-code]」および「[Visual Studio を使用して Local Process with Kubernetes を利用する][local-process-kubernetes-vs]」を参照してください。

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download