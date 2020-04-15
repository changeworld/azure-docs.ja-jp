---
title: 環境と素材を変更する
description: Unity シーン内の空のマップとオブジェクトの素材を変更する方法を示すチュートリアル。
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678683"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>チュートリアル:環境と素材を変更する

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * シーンの環境マップを変更する。
> * 素材パラメーターを変更する。
> * カスタム テクスチャを読み込む。

## <a name="prerequisites"></a>前提条件

このチュートリアルは、次の記事を理解していることを前提としています: 「[チュートリアル: Unity でリモート エンティティを操作する](working-with-remote-entities.md)」。 ただし、次のトピックに説明されているように、必要になるのはセッションに接続してモデルを読み込むことができる Unity プロジェクトのみです: 「[チュートリアル: Unity プロジェクトをゼロから設定する](project-setup.md)」。

> [!TIP]
> [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)の *Unity* フォルダーには、すべてのチュートリアルを対象に準備された Unity プロジェクトが格納されています。それらのプロジェクトは、リファレンスとして使用することができます。

## <a name="change-the-environment-map"></a>環境マップを変更する

Azure Remote Rendering では、環境光をシミュレートするために[スカイ ボックス](../../overview/features/sky.md) ("環境マップ" とも呼ばれます) の使用をサポートしています。 これは、サンプル モデルのように、オブジェクトで " *[物理ベース レンダリング](../../overview/features/pbr-materials.md)* " を使用する場合に特に役立ちます。 ARR には、このチュートリアルで使用するさまざまな組み込みの空のテクスチャも付属しています。

**RemoteSky** という名前の新しいスクリプトを作成し、それを新しい GameObject に追加します。 スクリプト ファイルを開き、次のコードに置き換えます。

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

組み込みのテクスチャが読み込まれるため、上記の `LoadTextureFromSASAsync` バリアントが使用されていることに注意してください。 [リンクされた BLOB ストレージ](../../how-tos/create-an-account.md#link-storage-accounts)から読み込む場合は、`LoadTextureAsync` バリアントを使用します。 これがモデルに対してどのように動作するかを示す例は、[モデルの読み込みに関するセクション](../../concepts/models.md#loading-models)にあります。

コードを実行して空のマップを切り替えると、モデルのライティングが大きく異なることがわかります。 ただし、背景は黒のままで、実際の空のテクスチャを見ることはできません。 これは意図的なものです。背景をレンダリングすると、Augmented Reality デバイスで邪魔になることがあるためです。 適切なアプリケーションでは、実際の環境に似た空のテクスチャを使用することをお勧めします。これは、オブジェクトがより現実的に見えるようにするためです。

## <a name="modify-materials"></a>素材を変更する

前のチュートリアルでは、[状態のオーバーライド コンポーネント](../../overview/features/override-hierarchical-state.md)を使用して、選択したオブジェクトの濃淡の色を変更しました。 ここでは、オブジェクトの[素材](../../concepts/materials.md)を変更することで同様の効果を実現したいと思います。

最初に、[2 番目のチュートリアル](working-with-remote-entities.md)で行ったように、オブジェクトを選択するためのスクリプトが必要です。 **RemoteRaycaster** スクリプトをまだ作成していない場合は、ここで作成してください。 その内容を次のコードに置き換えます。

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }
}
```

コンポーネントを *RemoteRendering* ゲーム オブジェクトに追加します。 これは、マウス ポインターの下にあるオブジェクトを選択し、選択したオブジェクトに *RemoteModelEntity* コンポーネントを追加する役割を担います。 このコンポーネント クラスが、実際の素材変更機能を実装する場所になります。

**RemoteModelEntity** スクリプトをまだ作成していない場合はこれを作成し、その内容を次のコードに置き換えます。

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

このコードを実行すると、マウス ポインターを合わせたオブジェクトが強調表示されます。 この効果はチュートリアル 2 の効果に似ていますが、実現方法が異なります。 ここでは、選択したオブジェクトの素材の一覧を取得し、最初の素材を変更して、別のアルベド カラーを設定します。

> [!IMPORTANT]
> この方法によってモデルの正しい部分が強調表示されるかどうかはモデルの作成方法によって異なることに注意してください。 これは、すべてのオブジェクトで 1 つの素材だけを使用する場合に完全に機能します。 ただし、部品と素材の間に 1 対 1 の関係がないモデルの場合、上記の単純なコードでは適切な処理は行われません。

## <a name="use-a-different-texture"></a>別のテクスチャを使用する

[テクスチャ](../../concepts/textures.md)は、通常、ソース モデルの一部です。 [モデルの変換](../../quickstarts/convert-model.md)中、すべてのテクスチャが必要なランタイム形式に変換され、最終的なモデル ファイルにパッケージ化されます。 実行時にテクスチャを置き換えるには、テクスチャを [DDS ファイル形式](https://en.wikipedia.org/wiki/DirectDraw_Surface)で保存し、Azure Blob Storage にアップロードする必要があります。 Azure BLOB コンテナーを作成する方法については、[このクイックスタート ガイド](../../quickstarts/convert-model.md)を参照してください。 BLOB コンテナーを作成したら、それを Azure Storage Explorer で開き、ドラッグ アンド ドロップでファイルをアップロードすることができます。

ランタイム側では、BLOB ストレージ内のテクスチャ アセットを 2 つの異なる方法でアドレス指定できます。

* SAS URI でテクスチャをアドレス指定する。 そのためには、アップロードされたファイルを右クリックし、コンテキスト メニューの **[Shared Access Signature の取得]** を選択します。 この SAS URI を `LoadTextureFromSASAsync` 関数バリアントと共に使用します (以下のサンプル コードを参照してください)。
* BLOB ストレージ パラメーターによってテクスチャを直接アドレス指定する ([BLOB ストレージがアカウントにリンクされている場合](../../how-tos/create-an-account.md#link-storage-accounts))。 この場合、関連する読み込み関数は `LoadTextureAsync` です。

次に、**RemoteModelEntity** スクリプトを開きます。次のコードを追加し、重複する関数を削除します。

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

このコードを実行し、モデルにマウス ポインターを合わせます。 モデルに適切な UV 座標がある場合は、そのテクスチャが表示されます。 それ以外の場合は、色が変わるだけです。

## <a name="next-steps"></a>次のステップ

これで、Unity で Azure Remote Rendering を使用する方法についての概要シリーズは終了です。 次の手順として、[セッション](../../concepts/sessions.md)、[エンティティ](../../concepts/entities.md)、[モデル](../../concepts/models.md)などの ARR の基本的な概念をいくつか確認して、理解を深める必要があります。 また、[ライト](../../overview/features/lights.md)、[アウトライン レンダリング](../../overview/features/outlines.md)、[階層型の状態のオーバーライド](../../overview/features/override-hierarchical-state.md)、[素材](../../concepts/materials.md)など、より深く理解する必要のあるさまざまな機能があります。

> [!div class="nextstepaction"]
> [Unity のゲーム オブジェクトとコンポーネント](../../how-tos/unity/objects-components.md)
