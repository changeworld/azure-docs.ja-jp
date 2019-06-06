---
title: Azure Data Science Virtual Machine を使用する
description: Azure Notebooks で使用できるコンピューティング能力を拡張するために Azure Data Science Virtual Machine (DSVM) に接続します。
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: getroyer
ms.openlocfilehash: ab3b742d50cc141420f9bffa1961a6e170b99d2a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234344"
---
# <a name="use-azure-data-science-virtual-machines"></a>Azure Data Science Virtual Machine を使用する

既定では、プロジェクトは**無料のコンピューティング** レベルで実行しますが、このレベルは不正使用を回避するために、4 GB のメモリと 1 GB のデータに制限されています。 これらの制限をバイパスするには、Azure サブスクリプションでプロビジョニングした別の仮想マシンを使用します。 この目的での最適な選択肢は、**Data Science Virtual Machine for Linux (Ubuntu)** イメージを使用する Azure Data Science Virtual Machine (DSVM) です。 この DSVM では、Azure Notebooks に必要なすべてが事前構成されており、Azure Notebooks の **[実行]** ドロップダウン リストに自動的に表示されます。

> [!Note]
> Azure Notebooks は、Linux Ubuntu イメージを使用して作成された DSVM でのみサポートされています。 Azure Notebooks は、Windows 2012、Windows 2016、Linux CentOS イメージではサポートされていません。

## <a name="create-a-dsvm-instance"></a>DSVM インスタンスを作成する

新しい DSVM インスタンスを作成するには、[Ubuntu Data Science VM の作成](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)に関するページの指示に従います。 価格を含む詳細については、「[Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)」を参照してください。

## <a name="connect-to-the-dsvm"></a>DSVM に接続する

DSVM を作成したら、Azure Notebook プロジェクト ダッシュ ボードの **[実行]** ドロップダウン リストを選択し、適切な DSVM インスタンスを選択します。 次の条件が当てはまる場合、ドロップダウン リストには DSVM インスタンスが表示されます。

- 会社のアカウントなどの Azure Active Directory (AAD) を使用するアカウントで、Azure Notebooks にサインインしている。
- アカウントが Azure サブスクリプションに接続されている。
- そのサブスクリプションに、Data Science Virtual Machine for Linux (Ubuntu) イメージを使用する仮想マシン (少なくとも閲覧者アクセス権あり) が 1 つ以上含まれている。

![プロジェクト ダッシュボードのドロップダウン リストの Data Science Virtual Machine インスタンス](media/project-compute-tier-dsvm.png)

DSVM インスタンスを選択すると、Azure Notebooks によって、VM を作成したときに使用した特定のマシン資格情報が求められることがあります。

条件のいずれかを満たしていない場合でも、DSVM に接続することができます。 ドロップダウン リストで **[Direct Compute]\(直接コンピューティング\)** オプションを選択すると、名前 (一覧に表示する名前)、VM の IP アドレスとポート (通常は、JupyterHub がリッスンする既定のポートである 8000)、および VM 資格情報の入力を求められます。

![[Direct Compute]\(直接コンピューティング\) オプションのサーバー情報を収集するプロンプト](media/project-compute-tier-direct.png)

これらの値は Azure portal の [DSVM] ページで確認できます。

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>DSVM から Azure Notebooks ファイルへのアクセス

**無料のコンピューティング** レベルでファイル パスのパリティを保持するには、DSVM で一度に 1 つのプロジェクトしか開くことができません。 新しいプロジェクトを開くには、まず開いているプロジェクトをシャットダウンする必要があります。

![Azure Notebooks の [シャットダウン] ボタン](media/shutdown.png)

VM でプロジェクトが実行されると、ファイルは Jupyter サーバーのルート ディレクトリ (JupyterHub に表示されるディレクトリ) にマウントされ、既定の Azure Notebooks ファイルと置き換えられます。 Azure Notebooks UI の **[シャットダウン]** ボタンを使用して VM をシャットダウンすると、既定のファイルが復元されます。

## <a name="next-steps"></a>次の手順

DSVM の詳細については、[Azure Data Science Virtual Machines の概要](/azure/machine-learning/data-science-virtual-machine/overview)に関するページをご覧ください。
