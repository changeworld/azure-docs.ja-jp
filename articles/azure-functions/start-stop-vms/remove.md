---
title: VM の開始/停止 v2 (プレビュー) の削除の概要
description: この記事では、VM の開始/停止 v2 (プレビュー) 機能を削除する方法について説明します。
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111105"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>VM の開始/停止 v2 (プレビュー) を削除する方法

Azure VM の実行状態を管理するために VM の開始/停止 v2 (プレビュー) 機能を有効にした後、使用を停止することもできます。 この機能を削除するには、VM の開始/停止 v2 (プレビュー) をサポートするために次のリソースを格納する、専用のリソース グループを削除します。

- Azure Functions アプリケーション
- Azure Logic Apps のスケジュール
- Application Insights のインスタンス
- Azure ストレージ アカウント

## <a name="delete-the-dedicated-resource-group"></a>専用のリソース グループを削除する

リソース グループを削除するには、「[Azure Resource Manager のリソース グループとリソースの削除](../../azure-resource-manager/management/delete-resource-group.md)」記事に記載されている手順に従います。

## <a name="next-steps"></a>次のステップ

この機能を再デプロイするには、[開始/停止 v2 (プレビュー) のデプロイ](deploy.md)に関する記事を参照してください。