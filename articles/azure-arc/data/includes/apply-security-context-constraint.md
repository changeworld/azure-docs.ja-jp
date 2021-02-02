---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696068"
---
このセクションでは、セキュリティ コンテキスト制約 (SCC) を適用する方法について説明します。 プレビュー リリースでは、これらによってセキュリティ制約が緩和されます。 

1. カスタム セキュリティ コンテキスト制約 (SCC) をダウンロードします。 次のいずれかを使用します: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([未加工](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` 次のコマンドを実行すると、arc-data-scc.yaml がダウンロードされます。

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. SCC を作成します。

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. SCC をサービス アカウントに適用します。

   > [!NOTE]
   > ここで使用するものと同じ名前空間を、次の `azdata arc dc create` コマンドでも使用します。 たとえば `arc` とします。

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```