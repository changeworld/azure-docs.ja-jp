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
ms.openlocfilehash: 12f0f17a7c25696d2c8ff5b427f4b103617e5678
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010726"
---
コマンド プロンプトまたはシェル ウィンドウで、numpy と cython を使用して Conda パッケージ マネージャー環境を作成してアクティブ化します。 この例では Python 3.6 を使用します。

  + Windows の場合:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       conda activate myenv
       ```

  + Linux またｈ MacOS の場合:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       source activate myenv
       ```

SDK をインストールします。
   ```sh 
   pip install azureml-sdk
   ```
