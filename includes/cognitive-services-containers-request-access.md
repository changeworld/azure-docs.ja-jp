---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.openlocfilehash: 88d83676de1e7fa18c4c1dcbf347da8d685ba2fa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593335"
---
コンテナーへのアクセスを要求するには、[Cognitive Services コンテナー要求フォーム](https://aka.ms/cognitivegate)に記入して送信します。 このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、Azure Cognitive Services チームがそれをレビューして、プライベート コンテナー レジストリにアクセスするための条件を満たしていることを確認します。

> [!IMPORTANT]
> フォームでは、Microsoft アカウント (MSA) または Azure Active Directory (Azure AD) アカウントに関連付けられた電子メール アドレスを使用する必要があります。 受け入れ基準の詳細については、「[Cognitive Services - ゲート プロセス](../articles/cognitive-services/cognitive-services-gating-process.md)」を参照してください。

要求が承認されると、資格情報を取得してプライベート コンテナー レジストリにアクセスする方法を説明する手順が記載された電子メールを受け取ります。

## <a name="log-in-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのログイン

Cognitive Services コンテナーのプライベート コンテナー レジストリで認証を行うにはいくつかの方法があります。 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) を使用したコマンドライン メソッドの使用を推奨しています。

次の例のように [docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用して、Cognitive Services コンテナーのプライベート コンテナー レジストリである `containerpreview.azurecr.io` にログインします。 *\<username\>* と *\<password\>* を Azure Cognitive Services チームから受け取った資格情報に指定されているユーザー名とパスワードにそれぞれ置き換えます。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

テキスト ファイルに資格情報をセキュリティ保護した場合は、そのテキスト ファイルの内容を `docker login` コマンドに連結することができます。 次の例に示すように、`cat` コマンドを使用します。 *\<passwordFile\>* は、パスワードを含むテキスト ファイルのパスと名前に置き換えてください。 *\<username\>* は、資格情報に指定されているユーザー名に置き換えてください。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

