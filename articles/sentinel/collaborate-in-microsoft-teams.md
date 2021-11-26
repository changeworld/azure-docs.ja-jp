---
title: Microsoft Teams で Microsoft Azure Sentinel インシデント チームを使用して共同作業を行う | Microsoft Docs
description: Microsoft Azure Sentinel から Microsoft Teams に接続して、Microsoft Azure Sentinel データを使用してチームで他のメンバーと共同作業する方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0a560103953aab15be807c4a51be034cdb8ff1b9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716146"
---
# <a name="collaborate-in-microsoft-teams-public-preview"></a>Microsoft Teams での共同作業 (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Azure Sentinel では、[Microsoft Teams](/microsoftteams/) との直接統合がサポートされているため、特定のインシデントに対するチームワークに直接参加することができます。


> [!IMPORTANT]
> Microsoft Teams との統合は現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="overview"></a>概要

Microsoft Azure Sentinel から直接 Microsoft Teams と統合することで、チームが組織全体で、また外部の利害関係者とシームレスに共同作業を行うことができます。

Microsoft Teams と Microsoft Azure Sentinel *インシデント チーム* を使用して、関係者間のコミュニケーションと調整を一元化します。 インシデント チームの能力が特に発揮されるのは、重要度が高い進行中のインシデントに特化した会議ブリッジとしての役割を担うときです。

コミュニケーションとコラボレーションに Microsoft Teams を既に使用している組織は、Microsoft Azure Sentinel の統合を使用して、会話や日常業務に直接セキュリティ データを取り入れることができます。 

Microsoft Azure Sentinel インシデント チームには、お客様のチームが最も関連性の高いデータを手元に置いておけるように、Microsoft Azure Sentinel からの最新のデータが常に保持されています。

## <a name="required-permissions"></a>必要なアクセス許可

Microsoft Azure Sentinel からチームを作成するには:

- チームを作成するユーザーには、Microsoft Azure Sentinel のインシデントの書き込みアクセス許可が必要です。 たとえば、[Microsoft Azure Sentinel レスポンダー](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) ロールは、この特権に最適な最小ロールです。

- チームを作成するユーザーには、Microsoft Teams でチームを作成するためのアクセス許可も必要です。

- [閲覧者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)、[レスポンダー](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)、[共同作成者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)のロールを持つユーザーを含む、すべての Microsoft Azure Sentinel ユーザーは、アクセスを要求して、作成されたチームにアクセスできます。

## <a name="use-an-incident-team-to-investigate"></a>インシデント チームを使用して調査する

インシデントから直接 Microsoft Teams を 統合して、"*インシデント チーム*" と共に調査します。

**インシデント チームを作成するには**:

1. Microsoft Azure Sentinel の **[脅威の管理]**  >  **[インシデント]** グリッドで、現在調査しているインシデントを選択します。

1. 右側に表示されるインシデント ウィンドウの下部で **[アクション]**  >  **[チームの作成]** を選択します。

    [ ![インシデント チームで共同作業を行うチームを作成する。](media/collaborate-in-microsoft-teams/create-team.png) ](media/collaborate-in-microsoft-teams/create-team.png#lightbox)

    右側に **[新しいチーム]** ウィンドウが開きます。 インシデント チームに対して次の設定を定義します。

    - **[チーム名]** : インシデントの名前として自動的に定義されます。 簡単に識別できるように、必要に応じて名前を変更します。
    - **[説明]** : インシデント チームのわかりやすい説明を入力します。
    - **[グループの追加]** : インシデント チームに追加する Azure AD グループを 1 つ以上選択します。 このページでは、個々のユーザーはサポートされていません。 個々のユーザーを追加する必要がある場合は、チームの作成後に、[Microsoft Teams で追加します](#more-users)。

        > [!TIP]
        > 同じチームで定期的に作業する場合は、星 :::image type="icon" source="media/collaborate-in-microsoft-teams/save-as-favorite.png" border="false"::: を選択してお気に入りとして保存することができます。
        >
        > 次回チームを作成するときに、お気に入りが自動的に選択されます。 次に作成するチームからこれを削除する場合は、 **[削除]** :::image type="icon" source="media/collaborate-in-microsoft-teams/delete-user-group.png" border="false"::: を選択するか、星 :::image type="icon" source="media/collaborate-in-microsoft-teams/save-as-favorite.png" border="false"::: をもう一度選択して、チームをお気に入りから完全に削除します。
        >

1. グループの追加が完了したら、 **[作成]** を選択して インシデント チームを作成します。

    インシデント ウィンドウが更新され、 **[チーム名]** タイトルの下に新しいインシデント チームへのリンクが表示されます。

    [ ![インシデントに追加された Teams 統合リンクをクリックする。](media/collaborate-in-microsoft-teams/teams-link-added-to-incident.jpg) ](media/collaborate-in-microsoft-teams/teams-link-added-to-incident.jpg#lightbox)


1. **Teams 統合** リンクを選択して Microsoft Teams に切り替えます。この **[インシデント ページ]** タブには、ご自分のインシデントに関するすべてのデータが一覧表示されます。

    [ ![Microsoft Teams のインシデント ページ。](media/collaborate-in-microsoft-teams/incident-in-teams.jpg)](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

必要に応じて、Teams で調査に関する会話を続けます。 完全なインシデントの詳細は、Teams で直接入手できます。

> [!TIP]
> - <a name="more-users"></a>個々のユーザーをチームに追加する必要がある場合は、 **[投稿]** タブの **[ユーザーを追加する]** ボタンを使用すると、Microsoft Teams で追加できます。
>
> - [インシデントを閉じる](investigate-cases.md#closing-an-incident)と、Microsoft Teams で作成した関連インシデント チームがアーカイブされます。 インシデントが再開されると、関連するインシデント チームも Microsoft Teams で再開されるため、中断したところから会話を続けることができます。
>

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [チュートリアル: Microsoft Azure Sentinel でインシデントを調査する](investigate-cases.md)
- [Microsoft Teams のチームとチャネルの概要](/microsoftteams/teams-channels-overview/)
