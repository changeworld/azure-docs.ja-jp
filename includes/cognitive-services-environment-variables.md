---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841461"
---
## <a name="configure-an-environment-variable-for-authentication"></a>認証用の環境変数を構成する

アプリケーションは、使用する Cognitive Services へのアクセスを認証する必要があります。 認証するために、Azure リソースのキーを格納する環境変数を作成することをお勧めします。 

キーを用意したら、アプリケーションを実行しているローカル マシンの新しい環境変数に書き込みます。 環境変数を設定するには、コンソール ウィンドウを開いて、お使いのオペレーティング システムの手順に従います。 `your-key` をお使いのリソースのいずれかのキーで置き換えます。

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    実行中のプログラムのうち、環境変数の読み取りを必要とするプログラム (コンソール ウィンドウを含む) については、環境変数を追加した後で再起動が必要となる場合があります。 たとえば、Visual Studio をエディターとして使用している場合、サンプルを実行する前に Visual Studio を再起動します。

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。
    
* macOS
    
    次のように .bash_profile を編集し、環境変数を追加します。
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。
