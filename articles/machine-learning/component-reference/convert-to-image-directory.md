---
title: イメージ ディレクトリへの変換
titleSuffix: Azure Machine Learning
description: イメージ ディレクトリへの変換コンポーネントを使って、データセットをイメージ ディレクトリ形式に変換する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: d6411f0744872daaf4ffaf2a03c2505d32c264e8
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565691"
---
# <a name="convert-to-image-directory"></a>イメージ ディレクトリへの変換

この記事では、イメージ ディレクトリへの変換コンポーネントを使って画像データセットを *Image Directory* データ型に変換する方法について説明します。これは、Azure Machine Learning デザイナーの画像関連タスク (画像分類など) で使われる標準のデータ形式です。

## <a name="how-to-use-convert-to-image-directory"></a>イメージ ディレクトリへの変換を使用する方法  

1. まず、イメージ データセットを準備します。 

    監視学習を行うには、トレーニング データ セットのラベルを指定する必要があります。 イメージ データセット ファイルは、次の構造になっている必要があります。
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    イメージ データセット フォルダーには、複数のサブフォルダーがあります。 各サブフォルダーには、それぞれ1つのカテゴリの画像が含まれます。 サブフォルダーの名前は、イメージ分類などのタスクのラベルと見なされます。 詳細については、「[Torchvision データセット](https://pytorch.org/vision/stable/datasets.html#imagefolder)」を参照してください。

    > [!WARNING]
    > 現在、データ ラベルからエクスポートされたラベル付きデータセットは、デザイナーではサポートされていません。

    これらの拡張子を持つイメージ (小文字) はサポートされています: .jpg、.jpeg、.png、ppm、.bmp、. pgm、.tif、tiff、. webp。 また、1 つのフォルダーに複数の種類のイメージを含めることもできます。 各カテゴリ フォルダーに同じ数のイメージを含める必要はありません。

    拡張子が.zip、. tar、. gz、および bz2 のフォルダーまたは圧縮ファイルを使用できます。 **圧縮ファイルは、パフォーマンスを高めるため推奨されます。** 
    
    ![イメージのサンプル データセット](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > 推論の場合、イメージ データセットのフォルダーには、未分類のイメージのみを含める必要があります。

1. イメージ ディレクトリ コンポーネントへの変換の入力は **ファイル データセット** でなければならないため、[イメージ データセットはファイル データセットとしてワークスペースに登録します](../how-to-create-register-datasets.md)。

1. 登録されているイメージ データセットをキャンバスに追加します。 登録されているデータセットは、キャンバスの左側にあるコンポーネント一覧の **データセット** カテゴリで確認できます。 現在、デザイナーでは、イメージデータセットの視覚化はサポートされていません。

    > [!WARNING]
    > **データのインポート** コンポーネントの出力型は DataFrame Directory であるため、**データのインポート** コンポーネントを使って画像データセットをインポートすることは **できません**。

1. **イメージ ディレクトリへの変換** コンポーネントをキャンバスに追加します。 このコンポーネントは、コンポーネント一覧の [Computer Vision/Image Data Transformation]\(Computer Vision/イメージ データ変換\) カテゴリにあります。 これをイメージ データセットに接続します。
    
3.  パイプラインを送信します。 このコンポーネントは、GPU または CPU 上で実行できます。

## <a name="results"></a>結果

**イメージ ディレクトリへの変換** コンポーネントの出力は **Image Directory** 形式であり、入力ポートの形式が同様に Image Directory である他の画像関連コンポーネントに接続できます。

![イメージ ディレクトリへの変換の出力](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>テクニカル ノート 

###  <a name="expected-inputs"></a>想定される入力  

| 名前          | 種類                  | 説明   |
| ------------- | --------------------- | ------------- |
| 入力データセット | AnyDirectory、ZipFile | 入力データセット |

###  <a name="output"></a>出力  

| 名前                   | 種類           | 説明            |
| ---------------------- | -------------- | ---------------------- |
| 出力イメージ ディレクトリ | ImageDirectory | 出力イメージ ディレクトリ |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使える一連のコンポーネント](component-reference.md)を参照してください。