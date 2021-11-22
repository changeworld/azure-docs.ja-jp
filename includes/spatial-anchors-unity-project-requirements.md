---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/12/2021
ms.author: parkerra
ms.openlocfilehash: 81dffe5fd5e5788538a0d263f305186933ee2574
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490517"
---
新しい Unity プロジェクトを開始するときは、[Unity XR プラグイン フレームワーク](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)と従来の組み込み XR のいずれかを選択できます。

### <a name="xr-plug-in-framework"></a>[XR プラグイン フレームワーク](#tab/xr-plugin-framework)

XR プラグイン フレームワークは、ASA SDK バージョン 2.9.0 以降でのみサポートされています。 XR プラグインフレームワークを対象とするには、プラットフォームに応じて、次のパッケージで、[2020.3 (LTS)](https://unity3d.com/unity/whats-new/2020.3.0)、および AR Foundation [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@4.1/manual/index.html) を使用します。
> [!IMPORTANT]
> Mixed Reality OpenXR プラグインは、ASA SDK バージョン 2.10.0 以降でのみサポートされています
- Mixed Reality OpenXR プラグイン: [1.1.2](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr)
- Windows XR プラグイン: [4.5.0](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr@4.5/manual/index.html)
- ARCore XR プラグイン: [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arcore@4.1/manual/index.html)
- ARKit XR プラグイン: [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arkit@4.1/manual/index.html)

### <a name="legacy-built-in-xr"></a>[従来の組み込み XR](#tab/legacy-built-in-xr)

従来の組み込み XR は、ASA SDK バージョン 2.8.1 以前でのみサポートされています。 従来の組み込み XR を対象とするには、プラットフォームに応じて、次のパッケージで、Unity [2019.4 (LTS)](https://unity.com/releases/2019-lts) および AR Foundation [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@3.1/manual/index.html) を使用します。
- Windows Mixed Reality:[4.2.1](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr.metro@4.2/manual/index.html)
- ARCore XR プラグイン: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arcore@3.1/manual/index.html)
- ARKit XR プラグイン: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arkit@3.1/manual/index.html)