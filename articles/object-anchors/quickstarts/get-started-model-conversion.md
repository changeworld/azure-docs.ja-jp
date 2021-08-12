---
title: 'クイック スタート: アプリで使用する Object Anchors モデルを作成する'
description: このクイックスタートでは、3D モデルから Object Anchors モデルを作成する方法について説明します。
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 06/10/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: e3b67bee93a0e9a7b823d3a91d6d95e1bccbbe71
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202826"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>クイック スタート: 3D モデルから Object Anchors モデルを作成する

Azure Object Anchors は、3D モデルを HoloLens の物体認識 Mixed Reality エクスペリエンスを実現する AI モデルに変換するマネージド クラウド サービスです。 このクイックスタートでは、[Azure Object Anchors の .NET 用変換 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre) を使用して 3D モデルから Object Anchors モデルを作成する方法について説明します。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Object Anchors アカウントを作成します。
> * [Azure Object Anchors の .NET 用変換 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre) ([NuGet](https://www.nuget.org/packages/Azure.MixedReality.ObjectAnchors.Conversion/)) を使用して、3D モデルを変換して Object Anchors モデルを作成します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

* <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> を備えた Windows マシン。
* <a href="https://git-scm.com" target="_blank">Git for Windows</a>。
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3.1 SDK</a>。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

## <a name="get-the-sample-project"></a>サンプル プロジェクトを入手する

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>3D モデルを変換する

これで先に進んで、3D モデルを変換できます。

1. Visual Studio で `quickstarts/conversion/Conversion.sln` を開きます。 このソリューションには C# コンソール プロジェクトが含まれています。

2. プロジェクトのルートに配置されている `Configuration.cs` ファイルを開き、次の各フィールドの `set-me` 値を置き換えます。

   | フィールド         | 説明                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | 上記で作成した Object Anchors アカウントの **アカウント ドメイン**。 |
   | AccountId     | 上記で作成した Object Anchors アカウントの **アカウント ID**。     |
   | AccountKey    | 上記で作成した Object Anchors アカウントの **主キー**。     |

   次の 4 つの追加フィールドを確認する必要があります。

    | フィールド                    | 説明                       |
    | ---                      | ---                               |
    | InputAssetPath           | ローカル コンピューター上の 3D モデルへの絶対パス。 サポートされるファイル形式は、`fbx`、`ply`、`obj`、`glb`、`gltf` です。 |
    | AssetDimensionUnit       | 3D モデルの測定単位。 サポートされているすべての測定単位は、`Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` 列挙型を使用してアクセスできます。 |
    | 重力                  | 3D モデルの重力ベクトルの方向。 この 3D ベクトルは、モデルの座標系で下方向を示します。 たとえば、負の `y` がモデルの 3D 空間の下方向を表す場合、この値は `Vector3(0.0f, -1.0f, 0.0f)` になります。 |

3. プロジェクトをビルドおよび実行して 3D モデルをアップロードし、新しい変換ジョブをサービスに登録して、それが完了するまで待ちます。 ジョブが完了すると、`InputAssetPath` に指定されたファイルの横に Object Anchors モデルがダウンロードされます。 次のようなコンソール出力が表示されます。

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   今後の参照のために **ジョブ ID** をメモしておきます。 デバッグやトラブルシューティングの際に役立つ場合があります。

4. ジョブが正常に完了すると、指定した出力場所に `<Model-Filename-Without-Extension>_<JobID>.ou` の形式のファイルが表示されます。 たとえば、3D モデルのファイル名が `chair.ply` で、ジョブ ID が `00000000-0000-0000-0000-000000000000` である場合、サービスにより出力されるファイル名は `chair_00000000-0000-0000-0000-000000000000.ou` になります。

## <a name="error-codes"></a>エラー コード
アセット変換ジョブが失敗した結果生成されるさまざまなエラー コードと、それぞれの処理方法の詳細については、[変換エラー コードに関するページ](..\model-conversion-error-codes.md)を参照してください。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Object Anchors アカウントを作成し、3D モデルを変換して Object Anchors モデルを作成しました。 このモデルを Mixed Reality アプリ内の Object Anchors SDK と統合する方法を理解するには、続けて次のいずれかの記事を参照してください。

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [ MRTK を使用した Unity HoloLens](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)

> [!div class="nextstepaction"]
> [変換 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
