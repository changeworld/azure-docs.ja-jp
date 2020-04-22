---
title: Unity でリモート エンティティを操作する
description: ARR エンティティの操作方法を紹介するチュートリアル。
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310207"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>チュートリアル:Unity でリモート エンティティを操作する

[チュートリアル:Unity プロジェクトをゼロから設定する](project-setup.md)」では、Azure Remote Rendering を操作するための新しい Unity プロジェクトを構成する方法を紹介しました。 このチュートリアルでは、すべての ARR ユーザーに必要となるごく一般的な機能について見ていきます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * レイ キャストを使用してオブジェクトを選択する。
> * 濃淡の色、選択状態、可視性など、オブジェクトの状態をオーバーライドする。
> * リモート エンティティを削除する。
> * リモート エンティティを動かす。
> * 切断面を使用してオブジェクトの内部を見る。

## <a name="prerequisites"></a>前提条件

* このチュートリアルは、次のチュートリアルに基づいて作成されています: 「[チュートリアル: Unity プロジェクトをゼロから設定する](project-setup.md)」。

> [!TIP]
> [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)の *Unity* フォルダーには、すべてのチュートリアルを対象に準備された Unity プロジェクトが格納されています。それらのプロジェクトは、リファレンスとして使用することができます。

## <a name="pick-objects"></a>オブジェクトを選択する

オブジェクトを対話的に操作するにあたってまず必要なことは、マウス カーソルの下にあるオブジェクトの選択です。

**RemoteRaycaster** という[新しいスクリプト](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)を作成し、その内容全体を以下のコードに置き換えます。

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

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

このコンポーネントをシーン内の *RemoteRendering* オブジェクトに追加します。

> [!WARNING]
>
> *RemoteRaycaster* コンポーネントでは、同じオブジェクトに *ARRServiceUnity* コンポーネントがアタッチされている必要があります。 *ARRServiceUnity* は、いくつかの ARR 機能にアクセスしやすいよう支援するヘルパー クラスです。 ただし、シーンに置くことができるこのコンポーネントのインスタンスは 1 つだけです。 そのため、*ARRServiceUnity* を必要とするコンポーネントはすべて同じ GameObject に追加してください。
> 複数のゲーム オブジェクトから ARR の機能にアクセスしたい場合は、そのいずれか 1 つにのみ *ARRServiceUnity* コンポーネントを追加して、他のスクリプトからそれを参照するか、ARR 機能に直接アクセスします。

再生を押してセッションに接続し、モデルを読み込みます。 ここで、シーン内のオブジェクトをポイントして、コンソール出力を観察してみましょう。 マウス ポインターを合わせた各部分のオブジェクト名が出力されるはずです。

## <a name="highlight-objects"></a>オブジェクトを強調表示する

次の手順として、モデルのどの部分をユーザーがポイントしているかがわかるよう、視覚的なフィードバックを与えたいと思います。 この動作を実現するために、ここでは、選択したエンティティに [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) をアタッチします。 このコンポーネントを使用すると、オブジェクトのさまざまな機能を有効または無効にすることができます。 ここでは、このコンポーネントを使用して濃淡の色を設定し、[アウトライン レンダリング](../../overview/features/outlines.md)を有効にします。

**RemoteModelEntity** という別のスクリプト ファイルを作成し、その内容を以下のコードに置き換えます。

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> このスクリプトをゲーム オブジェクトに割り当てないでください。以下のコードによってプログラムから割り当てられます。

次に、*RemoteRaycaster* を拡張して、先ほど選択したオブジェクトに *RemoteModelEntity* コンポーネントを追加する必要があります。

**RemoteRaycaster** の実装に次のコードを追加し、重複する関数は削除します。

```csharp
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
        focusedModel = null;
    }
```

プロジェクトを実行してモデルをポイントすると、赤色の濃淡と白色の選択アウトラインが適用されることがわかります。

## <a name="isolate-the-selected-object"></a>選択したオブジェクトを分離する

[HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) には、もう 1 つの使い方があります。可視性をオーバーライドする機能です。 これを使用すると、選択したオブジェクトをモデルの他の部分から分離することができます。 **RemoteModelEntity** スクリプトを開いて次のコードを追加し、重複する関数は削除してください。

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

このコードでは、すべてのオブジェクトを非表示にする状態オーバーライド コンポーネントを階層の最上位のオブジェクトに配置しています。 そのうえで、選択したオブジェクトの可視性を再度オーバーライドすることにより、その 1 つのオブジェクトを表示状態にします。 したがって状態オーバーライド コンポーネントは、ルート オブジェクトで作成する必要があります。

**RemoteRendering** スクリプトを開いて、*LoadModel* 関数の一番上に次のコードを挿入します。

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

最後に、可視性を切り替える方法が必要です。 **RemoteRaycaster** スクリプトを開いて、*Update* 関数を次の内容に置き換えます。

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

コードを実行し、モデルの一部を右クリックします。 他のモデル領域が消え、強調表示された部分だけが表示状態のままになります。

## <a name="remove-gameobject-instances-of-remote-entities"></a>リモート エンティティの GameObject インスタンスを削除する

既にお気付きかもしれませんが、このコードはオブジェクトを作り続けるだけで、一切クリーンアップしません。 このことは、オブジェクト階層パネルでも確認できます。 シミュレーション中にリモート オブジェクト階層を展開すると、モデルの各部分に新たにマウス ポインターを合わせるたびに、表示されるオブジェクトが増えていくのがわかります。

シーンのオブジェクトが増えると、パフォーマンスに悪影響が生じます。 不要になったオブジェクトは、必ずクリーンアップする必要があります。

**RemoteRaycaster** スクリプトに次のコードを挿入し、重複する関数は削除してください。

```csharp
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
```

## <a name="move-objects"></a>オブジェクトの移動

次のステップとして、選択したオブジェクトをあちこちに動かしたいと思います。 **RemoteRaycaster** スクリプトに次のコードを挿入し、重複する関数は削除してください。

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> このコードを実行しても、何も起こらないことに気付くでしょう。 パフォーマンス上の理由から、オブジェクトの変換に変更を加えても、自動的には状態の変化がサーバーと同期されないためです。 この場合、この状態の変化を手動でサーバーにプッシュするか、*RemoteEntitySyncObject* コンポーネントの **SyncEveryFrame** を有効にする必要があります。

**RemoteModelEntity** スクリプトを開いて、次の行を追加します。

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

もう一度コードを実行すると、オブジェクトを左クリックして、あちこちにドラッグできるはずです。

## <a name="add-a-cut-plane"></a>切断面を追加する

このチュートリアルの最後の機能として、[切断面](../../overview/features/cut-planes.md)を使ってみたいと思います。 切断面は、レンダリングされたオブジェクトの中身が見えるようにその一部を切り取るものです。

シーン **CutPlane** に新しい GameObject を作成します。 新しいスクリプトを作成し、**RemoteCutPlane** という名前を付けます。 そのコンポーネントを新しい GameObject に追加します。

スクリプト ファイルを開いて、その内容を次のコードに置き換えてください。

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

コードを実行すると、モデルが切断面で切り取られた状態を確認できます。 *[Scene]\(シーン\)* ウィンドウで *CutPlane* オブジェクトを選択し、動かしたり回転させたりすることができます。 切断面のオンとオフは、切断面オブジェクトを無効にすることで切り替えることができます。

## <a name="next-steps"></a>次のステップ

ここでは、リモート オブジェクトを対話的に操作するうえで最も重要な機能について説明しました。 次のチュートリアルでは、シーンの外観をカスタマイズする方法について見てみましょう。

> [!div class="nextstepaction"]
> [チュートリアル:環境と素材を変更する](changing-environment-and-materials.md)
