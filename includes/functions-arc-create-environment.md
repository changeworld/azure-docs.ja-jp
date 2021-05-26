---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 43eda9f0f5cdb108c2949205d72433c9d7176571
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371983"
---
## <a name="create-an-app-service-kubernetes-environment"></a>App Service Kubernetes 環境の作成

開始する前に、Azure Arc 対応 Kubernetes クラスター用の [App Service Kubernetes 環境を作成する](../articles/app-service/manage-create-arc-environment.md)必要があります。 

> [!NOTE] 
> 環境を作成するときは、カスタムの場所名とカスタムの場所を含むリソース グループの名前の両方をメモしておいてください。 これらを使用すると、環境で関数アプリを作成するときに必要なカスタムの場所 ID を見つけることができます。 
>
> 環境を作成していなかった場合は、クラスター管理者に確認してください。