---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: c18e40b8950d5e75398382a41cf0acc3505ea5a6
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092988"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>センサーのフィンガープリント プロバイダーを構成する

まず、センサーのフィンガープリント プロバイダーを作成して構成します。 これにより、デバイス上にあるプラットフォーム固有のセンサーの読み取りを処理し、それらのセンサーを、クラウド アンカー セッションによって使用される共通表現へと抽象化します。