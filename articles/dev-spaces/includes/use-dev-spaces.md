---
title: インクルード ファイル
description: インクルード ファイル
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: a8f5adc8e6745fdbfd31d1a9cb9407672d61da07
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129302"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Azure Dev Spaces を使用するように AKS クラスターを構成する

コマンド ウィンドウを開き、次の Azure CLI コマンドを入力します。このとき、AKS クラスターを含む含むリソース グループと、AKS クラスター名を使用します。 このコマンドでは、Azure Dev Spaces のサポートを使用してクラスターが構成されます。

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

