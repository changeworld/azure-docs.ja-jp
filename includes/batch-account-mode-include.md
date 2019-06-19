---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b5bb5da2b68bae36353b81a04993814b395ce67b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181529"
---
> [!NOTE]
> Batch アカウントの作成時には、**ユーザー サブスクリプション**と **Batch サービス**のいずれかの*プール割り当て*モードを選択することができます。 ほとんどの場合、既定の Batch サービス モードを選択することになります。このモードでは、Azure で管理されているサブスクリプションにバックグラウンドでプールが割り当てられます。 もう一方のユーザー サブスクリプション モードでは、プールの作成時に Batch VM などのリソースがサブスクリプションに直接作成されます。 [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/) を使用して Batch プールを作成する場合には、ユーザー サブスクリプション モードを選択する必要があります。 また、ユーザー サブスクリプション モードで Batch アカウントを作成するには、ご利用のサブスクリプションを Azure Batch に登録し、アカウントを Azure Key Vault に関連付ける必要があります。