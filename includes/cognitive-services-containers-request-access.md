---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 08e6b5d109d6647f2a291f117f4993bae7598464
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302327"
---
まず、[Cognitive Services Vision Containers Request フォーム](https://aka.ms/VisionContainersPreview)に入力して、送信し、コンテナーへのアクセスを要求する必要があります。 このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 送信すると、Azure Cognitive Services チームによってフォームがレビューされ、プライベート コンテナー レジストリにアクセスするための条件を満たしていることが確認されます。

> [!IMPORTANT]
> フォームでは、Microsoft アカウント (MSA) または Azure Active Directory (Azure AD) アカウントに関連付けられた電子メール アドレスを使用する必要があります。

要求が承認されると、資格情報を取得して、プライベート コンテナー レジストリにアクセスする方法を説明する手順を記載した電子メールを受け取ります。

## <a name="log-in-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのログイン

Cognitive Services コンテナーのプライベート コンテナー レジストリを認証する方法はいくつかありますが、コマンドラインからの推奨される方法は、[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) を使用することです。

次の例のように [docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用し、Cognitive Services コンテナーのプライベート コンテナー レジストリである `containerpreview.azurecr.io` にログインします。 *\<username\>* と *\<password\>* を Azure Cognitive Services チームから受け取った資格情報に指定されているユーザー名とパスワードにそれぞれ置き換えます。

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

テキスト ファイルで資格情報をセキュリティ保護した場合は、次の例に示すように、`cat` コマンドを使用して、そのテキスト ファイルの内容を `docker login` コマンドに連結することができます。 *\<passwordFile\>* を、パスワードを含むテキスト ファイルのパスと名前に置き換え、*\<username\>* を資格情報に指定されているユーザー名に置き換えます。

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

