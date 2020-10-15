---
title: カメラ
description: カメラの設定とユース ケースについて説明します
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564747"
---
# <a name="camera"></a>カメラ

ローカルおよびリモートでレンダリングされるコンテンツをシームレスに合成できるようにするには、さまざまなカメラ プロパティがサーバーとクライアントの間で同期されたままになっている必要があります。 中でも注目すべきは、カメラの変換と投影です。 たとえば、ローカルでレンダリングされるコンテンツでは、最新のリモート フレームのレンダリングで使用されたのと同じカメラの変換と投影を使用する必要があります。

![ローカルとリモートのカメラ](./media/camera.png)

上の画像では、ローカルのカメラがサーバーから送信されたリモート フレームと比べて移動しています。 その結果、ローカル コンテンツはリモート フレームと同じパースペクティブからレンダリングされる必要があるため、最終的に、結果として生成される画像がローカルのカメラ空間に再投影されます。これについては、「[Late Stage Reprojection](late-stage-reprojection.md)」で詳しく説明しています。

リモート レンダリングとローカル レンダリングの間の遅延は、これらの設定の変更が遅れて適用されることを意味します。 そのため、新しい値を同じフレームですぐに使用することはできません。

カメラの設定では、前方面と後方面の距離を設定できます。 HoloLens 2 では、変換と投影の残りのデータはハードウェアによって定義されます。 [デスクトップのシミュレーション](../../concepts/graphics-bindings.md)を使用する場合は、`Update` 関数の入力から設定されます。

変更された値は、それらを使用してレンダリングされた最初のリモート フレームが到着するとすぐにローカルで使用できます。 HoloLens 2 では、レンダリングに使用されるデータは、他のホログラフィック アプリケーションと同様に、*HolographicFrame* によって提供されます。 [デスクトップのシミュレーション](../../concepts/graphics-bindings.md)では、`Update` 関数の出力から取得されます。

## <a name="camera-settings"></a>カメラの設定

カメラの設定では、次のプロパティを変更できます。

**前方および後方面:**

無効な範囲を設定できないようにするために、**NearPlane** と **FarPlane** の各プロパティは読み取り専用になっており、範囲の変更には別の関数 **SetNearAndFarPlane** が用意されています。 このデータは、フレームの終了時にサーバーに送信されます。

> [!IMPORTANT]
> Unity では、これはメイン カメラの前方面と後方面の変更時に自動的に処理されます。

**EnableDepth**:

デバッグのためにリモート画像の深度バッファーへの書き込みを無効にすると便利な場合があります。 深度を無効にすると、サーバーによる深度データの送信も停止されるため、帯域幅が減少します。

> [!TIP]
> Unity では、**EnableDepthComponent** と呼ばれるデバッグ コンポーネントが用意されており、これを使用してエディターの UI でこの機能を切り替えることができます。

カメラの設定の変更は、次のように行うことができます。

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>API のドキュメント

* [C# CameraSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11.Update 関数](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11::Update 関数](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>次のステップ

* [グラフィックスのバインド](../../concepts/graphics-bindings.md)
* [Late Stage Reprojection](late-stage-reprojection.md)
