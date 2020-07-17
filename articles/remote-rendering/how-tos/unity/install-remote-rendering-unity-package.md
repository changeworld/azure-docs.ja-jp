---
title: Unity 用の Remote Rendering パッケージをインストールする
description: Unity 用の Remote Rendering クライアント DLL をインストールする方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679197"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Unity 用の Remote Rendering パッケージをインストールする

Azure Remote Rendering では、Unity パッケージを使用して Unity への統合をカプセル化します。

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Unity での Remote Rendering パッケージの管理

Unity パッケージは、Unity の[パッケージ マネージャー](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)を介して管理できるコンテナーです。
このパッケージには、C# API 全体に加えて、Unity で Azure Remote Rendering を使用するために必要なすべてのプラグイン バイナリが含まれています。
Unity のパッケージの命名規則に従い、パッケージは **com.microsoft.azure.remote-rendering** と呼ばれます。

このパッケージは [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)の一部ではなく、Unity の内部パッケージ レジストリからは使用できません。 プロジェクトに追加するには、プロジェクトの `manifest.md` ファイルを手動で編集して、次のものを追加する必要があります。
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
これが追加されたら、Unity のパッケージ マネージャーを使用して、最新バージョンがあることを確認できます。
より包括的な手順については、「[チュートリアル: Unity プロジェクトをゼロから設定する](../../tutorials/unity/project-setup.md)」を参照してください。

## <a name="unity-render-pipelines"></a>Unity のレンダリング パイプライン

Remote Rendering は、**ユニバーサル レンダリング パイプライン** と **標準レンダリング パイプライン**の両方で動作します。 パフォーマンス上の理由から、ユニバーサル レンダリング パイプラインを使用することをお勧めします。

**ユニバーサル レンダリング パイプライン**を使用するには、Unity にそのパッケージをインストールする必要があります。 これを行うには、Unity の **パッケージ マネージャー** UI (パッケージ名 **Universal RP**、バージョン 7.2.1 以降)、または[Unity プロジェクトのセットアップのチュートリアル](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)で説明されているように、`Packages/manifest.json` ファイルを使用します。

## <a name="next-steps"></a>次のステップ

* [Unity のゲーム オブジェクトとコンポーネント](objects-components.md)
* [チュートリアル:Unity プロジェクトをゼロから設定する](../../tutorials/unity/project-setup.md)
