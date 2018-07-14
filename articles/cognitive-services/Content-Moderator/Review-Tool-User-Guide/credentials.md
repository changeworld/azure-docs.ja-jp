---
title: Azure Content Moderator の資格情報 | Microsoft Docs
description: API で使用する Content Moderator 資格情報を管理します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372920"
---
# <a name="manage-credentials"></a>資格情報を管理する

Content Moderator 資格情報は、次の場所で作成されます。

- [Azure ポータル](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Content Moderator レビュー ツール](http://contentmoderator.cognitive.microsoft.com/)

この記事では、それらがある場所と相互の関連について説明します。

## <a name="the-azure-portal"></a>Azure ポータル

Azure portal ダッシュボードで、Content Moderator アカウントを選択します。 **[リソース管理]** で、**[キー]** を選択します。 キーをコピーするには、キーの右側にあるアイコンを選択します。

![Azure portal での Content Moderator キー](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>レビュー ツールで Azure アカウントを使用する方法
レビュー API で Azure キーを使用するには、次のスクリーンショットの **[プロパティ]** 画面に表示されているリソース ID をコピーし、次の **[リソース ID]** セクションに表示されているようにレビュー ツールの資格情報画面で **[Whitelisted Resource Id(s)]\(ホワイト リソース ID\)** フィールドにそれを入力します。 

Content Moderator 内で使用可能なワークフローに Azure キーを使用するには、次の **[ワークフロー]** セクションに示されているように、**[ワークフロー設定]** セクションの **[Ocp-Apim-Subscription-Key]** フィールドにそれを入力します。

> [!NOTE]
> レビュー ツール内の 2 つの場所を Azure サブスクリプションのキーとリソース ID に置き換えた後、[資格情報] 画面に表示されている**試用版 Ocp-Apim-Subscription-Key** は使用されなくなりますが、いつでも使用できます。
> 試用版のキーでは、1 要求/秒 (RPS) で 1 か月あたり最大 5,000 トランザクションに制限されます。

![Azure portal での Content Moderator リソース ID](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>レビュー ツール

レビュー ツールのダッシュボードの **[設定]** タブで、**[資格情報]** を選択します。

![レビュー ツールでの Content Moderator 資格情報](images/credentials-trial-resource-workflow.PNG)

次のセクションでは、前の画像をさらに詳しく調べます。


### <a name="api"></a>API

最初の部分には、レビュー チームの作成の一環として生成された**レビュー API エンドポイント**、**チーム ID**、および **Ocp-Apim-Subscription-Key (Content Moderator の試用キー)** が一覧表示されます。 これらは、レビュー API を含むすべての Content Moderator API の呼び出しに使用します。

また、API エンドポイントのリージョン識別子にも注意してください。 たとえば、**westus** は "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0" 内のリージョンです

![レビュー ツールでの Content Moderator キー](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>Resource ID

2 番目の部分は、リソース ID がない空の状態で開始します。 **レビュー API で Azure サブスクリプション キーを使用するには、前に示したリソース ID 画面に移動し、表示されるフィールドにそれをコピーします**。 **+** を押してリソース ID を保存します。

> [!NOTE]
> Content Moderator サブスクリプションのリージョンは、チームを認識し、チームのデータにアクセスするために、レビュー チームのリージョンと一致する必要があります。 たとえば、このページのイメージで、**West US** リージョン **(4)** には、Content Moderator Azure サブスクリプションとレビュー チームが含まれています。

![レビュー ツールでの Content Moderator リソース ID](images/credentials-resourceids.PNG)


### <a name="workflows"></a>Workflows

3 番目の部分は、ワークフローを実行するために使用される情報を示します。 既定では、自動生成された試用キーの表示から開始します。 

**Azure サブスクリプションを取得するときに、それを Azure キーで更新**します。 他の 2 つのフィールドでは、それぞれ画面の文字と画像の評価の操作で用語と画像の一覧を使用できます。

![レビュー ツールでの Content Moderator ワークフロー資格情報](images/credentials-workflow.PNG)


## <a name="next-steps"></a>次の手順

* [ワークフロー](workflows.md)で Content Moderator 資格情報を使用する方法について説明します。
