---
author: baanders
description: Azure Digital Twins のインクルード ファイル - インスタンスを設定するための前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 8ea371e25f149c52412153aa0b9c3b7475dc77fd
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303578"
---
この記事で Azure Digital Twins を操作するには、まず **Azure Digital Twins インスタンス** とそれを使用するために必要なアクセス許可を設定する必要があります。 以前の作業で Azure Digital Twins インスタンスが既に設定されている場合は、そのインスタンスを使用できます。

それ以外の場合は、「[インスタンスと認証を設定する](../articles/digital-twins/how-to-set-up-instance-portal.md)」の手順に従います。 説明には、各手順が正常に完了し、新しいインスタンスを使用する準備ができていることを確認するための手順も含まれています。

Azure Digital Twins インスタンスを設定したら、後でインスタンスに接続するために必要な次の値をメモしておきます。
* インスタンスの "**_ホスト名_**"。 この値は、Azure portal ([[概要]](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) で確認できます。
* インスタンスの作成に使用した **Azure サブスクリプション** (名前または ID のいずれか)。 Azure Digital Twins インスタンスのサブスクリプションを確認するには、[Azure portal](https://portal.azure.com) でインスタンスの同じ *[概要]* ページを表示します。
