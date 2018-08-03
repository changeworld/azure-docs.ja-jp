---
title: LUIS アプリのコラボレーションについて - Azure | Microsoft Docs
description: LUIS アプリには、1 人の所有者と省略可能なコラボレーターが必要です。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: fe5e35c2dcb08cdff9d92142558cf8d7ec81c36c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399573"
---
# <a name="collaborating"></a>コラボレーション

LUIS では、グループでアプリを作成できるように、コラボレーション機能が用意されています。

## <a name="luis-account"></a>LUIS アカウント
LUIS アカウントは、1 つの [Microsoft Live](https://login.live.com/) アカウントに関連付けられています。 各 LUIS アカウントには、そのアカウントがアクセスできるすべての LUIS アプリの作成に使用する、無料の[オーサリング キー](luis-concept-keys.md#authoring-key)が与えられます。 

LUIS アカウントは、多くの LUIS アプリを所有できます。

Active Directory ユーザー アカウントの詳細については、「[Azure Active Directory テナント ユーザー](luis-how-to-collaborate.md#azure-active-directory-tenant-user)」を参照してください。 

## <a name="luis-app-owner"></a>LUIS アプリ所有者
アプリを作成するアカウントが所有者です。 アプリごとに 1 人の所有者がいます。 所有者は、アプリの **[[設定]](luis-how-to-collaborate.md)** に表示されます。 アプリを削除できるのは、このアカウントです。 エンドポイント クォータが毎月の制限の 75% に達したときに電子メールを受信するのも、このアカウントです。 

## <a name="transfer-ownership"></a>所有権の移転
LUIS には所有権の移転の機能はありませんが、コラボレーターはアプリをエクスポートしてからインポートすることで、アプリを作成できます。 新しいアプリには別のアプリ ID が付きます。 新しいアプリをトレーニングして公開する必要があります。また、使用する新しいエンドポイントが必要です。

## <a name="luis-app-collaborators"></a>LUIS アプリ コラボレーター
アプリの所有者は、コラボレーターをアプリに追加できます。 所有者は、アプリのコラボレーターの電子メール アドレスを **[[設定]](luis-how-to-collaborate.md)** に追加する必要があります。 コラボレーターには、アプリへのフル アクセス権が付与されます。 コラボレーターがアプリを削除した場合、そのアプリはこのコラボレーターのアカウントから削除されますが、所有者のアカウントには残ります。 

コラボレーターと複数のアプリを共有する場合は、それぞれのアプリにコラボレーターの電子メールを追加する必要があります。 

## <a name="managing-multiple-authors"></a>複数の作成者の管理
[LUIS](luis-reference-regions.md#luis-website) Web サイトでは現在、トランザクション レベルの作成は提供されていません。 基本バージョンから独立したバージョンで作業することを作成者に許可できます。 次のセクションで、2 種類の方法について説明します。

### <a name="manage-multiple-versions-inside-the-same-app"></a>同じアプリ内で複数のバージョンを管理する
まず、各作成者用に、基本バージョンから[複製](luis-how-to-manage-versions.md#clone-a-version)します。 

各作成者は、アプリの自分のバージョンに変更を加えます。 各作成者は自分のモデルに満足したら、新しいバージョンを JSON ファイルにエクスポートします。  

エクスポートされたアプリは JSON 形式のファイルなので、変更点を比較することができます。 ファイルを結合して、新しいバージョンの JSON ファイルを 1 つ作成します。 JSON の **versionId** プロパティを変更して、マージされた新しいバージョンを示します。 このバージョンを元のアプリにインポートします。 

この方法に従うと、アクティブなバージョンが 1 つ、ステージング バージョンが 1 つ、公開済みのバージョンが 1 つ存在することになります。 この 3 種類のバージョンの結果を、対話型のテスト ウィンドウでを比較できます。

### <a name="manage-multiple-versions-as-apps"></a>複数のバージョンをアプリとして管理する
基本バージョンを[エクスポート](luis-how-to-manage-versions.md#export-version)します。 各作成者がこのバージョンをインポートします。 アプリをインポートするユーザーが、バージョンの所有者です。 アプリの変更が完了したら、バージョンをエクスポートします。 

エクスポートされたアプリは JSON 形式ファイルなので、変更点を基本のエクスポートと比較することができます。 ファイルを結合して、新しいバージョンの JSON ファイルを 1 つ作成します。 JSON の **versionId** プロパティを変更して、マージされた新しいバージョンを示します。 このバージョンを元のアプリにインポートします。

## <a name="next-steps"></a>次の手順

[バージョン管理](luis-concept-version.md)の概念を理解します。 

[アプリ設定](luis-how-to-collaborate.md)を参照して、LUIS アプリでコラボレーターを管理する方法を学習します。

オーサリング API を使用して[メールをアクセス リストに追加する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342)方法を確認します。