---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/27/2018
ms.openlocfilehash: e86f2cc9fd2da585ad13b088442ebef9b55f186a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010735"
---
**aml_config/config.json** と同じディレクトリで、スクリプトを作成し、**pi.py** という名前にします。

次のコードをそのスクリプトにコピーします。
    
   ```python
   import random, math
   from azureml.core import Run
    
   run = Run.get_submitted_run()
    
   pi_counter = 0
   n_iter = 100000
   run.log("Number of iterations",n_iter)
    
   for i in range(1,n_iter):
       x = random.random()
       y = random.random()
       if x*x + y*y < 1.0:
           pi_counter += 1
       pi_value = 4.0*pi_counter / i
       if i%10000==0:
           error = math.pi-pi_value
           print(i, pi_value, error)
           run.log_row("Pi estimate",iteration=i,pi_value=pi_value)
           run.log_row("Error",iteration=i,error=error)
   ```

最後の `log_row` の呼び出しに注意してください。  このスクリプトを実行すると、ワークスペースにこれらの値が表示されます。