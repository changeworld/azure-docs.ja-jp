---
title: 管理 API とサービス API の違い - Azure Batch | Microsoft Docs
description: API は、Azure Batch サービスのさまざまなレベルで機能します。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672760"
---
# <a name="service-level-and-management-level-apis"></a>サービス レベル API と管理レベル API

Azure Batch には、サービス レベル用と管理レベル用の 2 つの API のセットがあります。 名前はよく似ていますが、返される結果は異なります。 アクティビティ ログが必要な場合は、管理 API を使用する必要があります。 サービス レベル API は、Azure Resource Management レイヤーをバイパスし、ログには記録されません。


Batch 管理と Batch サービスには両方ともプール用の API があります。例を次に示します。 
- プールを削除するこの API は、Batch アカウント (https://docs.microsoft.com/rest/api/batchservice/pool/delete ) で直接ターゲットになります。 

- プール (https://docs.microsoft.com/rest/api/batchmanagement/pool/delete ) を削除するこの API は、management.azure.com レイヤーを対象としています。

