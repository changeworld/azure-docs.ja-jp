---
title: Azure Digital Twins Explorer の認証エラー
description: "\"認証失敗\" の原因と解決策: Azure Digital Twins Explorer の場合。"
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107491331"
---
# <a name="authentication-failed"></a>認証に失敗しました

この記事では、ローカル コンピューターで [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) サンプルを実行しているときに "認証失敗" エラーを受け取ったときの原因と解決手順について説明します。 

## <a name="symptoms"></a>現象

Azure Digital Twins Explorer アプリケーションを設定して実行したとき、アプリで認証を行おうとすると次のエラー メッセージが表示されます。

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Azure Digital Twins Explorer のエラー メッセージのスクリーンショット: Authentication failed. If you are running the app locally, please make sure that you are logged in to Azure on your host machine, or example by running 'az login' in a command prompt, by signing into Visual Studio or VS Code or by setting environment variables. If you need more information, please see the readme, or look up DefaultAzureCredential in the Azure.Identity documentation. If you are running adt-explorer hosted in the cloud, please make sure that your hosting Azure Function has a system-assigned managed identity set up. See the readme for more information. \(認証に失敗しました。アプリをローカルで実行している場合は、コマンド プロンプトで &quot;az login&quot; を実行するか、Visual Studio または VS Code にサインインするか、環境変数を設定するかして、ホスト コンピューターで Azure にログインしていることをご確認ください。詳細情報が必要な場合は、readme を参照するか、Azure.Identity のドキュメントで DefaultAzureCredential を参照してください。クラウドでホストされている adt-explorer を実行している場合は、ホストしている Azure 関数に、システム割り当てのマネージド ID が設定されていることをご確認ください。詳細については、readme を参照してください。\)":::

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

Azure Digital Twins Explorer アプリケーションで使用されている [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (`Azure.Identity` ライブラリの一部) により、ローカル環境内で資格情報が検索されます。

エラー テキストで示されているように、このエラーは、`DefaultAzureCredential` によって取得されるローカル資格情報を提供していない場合に、発生するおそれがあります。

Azure Digital Twins Explorer でローカル資格情報を使用する方法の詳細については、Azure Digital Twins の "*サンプル シナリオの検討のクイックスタート*" に関する記事の「[*ローカル Azure 資格情報を設定する*](quickstart-adt-explorer.md#set-up-local-azure-credentials)」セクションを参照してください。

### <a name="cause-2"></a>原因 #2

このエラーは、Azure Digital Twins インスタンスで必要な Azure のロールベースのアクセス制御 (Azure RBAC) のアクセス許可が Azure アカウントに設定されていない場合にも、発生するおそれがあります。 インスタンス内のデータにアクセスするには、読み取りまたは管理を行おうとしているインスタンスで、それぞれ、**Azure Digital Twins データ リーダー** または **Azure Digital Twins データ所有者** のロールが必要です。 

Azure Digital Twins のセキュリティとロールの詳細については、"[*Azure Digital Twins ソリューションのセキュリティの概念*](concepts-security.md)" に関する記事を参照してください。

## <a name="solutions"></a>ソリューション

### <a name="solution-1"></a>解決策 #1

最初に、必要な資格情報をアプリケーションに提供していることを確認します。

#### <a name="provide-local-credentials"></a>ローカルの資格情報を提供する

`DefaultAzureCredential` により、ローカルの Azure サインインからの情報を使用して、サービスに対する認証が行われます。 Azure の資格情報を提供するには、ローカル [Azure CLI](/cli/azure/install-azure-cli) ウィンドウで、または Visual Studio か Visual Studio Code で、Azure アカウントにサインインします。

`DefaultAzureCredential` によって受け付けられる資格情報の種類と、試行される順序を確認するには、[DefaultAzureCredential に関する Azure ID のドキュメント](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)を参照してください。

ローカル環境で適切な Azure アカウントに既にサインインしているのに問題が解決されない場合は、次の解決策に進んでください。

### <a name="solution-2"></a>解決策 #2

データを読み取るだけの場合は、Azure Digital Twins インスタンスに対する **Azure Digital Twins データ リーダー** ロールが、またデータを管理しようとしている場合は、インスタンスに対する **Azure Digital Twins データ所有者** ロールが、Azure ユーザーにあることを確認します。

このロールは以下とは異なることに注意してください。
* 以前、プレビュー期間中に付けられていたこのロールの名前 "*Azure Digital Twins 所有者 (プレビュー)* " (ロールは同じですが、名前が変更されています)
* Azure サブスクリプション全体に対する "*所有者*" ロール。 "*Azure Digital Twins データ所有者*" は、Azure Digital Twins 内のロールであり、この個別の Azure Digital Twins インスタンスにスコープが設定されています。
* Azure Digital Twins での "*所有者*" ロール。 これらは 2 つの個別の Azure Digital Twins 管理ロールであり、"*Azure Digital Twins データ所有者*" は管理に使用する必要があるロールです。

 このロールがない場合は、それを設定して問題を解決します。

#### <a name="check-current-setup"></a>現在の設定を確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>問題を修正する 

このロールが割り当てられていない場合は、お使いの **Azure サブスクリプション** での所有者ロールを持っているユーザーに、次のコマンドを実行して、お使いの Azure ユーザーに **Azure Digital Twins インスタンス** での適切なロールを付与してもらう必要があります。 

サブスクリプション上でご自身が所有者になっている場合は、このコマンドを自分で実行できます。 そうでない場合は、所有者に連絡して、このコマンドを代わりに実行してもらいます。 ロールの名前は、編集アクセスの場合は **Azure Digital Twins データ所有者**、読み取りアクセスの場合は **Azure Digital Twins データ リーダー** です。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

このロールの要件と割り当てプロセスの詳細については、「[*ユーザーのアクセス許可の設定*」セクション](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)を参照してください。"*方法:インスタンスと認証を設定する (CLI またはポータル)* " に関するページに含まれています

## <a name="next-steps"></a>次のステップ

新しい Azure Digital Twins インスタンスを作成して認証するための設定手順を確認してください。
* [*方法: インスタンスと認証を設定する (CLI)*](how-to-set-up-instance-cli.md)

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [*概念:Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)
