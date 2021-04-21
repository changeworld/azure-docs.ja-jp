---
title: 変換用のファイルのレイアウト
description: 入力コンテナーにファイルを配置するための最適な方法に関する推奨事項。
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 9a094755dfb9381b1e4d6abdf1c0e6342d7427c1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308441"
---
# <a name="laying-out-files-for-conversion"></a>変換用のファイルのレイアウト

資産を正しく処理するためには、変換サービスがすべての入力ファイルを見つけることができる必要があります。
これらは、変換されるメインの資産ファイルと、通常、資産ファイル内のパスで参照される他のいくつかのファイルで構成されます。
資産の変換要求には、変換サービスがこれらのファイルを見つける方法を決定する 2 つのパラメーター(`settings.inputLocation.blobPrefix` (省略可能) と `settings.inputLocation.relativeInputAssetPath`) があります。
これらについては、[REST API の変換](conversion-rest-api.md)に関するページに詳細に記載されています。
ファイルをレイアウトするために注意する必要がある重要な点は、資産を処理するときに変換サービスが使用できる一連のすべてのファイルが `BlobPrefix` によって決定されることです。

> [!Note]
> このサービスを使用すると、input.BlobPrefix の下にあるすべてのファイルがダウンロードされます。 サービスでの問題を回避するために、ファイル名とパスが [Windows パスの長さ制限](https://docs.microsoft.com/windows/win32/fileio/maximum-file-path-limitation)を超えていないことを確認してください。 

## <a name="placing-files-so-they-can-be-found"></a>ファイルが見つけることができるように配置する

ソース資産で外部ファイルを使用する場合、それらのファイルへのパスは資産内に格納されます。
変換サービスは、資産の元のファイル システムとは異なるファイル システムでこれらのパスを解釈する必要があります。
パスが相対パスとして格納されていて、ソース資産とそれが参照するファイルの間の相対位置が変更されていない場合、変換サービスは参照されるファイルを簡単に見つけることができます。

> [!Note]
> 資産が作成されたときとファイルの相対位置が同じになるように、入力コンテナーにファイルを配置することをお勧めします。

> [!Note]
> 相対パスが保持される資産を作成することをお勧めします。
> [3ds Max でのマテリアルの設定](../../tutorials/modeling/3dsmax-material-setup.md)に関するチュートリアルに、資産によって相対パスが必ず使用されるようにする方法の 3ds Max の例が示されています。

## <a name="finding-textures"></a>テクスチャを見つける

資産の生成には多くの方法があるため、変換サービスは柔軟である必要があります。
特に、資産内のパスとテクスチャの位置が正確に一致しない状況に対処する必要があります。
例として、絶対パスを含む資産が生成される場合があります。これらのパスは、変換サービスで使用されるファイルシステムと一致しないためです。
このような状況に対処するために、テクスチャを見つけるためのベスト エフォートのアプローチが使用されています。

テクスチャを見つけるためのアルゴリズムは次のとおりです。資産に格納されているパスがあり、ソース資産の場所からの相対パスとして使用されている場合、最も長いサブパス サフィックスが検索され、存在するファイルが対象となります。
そのようなサブパス (パス全体を含む) がファイルを指していない場合、テクスチャは欠落していると見なされます。

次のような不自然なファイルシステムについて考えてみましょう。 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
myAsset.fbx が相対パス `..\Textures\MyAssetTextures\myTexture.png` のテクスチャを参照している場合、変換サービスはファイル E を使用します。実際には、ファイル A、C、および E のいずれかを使用でき (存在する場合)、最も長いサフィックスで見つかったのでファイル E が優先されます。
ファイル B と D は使用されません。`Textures\myTexture.png` は、格納されているパスのサフィックスの一部ではないためです。
資産にパス `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` または `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` が含まれている場合、変換サービスはファイル A と C を見つけることができます (存在する場合。C は A より優先されます)。 ただし、E はこのように見つけることができないため、ファイルを再配置する必要があります。
これを解決するには、Textures フォルダーを資産と同じレベルに移動します。

> [!Note]
> テクスチャが見つからない場合、考えられる対応策は資産がそのテクスチャを含むサブツリーと同じレベルであることを確認することです。

## <a name="next-steps"></a>次のステップ

- [モデル変換](model-conversion.md)
