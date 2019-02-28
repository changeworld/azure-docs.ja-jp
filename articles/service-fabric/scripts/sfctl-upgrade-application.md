---
title: Service Fabric CLI スクリプト サンプル - クラスター上のアプリケーションを更新する
description: Service Fabric CLI スクリプト サンプル - アプリケーションを新しいバージョンで更新する この例では、デプロイされているアプリケーションも新しいビットでアップグレードされます。
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: 7c6b4b6214f5fc2788827ae34aad4019dad1d8ae
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806965"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>アプリケーション証明書の Service Fabric クラスターへの追加

このサンプル スクリプトは、既存のアプリケーションの新しいバージョンをアップロードしてから、デプロイされているアプリケーションを新しいビットでアップグレードします。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>次の手順

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
