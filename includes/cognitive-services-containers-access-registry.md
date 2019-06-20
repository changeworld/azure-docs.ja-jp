---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063930"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Docker CLI を使用してプライベート コンテナー レジストリを認証する

Cognitive Services コンテナーのプライベート コンテナー レジストリはいくつかの方法で認証できますが、コマンド ラインからの推奨される方法は、[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) を使用することです。

次の例のように [`docker login` コマンド](https://docs.docker.com/engine/reference/commandline/login/)を使用し、Cognitive Services コンテナーのプライベート コンテナー レジストリである `containerpreview.azurecr.io` にログインします。 *\<username\>* と *\<password\>* を、Azure Cognitive Services チームから受け取った資格情報に指定されているユーザー名とパスワードにそれぞれ置き換えます。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

テキスト ファイルで資格情報をセキュリティ保護した場合は、次の例に示すように、`cat` コマンドを使用して、そのテキスト ファイルの内容を `docker login` コマンドに連結することができます。 *\<passwordFile\>* を、パスワードを含むテキスト ファイルのパスと名前に置き換え、 *\<username\>* を資格情報に指定されているユーザー名に置き換えます。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
