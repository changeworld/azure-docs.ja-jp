---
title: インクルード ファイル
description: インクルード ファイル
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 6d2940712f8a76173de47badd45d7c7f0f0be05c
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825478"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Azure Dev Spaces 対応の Kubernetes クラスターを作成する

1. Azure Portal (http://portal.azure.com) にサインインします。
1. **[Create a resource]\(リソースの作成\)** を選択し、**[Kubernetes]** を検索して、**[Azure Kubernetes Service]** > **[作成]** を選択します。

   AKS クラスター の作成フォームの各見出しの下で、次の手順を実行します。

    - **PROJECT DETAILS (プロジェクトの詳細)**: Azure サブスクリプションと、新規または既存の Azure リソース グループを選択します。
    - **CLUSTER DETAILS (クラスターの詳細)**: AKS クラスターの名前、リージョン (現時点では EastUS、WestEurope、CanadaEast のいずれかを選択してください)、バージョン、DNS 名プレフィックスを入力します。
    - **[SCALE]\(スケール\)**: AKS エージェント ノードの VM サイズとノード数を選択します。 Azure Dev Spaces を初めてお使いになる場合、ノード数は 1 つあれば十分にさまざまな機能を試すことができます。 ノード数は、クラスターのデプロイ後、いつでも簡単に調整できます。 AKS クラスターの作成後に VM サイズを変更することはできないので注意してください。 ただし、AKS クラスターのデプロイ後にスケールアップする必要が生じた場合は、より大きな VM を使って新しい AKS クラスターを簡単に作成できます。Dev Spaces を使用して、その大きい方のクラスターに再デプロイすることができます。

   必ず Kubernetes バージョン 1.9.6 以降を選択してください。

   ![Kubernetes の構成設定](../media/common/Kubernetes-Create-Cluster-2.PNG)

   完了したら、**[Next: Networking] (次へ: ネットワーク)** を選択します。

1. [HTTP アプリケーションのルーティング] が有効になっていることを確認します。

   ![[HTTP アプリケーションのルーティング] の有効化](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > AKS クラスターを作成する際は、必ず [HTTP アプリケーションのルーティング] を有効にしてください。 この設定を後から変更することはできません。

1. 完了したら、**[Review + create] (レビュー + 作成)**、**[作成]** の順に選択します。
