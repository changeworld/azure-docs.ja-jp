---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 5cd64b806392162fd3bee14ddaf607385ac05264
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807153"
---
検証ワークフローでは、テストを**スケジュール設定**するときに、ワークフローの作成時に指定したワークフロー レベルの一般的なパラメーターを使用します (「[Azure Stack Validation as a Service に使用される一般的なワークフロー パラメーター](../azure-stack-vaas-parameters.md)」を参照してください)。 テスト パラメーター値のいずれかが無効になった場合は、[ワークフロー パラメーターの変更](../azure-stack-vaas-monitor-test.md#change-workflow-parameters)に関するセクションの手順に従ってパラメーター値を再度指定する必要があります。

> [!NOTE]
> 既存のインスタンスに対して検証テストをスケジュール設定すると、ポータルの古いインスタンスに代わる新しいインスタンスが作成されます。 古いインスタンスのログは保持されますが、ポータルからアクセスできません。  
テストが正常に完了すると、**[スケジュール]** アクションが無効になります。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. テスト インスタンスをスケジュール設定するためのプロンプトを開くには、コンテキスト メニューの **[スケジュール]** を選択します。

1. テスト パラメーターを確認し、**[送信]** を選択してテストの実行をスケジュール設定します。