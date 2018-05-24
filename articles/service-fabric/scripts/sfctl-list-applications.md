---
title: Service Fabric CLI スクリプト サンプル - クラスター上のアプリケーションを列挙する
description: Service Fabric CLI スクリプト サンプル - Service Fabric クラスターにプロビジョニングされているアプリケーションを列挙します。
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: ''
ms.openlocfilehash: be7464cab4f9a1014fa129314c332bc41b2f499c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212944"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Service Fabric クラスターで実行されているアプリケーションを列挙する

このサンプル スクリプトは、Service Fabric クラスターに接続し、プロビジョニングされているすべてのアプリケーションを列挙します。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>次の手順

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
