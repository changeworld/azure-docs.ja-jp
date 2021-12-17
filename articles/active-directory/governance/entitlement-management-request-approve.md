---
title: アクセス要求を承認または拒否する - Azure AD エンタイトルメント管理
description: マイ アクセス ポータルを使用して、Azure Active Directory のエンタイトルメント管理でアクセス パッケージへの要求を承認または拒否する方法を学習します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: f44390d042f12336c6556d05c4fe1332e822e068
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112963764"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス要求を承認または拒否する

Azure AD のエンタイトルメント管理では、アクセス パッケージの承認を要求し、1 人または複数の承認者を選択するようにポリシーを構成することができます。 この記事では、指定された承認者がアクセス パッケージの要求をどのように承認または拒否できるかについて説明します。

## <a name="open-request"></a>要求を開く

アクセス要求を承認または拒否する最初の手順は、承認待ちのアクセス要求を見つけて開くことです。 アクセス要求を開く方法は 2 つあります。

**事前に必要なロール:** 承認者

1. 要求を承認または拒否するよう求める、Microsoft Azure からのメールを検索します。 メールの例を以下に示します。

    ![アクセス パッケージに対する要求を承認するよう求めるメール](./media/entitlement-management-shared/approver-request-email.png)

1. **[Approve or deny request]\(要求を承認または拒否する\)** リンクをクリックして、アクセス要求を開きます。

1. マイ アクセス ポータルにサインインします。

メールが届いていない場合は、これらの手順に従って、承認待ちのアクセス要求を見つけることができます。

1. [https://myaccess.microsoft.com](https://myaccess.microsoft.com) でマイ アクセス ポータルにサインインします。  (米国政府の場合、マイ アクセス ポータルのリンクのドメインは `myaccess.microsoft.us` になります)。

1. 左側のニューで、 **[承認]** をクリックして、承認待ちのアクセス要求のリストを表示します。

1. **[保留中]** タブで、要求を見つけます。

## <a name="view-requestors-answers-to-questions"></a>質問に対する要求元の回答を表示する

1. マイ アクセスの **[承認]** タブに移動します。

1. 承認する要求に移動し、 **[詳細]** をクリックします。 決定する準備ができている場合は、 **[承認]** または **[拒否]** をクリックすることもできます。

1. **[要求の詳細]** をクリックします。

    ![マイ アクセス ポータル - アクセス要求 - [要求の詳細] のクリック](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. 要求元が指定した情報は、パネルの下部に表示されます。

    ![要求の詳細を示すスクリーンショット](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. 要求元が指定した情報に基づいて、要求を承認または拒否することができます。 ガイダンスについては、「要求を承認または拒否する」の手順を参照してください。

## <a name="approve-or-deny-request"></a>要求を承認または拒否する

承認待ちのアクセス要求を開いた後、承認または拒否の決定に役立つ詳細を確認することができます。

**事前に必要なロール:** 承認者

1. **[表示]** リンクをクリックして、[アクセス要求] ウィンドウを開きます。

1. **[詳細]** をクリックして、アクセス要求の詳細を確認します。

    詳細には、ユーザーの名前、組織、アクセスの開始日と終了日 (指定されている場合)、業務上の正当な理由、要求の送信日、要求の有効期限が含まれます。

1. **[承認]** または **[拒否]** をクリックします。

1. 必要に応じて、理由を入力します。

    ![要求を承認または拒否するページを示すスクリーンショット。](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. **[送信]** をクリックして、決定を送信します。

    ポリシーがステージ内の複数の承認者によって構成されている場合は、1 人の承認者のみが承認待ちに関する決定を行う必要があります。 承認者がアクセス要求に対する決定を送信した後、要求は完了し、他の承認者は要求を承認することも拒否することもできなくなります。 他の承認者は、マイ アクセス ポータルで要求に関する決定と決定者を確認することができます。

    ステージで構成されたステージ内の承認者がアクセス要求を承認または拒否できない場合、要求は、構成された要求期間の後に期限切れになります。 ユーザーには、アクセス要求の有効期限が切れたことと、アクセス要求を再送信する必要があることが通知されます。

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージへのアクセスを要求する](entitlement-management-request-access.md)
- [要求プロセスとメール通知](entitlement-management-process.md)
