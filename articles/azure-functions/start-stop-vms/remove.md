---
title: VM の開始/停止 v2 (プレビュー) の削除の概要
description: この記事では、VM の開始/停止 v2 (プレビュー) 機能を削除する方法について説明します。
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 1a22faba480eb9c130cc7c883abfc3e53ef58fe5
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970159"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>VM の開始/停止 v2 (プレビュー) を削除する方法

Azure VM の実行状態を管理するために VM の開始/停止 v2 (プレビュー) 機能を有効にした後、使用を停止することもできます。 この機能を削除するには、VM の開始/停止 v2 (プレビュー) をサポートするために次のリソースを格納する、専用のリソース グループを削除します。

- Azure Functions アプリケーション
- Azure Logic Apps のスケジュール
- Application Insights のインスタンス
- Azure ストレージ アカウント

> [!NOTE]
> デプロイ中に問題が発生した場合、VM の開始/停止 v2 (プレビュー) を使用しているときに問題が発生します。または、関連する質問がある場合、[GitHub](https://github.com/microsoft/startstopv2-deployments/issues) で問題を送信できます。 このプレビュー バージョンでは、[Azure サポート サイト](https://azure.microsoft.com/support/options/)から Azure サポート インシデントを提出することはできません。 

## <a name="delete-the-dedicated-resource-group"></a>専用のリソース グループを削除する

リソース グループを削除するには、「[Azure Resource Manager のリソース グループとリソースの削除](../../azure-resource-manager/management/delete-resource-group.md)」記事に記載されている手順に従います。

## <a name="next-steps"></a>次のステップ

この機能を再デプロイするには、[開始/停止 v2 (プレビュー) のデプロイ](deploy.md)に関する記事を参照してください。