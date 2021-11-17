---
title: パブリックな環境の構成と使用
description: この記事では、Azure DevTest Labs でパブリックな環境 (Git リポジトリの Azure Resource Manager テンプレート) を構成して使用する方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 3570f88ae8fe88740721b04783a8689e22c7bb7d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286376"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Azure DevTest Labs でのパブリックな環境の構成と使用
Azure DevTest Labs には、環境の作成に使用できる[Azure Resource Manager テンプレートのパブリック リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments)があります。 外部 GitHub ソースに自分で接続する必要はありません。 このリポジトリには、Azure Web Apps、Service Fabric クラスター、開発の SharePoint ファームなど、頻繁に使用されるテンプレートが含まれています。 この機能は、作成するすべてのラボに含まれている成果物のパブリック リポジトリに似ています。 環境リポジトリは、事前に作成された環境テンプレートと最小入力パラメーターを提供します。 これらのテンプレートを使用すると、ラボ内でサービスとしてのプラットフォーム (PaaS) リソースを使い始める作業がスムーズになります。
  
## <a name="configuring-public-environments"></a>パブリックな環境の構成
ラボ所有者は、ラボの作成時に、ラボ用のパブリックな環境リポジトリを有効にできます。 ラボ用のパブリックな環境を有効にするには、ラボの作成時に、**[パブリックな環境]** フィールドで **[On]** を選択します。 

![新しいラボのパブリックな環境の有効化](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)

既存のラボでは、パブリックな環境リポジトリは有効になっていません。 リポジトリでテンプレートを使用できるように手動で有効にします。 Resource Manager テンプレートを使用して作成されたラボの場合も、リポジトリは既定で無効になっています。

ラボでパブリックな環境を有効または無効にすることも、次の手順を使用してラボのユーザーが特定の環境のみを使用できるようにすることもできます。 

1. ラボの **[構成とポリシー]** を選択します。 
2. **[仮想マシンのベース]** セクションで、 **[パブリックな環境]** を選択します。
3. ラボでパブリックな環境を有効にするには、**[はい]** を選択します。 それ以外の場合は、 **[いいえ]** を選択します。 
4. パブリックな環境を有効にした場合は、既定でリポジトリ内のすべての環境が有効になります。 環境を選択解除すると、その環境をラボ ユーザーが使用できないようにすることができます。 

![[パブリック環境] ページを示すスクリーンショット。](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>ラボ ユーザーによる環境テンプレートの使用
ラボ ユーザーは、ラボ ページのツール バーから **[+ 追加]** を選択することで、有効な環境テンプレートのリストから新しい環境を作成できます。 ベースのリストの一番上にあるのは、ラボ管理者によって有効にされたパブリックな環境のテンプレートです。

![パブリック環境テンプレートを示すスクリーンショット。](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>次のステップ
このリポジトリは、投稿できるオープンソースのリポジトリです。 頻繁に使用される独自の便利な Resource Manager テンプレートを追加するには、リポジトリに対して pull request を送信します。
