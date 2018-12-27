---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2c16e82ccf259a4cc5ae8fcf35b2dd6b5d50ee2d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740576"
---
リソースを Azure に展開する場合、展開するリソースの種類、展開先、設定方法を決定するときにとても柔軟に対応できます。 ただし、その柔軟性によって、組織内で許可するオプションよりも多くのオプションを使用できるようになる可能性があります。 Azure にリソースを展開することを検討する場合、次のような懸案事項があります。

* 特定の国でのデータの主権に関する法的要件を満たすにはどうすればよいか
* コストを管理するにはどうすればよいか
* 重要なシステムが誤って変更されないようにするにはどうすればよいか
* リソース コストを追跡し、正確に請求するにはどうすればよいか

この記事ではこのような問題に対応します。 具体的には次のとおりです。

> [!div class="checklist"]
* ユーザーをロールに割り当て、ロールを範囲に割り当てることで、想定したアクションのみを実行するアクセス許可がユーザーに付与されます。
* サブスクリプション内のリソースの規則を規定するポリシーを適用します。
* システムの重要なリソースをロックします。
* リソースにタグを付けることで、組織にとって意味のある値で追跡することができます。

この記事では、ガバナンスの実装に必要なタスクを中心に説明します。 概念に関する幅広い議論については、「[Azure でのガバナンス](../articles/security/governance-in-azure.md)」を参照してください。 
