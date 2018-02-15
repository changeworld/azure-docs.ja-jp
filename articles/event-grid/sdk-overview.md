---
title: Azure Event Grid SDK
description: "Azure Event Grid の SDK について説明します。 これらの SDK は、管理、発行、および使用の機能を提供します。"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9c56e4c3314090ad55017d5c681a0cfd7bf5722c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>管理と発行のための Event Grid SDK

Event Grid の SDK を使うと、プログラムでリソースの管理やイベントの投稿を行うことができます。

## <a name="management-sdks"></a>管理 SDK

管理 SDK を使うと、Event Grid のトピックとサブスクリプションを作成、更新、および削除できます。 現時点では、次の SDK を利用できます。

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="publish-sdks"></a>発行 SDK

発行 SDK を使うと、トピックにイベントを投稿でき、認証、イベントの形成、指定されたエンドポイントへの非同期の投稿が行われます。 現時点では、次の SDK を利用できます。

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure CLI での Event Grid コマンドについては、[Azure CLI](/cli/azure/eventgrid) のページをご覧ください。
* PowerShell での Event Grid コマンドについては、[PowerShell](/powershell/module/azurerm.eventgrid) のページをご覧ください。