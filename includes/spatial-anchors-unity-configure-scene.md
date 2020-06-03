---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998051"
---
次に、自分のアカウント識別子とアカウント キーを使用するようにアプリを構成します。 これらの情報は、[Spatial Anchors リソースを設定](#create-a-spatial-anchors-resource)するときにテキスト エディターにコピーしました。

**[Project]\(プロジェクト\)** ウィンドウで `Assets\AzureSpatialAnchors.SDK\Resources` に移動します。 [`SpatialAnchorConfig`] を選択します。 次に、 **[Inspector]** ウィンドウで、`Spatial Anchors Account Key` の値として `Account Key` を、`Spatial Anchors Account Id` の値として `Account ID` を入力します。

次に、`SpatialAnchorManager.cs` を開きます。 `CreateSessionAsync()` を見つけて、`session.Configuration.AccountDomain = "MyAccountDomain";` を追加し、前述のアカウント ドメインを置き換えます。 この行を、`// Configure authentication` というコメントの前に直接追加できます。
