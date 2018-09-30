---
title: Azure Machine Learning の開発環境 | Microsoft Docs
description: Azure Machine Learning サービスで使用できる開発環境の概要。 Python 3 は開発環境の唯一の要件ですが、Conda 環境の使用もお勧めします。 開発ツールについては、Jupyter Notebook、Azure Notebooks、IDE/コード エディターをお勧めします。
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 260e209bcf00396ee545851684038578c4fd148a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971044"
---
# <a name="development-environment-for-azure-machine-learning"></a>Azure Machine Learning の開発環境 

Azure Machine Learning サービスで使用できる開発環境について説明します。 

Azure Machine Learning サービスは、プラットフォームに依存せず、特定の開発環境を必要としません。 必要なのは __Python 3__ で、分離環境を作成するためには、__Conda__ を使用することをお勧めします。 __これらの要件を満たしている開発環境が既にある場合は__、このドキュメントをスキップして[開発環境の構成](how-to-configure-environment.md)に関するドキュメントに移動することができます。

このドキュメントの残りの部分では、推奨される開発環境について説明します。

* __Jupyter Notebook__
* __Azure Notebooks__
* __統合開発環境 (IDE) とコード エディター__
* __データ サイエンス仮想マシン__

Notebook と IDE はどちらも拡張可能なため、これらの環境を比較するのは困難です。 たとえば、一部の IDE は、Jupyter Notebook のクライアントとして使用できます。 一般的に言えば、__Notebook__ は__対話型の実験__と__視覚化__のために設計されています。 __IDE エディターとコード エディター__は、__コード品質を向上__し、バージョン管理などの__外部システムと統合__するためのツールを提供します。

> [!TIP]
> ある環境を使用しても、他の環境が利用できなくなるわけではありません。 Notebook や IDE は単なるツールで、必要に応じて混在させることができます。 たとえば、Notebook で実験を開始し、IDE で編集およびデバッグするために Python スクリプトにエクスポートすることができます。
>
> これが、Data Science Virtual Machine で Jupyter Notebook と複数の一般的な Python IDE を提供する理由です。

## <a name="jupyter-notebooks"></a>Jupyter Notebooks

Jupyter Notebook は、[Jupyter プロジェクト](https://jupyter.org/)の一部です。 これらは、ライブ コードと説明のテキストとグラフィックスが混在するドキュメントを作成する対話型のコーディング エクスペリエンスを提供することに重点を置いています。 Jupyter Notebook は、さまざまなプラットフォームにインストールできます。

独自の Jupyter Notebook のインストール持つことで、必要に応じて環境をインストールして構成することができます。 ただし、ご自分で責任を持ってシステムをメンテナンスする必要があります。

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (プレビュー) は、Azure クラウドにおける Notebook 環境です。 これは、Jupyter をベースにしており、一般的なライブラリが事前に読み込まれている環境を提供します。 Azure Notebooks には Azure Machine Learning SDK が既にインストールされているため、ほぼ設定なしで実験を開始できます。

Azure Notebooks は、Notebook を共有するプロセスも簡略化します。 Azure Notebooks インターフェイスから、Notebook への URL を共有したり、ライブラリを公開したり、ソーシャル メディアで共有したりできます。

Azure Notebooks の欠点は、環境を完全に制御できず、必要なカスタム ソフトウェアをインストールできない場合があることです。 これは共有環境でもあるため、Notebook の実行が専用の Jupyter Notebook のインストールで実行する場合より遅くなる可能性があります。

## <a name="ides-and-code-editors"></a>IDE エディターとコード エディター

Azure Machine Learning で動作する多くの IDE エディターとコード エディターがあります。 唯一のソフトウェア要件は Azure Machine Learning SDK です。これは `pip` ユーティリティを使用してインストールできます。

[Visual Studio Code](https://code.visualstudio.com/) は、Python 言語と Azure Machine Learning の両方で動作するツールを提供しているため、お勧めします。 これは Linux、macOS、および Windows プラットフォームで使用できます。

## <a name="data-science-virtual-machine"></a>データ サイエンス仮想マシン

Data Science Virtual Machine (DSVM) は、前述の環境を組み合わせたものです。 これは、Jupyter Notebook、Visual Studio Code、および Azure Machine Learning SDK が事前にインストールされている Azure プラットフォーム上の VM です。 VM の作成は Azure Notebooks よりも複雑ですが、マシンを最初から設定するよりは複雑ではありません。 VM イメージに必要なソフトウェアが事前にインストールされているため、VM が作成されたらすぐに、Azure Machine Learning で実験を開始できます。

DSVM では、CPU、GPU、およびメモリなどの必要なコンピューティング リソースを選択できます。 PyCharm などのその他のエディターに加え、TensorFlow、Keras、PyTorch などの一般的な機械学習ソフトウェアも事前インストールされています。 必要なソフトウェアがインストールされていない場合は、ご自分でインストールできます。

事前にインストールされているものの詳細については、[Data Science Virtual Machine の概要](../data-science-virtual-machine/overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

これで、開発環境について学習できました。次は、[開発環境の構成方法](how-to-configure-environment.md)について学習します。

