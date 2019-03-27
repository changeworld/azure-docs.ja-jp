---
title: Service Fabric CLI スクリプト サンプル - クラスター上のアプリケーションを列挙する
description: Service Fabric CLI スクリプト サンプル - Service Fabric クラスターにプロビジョニングされているアプリケーションを列挙します。
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
ms.date: 04/13/2018
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: 60cb5aaf93521a3e2f7c922949d112ad96a00180
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805639"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Service Fabric クラスターで実行されているアプリケーションを列挙する

このサンプル スクリプトは、Service Fabric クラスターに接続し、プロビジョニングされているすべてのアプリケーションを列挙します。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>次の手順

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
