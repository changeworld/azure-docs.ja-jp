---
title: 管理 API とサービス API の違い
description: API は、Azure Batch サービスのさまざまなレベルで機能します。
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867590"
---
# <a name="service-level-and-management-level-apis"></a>サービス レベル API と管理レベル API

Azure Batch には、サービス レベル用と管理レベル用の 2 つの API のセットがあります。 名前はよく似ていますが、返される結果は異なります。 アクティビティ ログが必要な場合は、管理 API を使用する必要があります。 サービス レベル API は、Azure Resource Management レイヤーをバイパスし、ログには記録されません。


Batch 管理と Batch サービスには両方ともプール用の API があります。例を次に示します。 
- プールを削除するこの API は、Batch アカウント (https://docs.microsoft.com/rest/api/batchservice/pool/delete ) で直接ターゲットになります。 

- プール (https://docs.microsoft.com/rest/api/batchmanagement/pool/delete ) を削除するこの API は、management.azure.com レイヤーを対象としています。

