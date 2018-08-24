---
title: Azure の Visual Studio Tools for Machine Learning に関するクイックスタート記事 | Microsoft Docs
description: この記事では、Visual Studio Tools for Machine Learning の使用を開始するために、実験の作成、モデルのトレーニング、Web サービスの運用を開始する方法について説明します。
services: machine-learning
ms.workload: data-services
author: chris-lauren
ms.author: chris.lauren
ms.service: machine-learning
ms.component: core
ms.custom: mvc, vs-azure
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: 94bca4d7670b1ec6fba5057b8295f7a3caac2968
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42024370"
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
Visual Studio Tools for AI は、ディープ ラーニングおよび AI ソリューションを構築、テスト、デプロイするための開発拡張機能です。 その重要な特徴として、Azure Machine Learning とのシームレスな統合、特に過去のトレーニングのパフォーマンスとカスタム メトリックの詳細が表示される実行履歴ビューがあります。 サンプル エクスプローラー ビューも用意されています。これを使用すると、[Microsoft Cognitive Toolkit (従来は CNTK と呼ばれていました)](http://www.microsoft.com/en-us/cognitive-toolkit)、[Google TensorFlow](https://www.tensorflow.org)、その他のディープ ラーニング フレームワークで新しいプロジェクトを参照およびブートストラップすることができます。 最後に、コンピューティング ターゲット用のエクスプローラーも用意されています。これにより、Azure Virtual Machines や GPU 搭載の Linux サーバーなどのリモート環境でモデルをトレーニングするためのジョブを送信できます。 また、[Azure Batch AI (プレビュー)](https://docs.microsoft.com/azure/batch-ai/) にも簡単にアクセスできるようになっています。
 
## <a name="getting-started"></a>使用の開始 
操作を開始するには、最初に [Visual Studio](https://www.visualstudio.com/downloads/) をダウンロードしてインストールする必要があります。 Visual Studio を開いたら、次の手順を実行します。
1. Visual Studio のメニュー バーの [ツール] をクリックし、[拡張機能と更新プログラム] を選択します。
2. [オンライン] タブをクリックし、[Visual Studio Marketplace の検索] を選択します。
3. 「Visual Studio for AI」を検索します。 
3. **[ダウンロード]** ボタンをクリックします。 
4. インストール後、Visual Studio を再起動します。 

Visual Studio の再読み込みが完了すると、拡張機能がアクティブになります。 [拡張機能の検索の詳細についてはこちらを参照してください](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)。

> [!NOTE]
> Visual Studio Tools for AI には、Visual Studio 2015 または Visual Studio 2017 (Professional または Enterprise エディション) が必要です。 Apple OSX バージョンはサポートされていません。 


## <a name="exploring-project-samples"></a>プロジェクト サンプルの探索
Visual Studio Tools for AI には、サンプル エクスプローラーが付属しています。 サンプル エクスプローラーを使用すると、数回のクリックでサンプルを簡単に見つけて試すことができます。 エクスプローラーを開くには、次のように実行します。   
1. メニュー バーの **[AI Tools]\(AI ツール\)** をクリックします。
2. [Azure Machine Learning Gallery]\(Azure Machine Learning ギャラリー\) をクリックします。

タブが開き、Azure ML サンプルがすべて表示されます。

## <a name="creating-a-new-project-from-the-sample-explorer"></a>サンプル エクスプローラーからの新しいプロジェクトの作成 
さまざまなサンプルを参照し、その詳細情報を取得できます。 サンプルを参照して "あやめの分類" サンプルを見つけます。 このサンプルに基づいて新しいプロジェクトを作成するには、次のように実行します。
1. プロジェクト サンプルの **[インストール]** ボタンをクリックし、新しいダイアログを開きます。 
2. リソース グループ、アカウント、ワークスペースを選択します。
3. プロジェクトの種類は、[全般] のままでかまいません。
4. プロジェクトのパスと名前を入力し、Enter キーを押します。 
5. ソリューションを保存するよう求めるダイアログが表示されたら、[保存] をクリックします。 

完了すると、Visual Studio の新しいインスタンスで新しいプロジェクトが開きます。 

> [!TIP]
> Azure リソースにアクセスするには、ログインする必要があります。 埋め込みターミナルで「az login」と入力し、指示に従います。 

## <a name="submitting-experiment-with-the-new-project"></a>新しいプロジェクトでの実験の送信
新しいプロジェクトが Visual Studio で開かれたら、コンピューティング ターゲット (ローカルまたは Docker が動作している VM) にジョブを送信します。
ジョブを送信するには、次のように実行します。 
1. ソリューション エクスプローラーから、送信するファイルを右クリックし、**[スタートアップ ファイルとして設定]** を選択します。
2. プロジェクト名を選択して右クリックし、**[ジョブの送信]** を選択します。
3. 新しいダイアログが開き、スクリプトを実行するクラスター (またはコンピューティング ターゲット) を選択することができます。
4. **[送信]** をクリックします。

ジョブが送信されると、埋め込みターミナルに実行の進行状況が表示されます。

## <a name="view-list-of-jobs"></a>ジョブの一覧の表示
ジョブの送信後は、実行履歴からジョブの一覧を表示できます。
1. **サーバー エクスプローラー**で **[AI Tools]\(AI ツール\)** をクリックします。
2. 次に、**[Azure Machine Learning]** を選択します。
3. **[ジョブ]** メニューをクリックします。

ジョブ エクスプローラーに、このプロジェクトに関して送信された実験がすべて一覧表示されます。 

## <a name="view-job-details"></a>ジョブの詳細を表示する
ジョブ エクスプローラー ビューを開いた状態で、一覧の先頭にある実行をクリックします。
[ジョブの概要] パネルおよび [Logs and Outputs]\(ログと出力\) パネルが読み込まれます。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [IDE を使用するための Azure Machine Learning の構成方法](./how-to-configure-your-IDE.md)
