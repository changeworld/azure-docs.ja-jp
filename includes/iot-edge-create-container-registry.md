---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "75564786"
---
## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このチュートリアルでは、Azure IoT Tools の拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。

コンテナー イメージは、Docker と互換性のある任意のレジストリを使用して格納できます。 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。

コンテナー レジストリがまだない場合は、次の手順に従って、Azure に新しいコンテナー レジストリを作成します。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[コンテナー]**  >  **[コンテナー レジストリ]** の順に選択します。

2. コンテナー レジストリを作成するには、以下の値を指定します。

   | フィールド | 値 |
   | ----- | ----- |
   | レジストリ名 | 一意の名前を指定します。 |
   | サブスクリプション | ドロップダウン リストで、サブスクリプションを選択します。 |
   | Resource group | IoT Edge のクイック スタートおよびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | Location | 近くの場所を選択します。 |
   | 管理者ユーザー | **[有効]** に設定します。 |
   | SKU | **[Basic]** を選択します。 |

3. **作成** を選択します。

4. コンテナー レジストリが作成された後、それを参照し、左側のペインの **[設定]** の下にあるメニューから **[アクセス キー]** を選択します。

5. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーして、どこか都合のいい場所に保存します。 このチュートリアルではこれらの値を使用して、コンテナー レジストリへのアクセスを提供します。

   ![コンテナー レジストリのログイン サーバー、ユーザー名、パスワードをコピーします。](./media/iot-edge-create-container-registry/registry-access-key.png)