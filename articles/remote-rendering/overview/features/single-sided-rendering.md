---
title: 片面レンダリング
description: 片面レンダリングの設定とユース ケースについて説明します
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f9feb75cf46eee4329c9392771fe2e329a1d6d5
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613622"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: レンダリング

ほとんどのレンダラーでは、パフォーマンスを向上させるために[バックフェイス カリング](https://en.wikipedia.org/wiki/Back-face_culling)を使用します。 ただし、[切断面](cut-planes.md)を使用してメッシュが切り開かれると、多くの場合、三角形の裏面が見えるようになります。 それらの三角形がカリングされている場合、表示される結果は納得のいくものではありません。

この問題を確実に回避する方法は、三角形の*両面*をレンダリングすることです。 バックフェイス カリングを使用しないとパフォーマンスに影響があるため、既定では、Azure Remote Rendering でのみ、切断面と交差しているメッシュについて両面レンダリングに切り替わります。

この動作は、 *:::no-loc text="single-sided"::: レンダリング*の設定を使用してカスタマイズできます。

> [!CAUTION]
> :::no-loc text="single-sided"::: レンダリングの設定は試験的な機能です。 将来、再度削除される可能性があります。 アプリケーション内の重大な問題を実際に解決する場合を除き、既定の設定は変更しないでください。

## <a name="prerequisites"></a>前提条件

:::no-loc text="single-sided"::: レンダリングの設定は、`opaqueMaterialDefaultSidedness` オプションを `SingleSided` に設定して[変換](../../how-tos/conversion/configure-model-conversion.md)されたメッシュにのみ効果があります。 既定では、このオプションは `DoubleSided` に設定されています。

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: レンダリング設定

3 種類のモードがあります。

**Normal:** このモードでは、メッシュは変換されるときに常にレンダリングされます。 つまり、`opaqueMaterialDefaultSidedness` を `SingleSided` に設定して変換されたメッシュは、切断面と交差している場合でも、常にバックフェイス カリングが有効な状態でレンダリングされます。

**DynamicDoubleSiding:** このモードでは、切断面がメッシュと交差すると、両面レンダリングに自動的に切り替わります。 これは既定のモードです。

**AlwaysDoubleSided:** すべての片面ジオメトリが常に強制的に両面レンダリングされるようにします。 このモードは大部分が公開されているので、:::no-loc text="single-sided"::: レンダリングと :::no-loc text="double-sided"::: レンダリングのパフォーマンスへの影響を簡単に比較できます。

:::no-loc text="single-sided"::: レンダリングの設定の変更は、次のように行うことができます。

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Actions()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>API のドキュメント

* [C# RemoteManager.SingleSidedSettings プロパティ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.singlesidedsettings)
* [C++ RemoteManager::SingleSidedSettings()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#singlesidedsettings)

## <a name="next-steps"></a>次のステップ

* [切断面](cut-planes.md)
* [モデルの変換を構成する](../../how-tos/conversion/configure-model-conversion.md)
