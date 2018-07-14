---
title: Content Moderator のレビュー ツール設定を構成する | Microsoft Docs
description: チーム、タグ、コネクタ、ワークフロー、資格情報を構成または取得します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372904"
---
# <a name="about-review-tool-settings"></a>レビュー ツールの設定について #

レビュー ツールのダッシュボードの [設定] タブを使用して、多くのコンポーネントを簡単に定義および変更できます。

![Content Moderator レビュー設定](images/settings-1.png)

## <a name="team-and-subteams"></a>チームおよびサブチーム ## 

このタブから、チームとサブチームを管理します。チームは 1 つだけ持つことができますが、[複数のサブチームを作成](subteams.md)し、将来のメンバーに招待を送信できます。 招待状を送信した後、それらを監視し、チーム メンバーのアクセス許可を変更し、他のユーザーを招待できます。 チーム メンバーが招待を承諾した後、別のサブチームにそれらのメンバーを割り当てることができます。 チーム メンバーのロールを管理者またはレビュー担当者のいずれかに設定することができます。管理者は他のユーザーを招待できますが、レビュー担当者はできません。

![Content Moderator のチーム設定](images/settings-2-team.png)

## <a name="tags"></a>タグ ##

ここでは、タグの短いコード、名前、説明を入力することで[カスタム タグを定義](tags.md)できます。 作成した後、レビュー時に使用可能になります。 可視性のオンとオフを切り替えて、異なるレビューに異なるタグを使用できます。

![Content Moderator のタグ設定](images/settings-3-tags.png)

## <a name="connectors"></a>コネクタ ##

ワークフローでは、レビュー ツールとの通信にコネクタを使用して機能を追加します。 レビュー ツールは、コンテンツをモデレートするために既定のワークフローを使用して Content Moderator API を呼び出します。 レビュー ツールにサインアップすると、Moderator API 資格情報が自動プロビジョニングされます。 コネクタが使用可能である限り、他のコネクタ API の統合もサポートされます。 いくつかのコネクタをすぐに利用できるようにしました。

[[コネクタ] タブ](connectors.md)でコネクタを管理します。 コネクタを追加または削除でき、特定のコネクタのサブスクリプション キーを検索できます。 [接続] をクリックして、これらをカスタム ワークフローに追加します。 

![Content Moderator のコネクタ設定](images/settings-4-connectors.png)

## <a name="workflows"></a>Workflows ##

ワークフローは [ワークフロー] タブから管理します。サンプル ファイルをアップロードしてワークフローをテストできます。 ([コネクタ] タブにある) 使用可能な API コネクタを使用して、画像とテキストの[カスタム ワークフローを定義](workflows.md)することもできます。 

![Content Moderator のワークフロー設定](images/settings-5-workflows.png)

## <a name="credentials"></a>資格情報 ##

このタブでは、Content Moderator に含まれている API (Image Moderation API、Text Moderation API、List Management API、Workflow API、Review API) を使用するために必要な Content Moderator サブスクリプション キーにすばやくアクセスできます。
 
![Content Moderator 資格情報](images/settings-6-credentials.png)
