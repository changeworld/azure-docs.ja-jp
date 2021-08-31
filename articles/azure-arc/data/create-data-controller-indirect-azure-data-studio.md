---
title: Azure Data Studio でデータ コントローラーを作成する
description: Azure Data Studio でデータ コントローラーを作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 4884730dc163b7720b5c2abb9c4f1fb529e5e2ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748727"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Azure Data Studio でデータ コントローラーを作成する

展開ウィザードとノートブックから Azure Data Studio を使用して、データ コントローラーを作成できます。


## <a name="prerequisites"></a>前提条件

- Kubernetes クラスターにアクセスし、デプロイ先の Kubernetes クラスターを示すように kubeconfig ファイルを構成しておく必要があります。
- `arcdata` 拡張機能を使用して、[クライアント ツール](install-client-tools.md) (**Azure Data Studio** や **Azure Arc** と呼ばれる Azure Data Studio の拡張機能 など) と Azure CLI をインストールする必要があります。
- Azure Data Studio で Azure にログインする必要があります。  これを行うには、「CTRL/Command + SHIFT + P」と入力してコマンド テキスト ウィンドウを開き、「**Azure**」と入力します。  **Azure:サインイン**」を検索して選びます。   パネルの右上にある [+] アイコンをクリックして、Azure アカウントを追加します。

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>展開ウィザードを使用して Azure Arc データ コントローラーをデプロイする

展開ウィザードを使用して Azure Arc データ コントローラーを作成するには、次の手順に従います。

1. Azure Data Studio の左側にあるナビゲーションで、[接続] タブをクリックします。
1. [接続] パネルの上部にある **[...]** ボタンをクリックし、 **[新しい展開...]** を選択します。
1. 新しい展開ウィザードで、 **[Azure Arc データ コントローラー]** を選択し、下部にある **[選択]** ボタンをクリックします。
1. 前提条件のツールが使用可能であり、必要なバージョンを満たしていることを確認します。 **[次へ]** をクリックします。
1. 既定の kubeconfig ファイルを使用するか、別のファイルを選択します。  **[次へ]** をクリックします。
1. Kubernetes クラスター コンテキストを選択します。 **[次へ]** をクリックします。
1. ターゲットの Kubernetes クラスターに応じて、展開構成プロファイルを選択します。 **[次へ]** をクリックします。
1. 目的のサブスクリプションとリソース グループを選択します。
1. Azure の場所を選択します。
   
   ここで選択した Azure の場所は、データ コントローラーと管理されるデータベース インスタンスに関する *メタデータ* が格納される Azure 内の場所です。 実際には、データ コントローラーとデータベース インスタンスは、その場所がどこにあるのかに関係なく、Kubernetes クラスターに作成されます。
   
   完了したら、 **[次へ]** をクリックします。

1. データ コントローラーの名前と、データ コントローラーが作成される名前空間の名前を入力します。

    データ コントローラーと名前空間の名前は、Kubernetes クラスターでカスタム リソースを作成するために使用されるため、[Kubernetes の名前付け規則](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names)に従っている必要があります。
    
    既に名前空間が存在する場合は、名前空間に他の Kubernetes オブジェクト (ポッドなど) がまだ含まれていなければ使用されます。名前空間が存在しない場合は、名前空間の作成が試みられます。  Kubernetes クラスターで名前空間を作成するには、Kubernetes クラスター管理者特権が必要です。  Kubernetes クラスター管理者特権を持っていない場合は、[Kubernetes ネイティブのツールを使用したデータ コントローラーの作成](./create-data-controller-using-kubernetes-native-tools.md)に関する記事の最初のいくつかの手順を実行するように、Kubernetes クラスター管理者に依頼してください。これらの手順は、このウィザードを完了する前に、Kubernetes 管理者が実行する必要があります。


1. データ コントローラーが展開されるストレージ クラスを選択します。 
1.  ユーザー名とパスワードを入力し、データ コントローラー管理者ユーザー アカウントのパスワードを確認します。 **[次へ]** をクリックします。

1. 展開構成を確認します。
1. **[展開]** をクリックして目的の構成を展開するか、または **[ノートブックへのスクリプト]** をクリックして展開手順を確認したり、ストレージ クラス名やサービスの種類などの必要な変更を行ったりします。 ノートブックの上部にある **[すべて実行]** をクリックします。

## <a name="monitoring-the-creation-status"></a>作成状態の監視

コントローラーの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、"arc" という名前のデータ コントローラーと Kubernetes 名前空間が作成されていることを前提としています。  異なる名前の名前空間/データ コントローラーを使用している場合は、"arc" をその名前で置き換えることができます。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。  これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。
