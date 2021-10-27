---
title: 'Azure Digital Twins Explorer のトラブルシューティング: 認証エラー'
titleSuffix: Azure Digital Twins
description: Azure Digital Twins Explorer の認証エラーを診断して解決する方法について説明します。
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 10/7/2021
ms.openlocfilehash: f03c85f912271dbd5d1cb7904f159d89f4a09ba6
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132628"
---
# <a name="troubleshooting-azure-digital-twins-explorer-authentication-error"></a>Azure Digital Twins Explorer のトラブルシューティング: 認証エラー

この記事では、ローカル コンピューターで [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) サンプルを実行しているときに "認証失敗" エラーを受け取ったときの原因と解決手順について説明します。 

## <a name="symptoms"></a>現象

Azure Digital Twins Explorer アプリケーションを設定して実行したとき、アプリで認証を行おうとすると次のエラー メッセージが表示されます。

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Azure Digital Twins Explorer の認証失敗エラー メッセージのスクリーンショット。":::

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

このエラーは、Azure Digital Twins インスタンスで必要な Azure のロールベースのアクセス制御 (Azure RBAC) のアクセス許可が Azure アカウントに設定されていない場合にも、発生するおそれがあります。 インスタンス内のデータにアクセスするには、読み取りまたは管理を行おうとしているインスタンスで、それぞれ、**Azure Digital Twins データ リーダー** または **Azure Digital Twins データ所有者** のロールが必要です。 

Azure Digital Twins のセキュリティとロールの詳細については、[Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)に関する記事を参照してください。

## <a name="solutions"></a>ソリューション

### <a name="solution-1"></a>解決策 #1

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

このロールの要件と割り当てプロセスの詳細については、「[ユーザーのアクセス許可の設定」セクション](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)を参照してください。"*方法:インスタンスと認証を設定する (CLI またはポータル)* " に関するページに含まれています

## <a name="next-steps"></a>次のステップ

新しい Azure Digital Twins インスタンスを作成して認証するための設定手順を確認してください。
* [インスタンスと認証を設定する (CLI)](how-to-set-up-instance-cli.md)

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)
