---
title: Microsoft Defender for Cloud のアクセス許可 | Microsoft Docs
description: この記事では、Microsoft Defender for Cloud でロールベースのアクセス制御を使用してアクセス許可をユーザーに割り当て、ロールごとに許可するアクションを特定する方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/04/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d274c06774377c7b70f3ba81fb1bacbaa2c837ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091925"
---
# <a name="permissions-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud のアクセス許可

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) が使用されており、これによって提供される[組み込みのロール](../role-based-access-control/built-in-roles.md)は、Azure のユーザー、グループ、およびサービスに割り当てることができます。

Defender for Cloud は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Defender for Cloud では、サブスクリプションまたはリソースのリソース グループに対する所有者、共同作業者、または閲覧者のロールが割り当てられているときに、リソースに関連した情報のみが表示されます。

組み込みロールに加えて、Defender for Cloud に固有のロールが 2 つあります。

* **セキュリティ閲覧者**: このロールに属しているユーザーには、Defender for Cloud に対する閲覧権限があります。 推奨事項、警告、セキュリティ ポリシー、セキュリティの状態を閲覧できますが、変更することはできません。
* **セキュリティ管理者**:このロールに属しているユーザーは、セキュリティ閲覧者と同じ権限があり、セキュリティ ポリシーの更新、アラートと推奨事項の無視も可能になります。

> [!NOTE]
> セキュリティ閲覧者とセキュリティ管理者のセキュリティ ロールには、Defender for Cloud でのみアクセス権が付与されています。 ストレージ、Web およびモバイル、モノのインターネットなど、他の Azure サービスへのアクセス権はありません。

## <a name="roles-and-allowed-actions"></a>ルールと許可されているアクション

次の表は、Defender for Cloud でのロールと許可されているアクションを示しています。

| **操作**                                                                                                                      | [セキュリティ閲覧者](../role-based-access-control/built-in-roles.md#security-reader) / <br> [Reader](../role-based-access-control/built-in-roles.md#reader) | [Security Admin](../role-based-access-control/built-in-roles.md#security-admin) | [共同作成者](../role-based-access-control/built-in-roles.md#contributor) / [所有者](../role-based-access-control/built-in-roles.md#owner)| [Contributor](../role-based-access-control/built-in-roles.md#contributor)| [所有者](../role-based-access-control/built-in-roles.md#owner)|
|:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
||||**(リソース グループ レベル)**|**(サブスクリプション レベル)**|**(サブスクリプション レベル)**|
| イニシアティブを追加する、または割り当てる (規制コンプライアンス標準を含む)                                                         | -                             | -              | -                                                      | -                        | ✔                 |
| セキュリティ ポリシーを編集する                                                                                                        | -                             | ✔             | -                                                      | -                        | ✔                 |
| Microsoft Defender プランを有効または無効にする                                                                                             | -                             | ✔             | -                                                      | -                        | ✔                 |
| 自動プロビジョニングを有効または無効にする                                                                                          | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| アラートを無視する                                                                                                              | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| セキュリティに関する推奨事項をリソースに適用する</br> (および [[修正する]](implement-security-recommendations.md#fix-button) を使用する) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| アラートと推奨事項を表示する                                                                                             | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |
||||||

> [!NOTE]
> タスクを実行するために必要となる最小限の権限ロールをユーザーに割り当てることをお勧めします。 たとえば、リソースのセキュリティ正常性に関する情報の表示のみが必要で、推奨事項の適用やポリシーの編集などの操作を行う必要がないユーザーには、閲覧者ロールを割り当ててください。

## <a name="next-steps"></a>次のステップ
この記事では、Defender for Cloud で Azure RBAC を使用してユーザーにアクセス許可を割り当てる方法について説明し、各ロールに許可されているアクションを示しました。 サブスクリプションのセキュリティ状態を監視するために必要なロールの割り当てについて理解したら、セキュリティ ポリシーを編集し、推奨事項を適用して、次の方法を学習してください。

- [Defender for Cloud でセキュリティ ポリシーを設定する](tutorial-security-policy.md)
- [Defender for Cloud でセキュリティに関する推奨事項を管理する](review-security-recommendations.md)
- [Defender for Cloud でセキュリティ アラートの管理と対応を行う](managing-and-responding-alerts.md)
- [パートナー セキュリティ ソリューションを監視する](./partner-integration.md)
