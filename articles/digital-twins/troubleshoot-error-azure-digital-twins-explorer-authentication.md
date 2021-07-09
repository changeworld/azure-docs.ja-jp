---
title: Azure Digital Twins Explorer の認証エラー
description: "\"認証失敗\" の原因と解決策: Azure Digital Twins Explorer の場合。"
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: a91053f9a21aebf1a99c35a3a982fd3ad5514d04
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110475657"
---
# <a name="authentication-failed"></a>認証に失敗しました

この記事では、ローカル コンピューターで [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) サンプルを実行しているときに "認証失敗" エラーを受け取ったときの原因と解決手順について説明します。 

## <a name="symptoms"></a>現象

Azure Digital Twins Explorer アプリケーションを設定して実行したとき、アプリで認証を行おうとすると次のエラー メッセージが表示されます。

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Azure Digital Twins Explorer の認証失敗エラー メッセージのスクリーンショット。":::

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

このエラーは、Azure Digital Twins インスタンスで必要な Azure のロールベースのアクセス制御 (Azure RBAC) のアクセス許可が Azure アカウントに設定されていない場合にも、発生するおそれがあります。 インスタンス内のデータにアクセスするには、読み取りまたは管理を行おうとしているインスタンスで、それぞれ、**Azure Digital Twins データ リーダー** または **Azure Digital Twins データ所有者** のロールが必要です。 

Azure Digital Twins のセキュリティとロールの詳細については、"[Azure Digital Twins ソリューションのセキュリティの概念](concepts-security.md)" に関する記事を参照してください。

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
* [方法: インスタンスと認証を設定する (CLI)](how-to-set-up-instance-cli.md)

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [概念:Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)
