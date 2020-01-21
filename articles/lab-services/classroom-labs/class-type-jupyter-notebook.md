---
title: Python と Jupyter Notebooks を使用してデータ サイエンスを教えるためのラボを設定する |Microsoft Docs
description: Python と Jupyter Notebooks を使用してデータサイエンスを教えるためのラボを設定する方法について説明します。
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 5bf54b6975475810650aeaee4b477e60255757bf
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75530682"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Python と Jupyter Notebook を使用してデータ サイエンスを教えるためのラボを設定する

この記事では、[Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) を使用する方法を学生に教えるために必要なツールを使用して、Lab Services でテンプレート マシンを設定する方法について説明します。  Jupyter Notebooks は、ノートブックと呼ばれる単一のキャンバス上でリッチ テキストと実行可能な [Python](https://www.python.org/) ソース コードを簡単に組み合わせることができるオープンソース プロジェクトです。  ノートブックを実行すると、入力と出力の線形レコードが生成されます。  これらの出力には、テキスト、情報テーブル、散布図などを含めることができます。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントを設定するためのチュートリアル](tutorial-setup-lab-account.md)を参照してください。  既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

以下の表に記載されているラボ アカウントの設定を有効にします。 Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Marketplace イメージを指定する](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| ------------------- | ------------ |
| Marketplace イメージ | ラボ アカウント内で [Data Science Virtual Machine - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) イメージを使用できるようにします。 |

>[!TIP]
>この記事では、Windows Server オペレーティング システムを使用するテンプレート マシンの構成に焦点を当てて説明します。  Azure Marketplace にある [Data Science Virtual Machine for Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) イメージまたは [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) イメージを使用して、Python および Jupyter Notebooks でデータ サイエンス クラスを設定することもできます。

### <a name="lab-settings"></a>ラボの設定

クラスルーム ラボを設定するときは、次の表の設定を使用します。  クラスルーム ラボの作成方法の詳細については、[クラスルーム ラボの設定に関するチュートリアル](tutorial-setup-classroom-lab.md)を参照してください。

| ラボの設定 | 値/説明 |
| ------------ | ------------------ |
|仮想マシンのサイズ| Small GPU (Compute)。 このサイズは、人工知能やディープ ラーニングのような、コンピューティング集中型およびネットワーク集中型のアプリケーションに最適です。 |
|仮想マシン イメージ| Data Science Virtual Machine - Windows 2016|

## <a name="template-machine"></a>テンプレート マシン

[Data Science Virtual Machine - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) イメージには、この種類のクラスに必要なディープ ラーニング フレームワークとツールが用意されています。  このイメージには、Jupyter Notebooks と Visual Studio Code が含まれています。  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) は、データ サイエンティストが生データの取得、計算の実行、および結果の表示をすべて同じ環境で行えるようにする Web アプリです。  テンプレート マシンの場合、Web アプリケーションはローカルで実行されます。  [Visual Studio Code](https://code.visualstudio.com/) は、ノートブックを作成およびテストする際に、豊富な対話型エクスペリエンスを提供する統合開発環境です。  詳細については、「[Visual Studio Code での Jupyter Notebooks の使用](https://code.visualstudio.com/docs/python/jupyter-support)」を参照してください。

クラスを設定する際の残りのタスクは、ローカル ノートブックを提供することです。  Azure Machine Learning サンプルの使用方法については、[Jupyter Notebooks を使用して環境を構成する方法](../../machine-learning/how-to-configure-environment.md#jupyter)に関するセクションをご覧ください。  テンプレート マシンで独自のノートブックを提供することもできます。  テンプレートが発行されると、ノートブックはすべての学生用マシンにコピーされます。

## <a name="cost-estimate"></a>コストの見積もり

このクラスの考えられるコスト見積もりを検討しましょう。  クラスの生徒数は 25 人とします。  予定された授業時間は 20 時間です。  また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。  ここで選択した仮想マシンのサイズは、139 ラボ ユニットの Small GPU (コンピューティング) です。

このクラスで考えられるコスト見積もりの例を次に示します。

25人の学生 \* (予定された 20 時間 + 割り当てられた 10 時間) \* 139 ラボ ユニット \* 0.01 USD/時間 = 1042.5 USD

価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="conclusion"></a>まとめ

この記事では、Jupyter Notebooks クラス用のラボを作成する手順について説明しました。 他の機械学習クラスにも同様のセットアップを使用できます。

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)
