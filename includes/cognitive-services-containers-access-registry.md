---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 27c6b59a458fb79b86d7064d710a01593a1745dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125126"
---
## <a name="use-docker-cli-to-authenticate-private-container-registry"></a>Docker CLI を使用してプライベート コンテナー レジストリを認証する

Cognitive Services コンテナーのプライベート コンテナー レジストリを認証する方法はいくつかありますが、コマンドラインからの推奨される方法は、[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) を使用することです。

次の例のように [docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用し、Cognitive Services コンテナーのプライベート コンテナー レジストリである `containerpreview.azurecr.io` にログインします。 *\<username\>* と *\<password\>* を Azure Cognitive Services チームから受け取った資格情報に指定されているユーザー名とパスワードにそれぞれ置き換えます。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

テキスト ファイルで資格情報をセキュリティ保護した場合は、次の例に示すように、`cat` コマンドを使用して、そのテキスト ファイルの内容を `docker login` コマンドに連結することができます。 *\<passwordFile\>* を、パスワードを含むテキスト ファイルのパスと名前に置き換え、 *\<username\>* を資格情報に指定されているユーザー名に置き換えます。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
