---
title: '失敗したサービス要求のトラブルシューティング: エラー 403 (禁止)'
titleSuffix: Azure Digital Twins
description: Azure Digital Twins からエラーの状態として 403 (禁止) が返されたとき、それを診断し、解決する方法について説明します。
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 9/23/2021
ms.openlocfilehash: 60c553faad2917ec57fd6e622348e9a694ff6fe2
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134243"
---
# <a name="troubleshooting-failed-service-request-error-403-forbidden"></a>失敗したサービス要求のトラブルシューティング: エラー 403 (禁止)

この記事では、Azure Digital Twins へのサービス要求から 403 エラーを受信した場合の原因と解決手順について説明します。 

## <a name="symptoms"></a>現象

このエラーは、認証を必要とするさまざまな種類のサービス要求で発生する可能性があります。 影響としては、API 要求が失敗して `403 (Forbidden)` のエラーの状態が返されます。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

多くの場合、このエラーは、サービスに対して Azure ロールベースのアクセス制御 (Azure RBAC) のアクセス許可が正しく設定されていないことを示しています。 Azure Digital Twins インスタンスの多くのアクションを行うには、管理しようとしているインスタンスに対して、"**Azure Digital Twins データ所有者**" ロールを保持している必要があります。 

### <a name="cause-2"></a>原因 #2

[アプリ登録](./how-to-create-app-registration-portal.md)で認証を行っている Azure Digital Twins と通信するためにクライアント アプリを使用している場合は、Azure Digital Twins サービスに対するアクセス許可がアプリ登録で設定されていないという理由で、このエラーが発生することがあります。

アプリ登録には、Azure Digital Twins API に対するアクセス許可が構成されている必要があります。 その後、アプリ登録に対してクライアント アプリが認証されると、そのアプリ登録に構成されたアクセス許可が付与されます。

## <a name="solutions"></a>ソリューション

### <a name="solution-1"></a>解決策 #1

1 つ目の解決策として、管理しようとしているインスタンスに対して、Azure ユーザーが Azure Digital Twins データ所有者ロールを保持していることを確認します。 このロールがない場合は、設定します。

このロールは以下とは異なります
* 以前、プレビュー期間中に付けられていたこのロールの名前の Azure Digital Twins 所有者 (プレビュー)。 この場合、ロールは同じですが、名前が変更されています。
* Azure サブスクリプション全体に対する "所有者" ロール。 "Azure Digital Twins データ所有者" は、Azure Digital Twins 内のロールであり、この個別の Azure Digital Twins インスタンスにスコープが設定されています。
* Azure Digital Twins での "所有者" ロール。 これらは 2 つの個別の Azure Digital Twins 管理ロールであり、"Azure Digital Twins データ所有者" は管理に使用する必要があるロールです。

#### <a name="check-current-setup"></a>現在の設定を確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>問題を修正する 

このロールが割り当てられていない場合は、**Azure サブスクリプション** 内で所有者ロールを保持するユーザーが次のコマンドを実行して、Azure ユーザーに **Azure Digital Twins インスタンス** 上での Azure Digital Twins データ所有者ロールを付与します。 

サブスクリプション上でご自身が所有者になっている場合は、このコマンドを自分で実行できます。 そうでない場合は、所有者に連絡して、このコマンドを代わりに実行してもらいます。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

このロールの要件と割り当てプロセスの詳細については、"*方法: インスタンスと認証を設定する (CLI またはポータル)* " に関するページの「[ユーザーのアクセス許可の設定](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)」セクションを参照してください。

このロールが既に割り当てられており、"**かつ**" クライアント アプリを認証するために Azure AD アプリ登録を使用しており、この解決策で 403 の問題が解決されなかった場合は、次の解決策に進むことができます。

### <a name="solution-2"></a>解決策 #2

クライアント アプリを認証するために Azure AD アプリ登録を使用している場合は、2 つ目の解決策として、アプリ登録に Azure Digital Twins サービスに対するアクセス許可が構成されていることを確認します。 構成されていない場合は、設定を行います。

#### <a name="check-current-setup"></a>現在の設定を確認する

アクセス許可が正しく構成されたかどうかを確認するには、Azure portal の [Azure AD アプリ登録の概要ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)に移動します。 ポータルの検索バーで "**アプリの登録**" を検索することで、このページにアクセスできます。

**[すべてのアプリケーション]** タブに切り替えると、サブスクリプションで作成されたすべてのアプリ登録が表示されます。

作成したアプリ登録がこの一覧に表示されます。 それを選択すると、その詳細が開かれます。

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Azure portal のアプリの登録ページのスクリーンショット。":::

最初に、Azure Digital Twins のアクセス許可の設定が登録時に正しく設定されたことを確認します。メニュー バーから **[マニフェスト]** を選択して、アプリ登録のマニフェスト コードを表示します。 コード ウィンドウの一番下までスクロールし、`requiredResourceAccess` の下のこれらのフィールドを探します。 値は、次のスクリーンショットの値と一致している必要があります。

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Azure portal の Azure AD アプリ登録のマニフェストのスクリーンショット。":::

次に、メニュー バーから **[API のアクセス許可]** を選択して、このアプリ登録に Azure Digital Twins に対する読み取り/書き込みアクセス許可が含まれていることを確認します。 次のようなエントリが表示されます。

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Azure Digital Twins に対して 'Read/Write Access' が表示されている、Azure portal の Azure AD アプリ登録の API のアクセス許可のスクリーンショット。":::

#### <a name="fix-issues"></a>問題を修正する

これが説明とは異なる表示になっている場合は、「[アプリの登録を作成する](./how-to-create-app-registration-portal.md)」で説明されているアプリ登録の設定方法に関する手順のようにします。

## <a name="next-steps"></a>次のステップ

新しい Azure Digital Twins インスタンスを作成して認証するための設定手順を確認してください。
* [インスタンスと認証を設定する (CLI)](how-to-set-up-instance-cli.md)

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)