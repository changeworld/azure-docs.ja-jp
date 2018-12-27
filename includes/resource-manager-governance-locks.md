---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a69a739f36854cacd6b361ca2bd17d904e9c4c96
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38753678"
---
リソースのロックは、組織内のユーザーが重要なリソースを誤って削除したり変更したりするのを防ぎます。 ロールベースのアクセス制御とは異なり、リソースのロックはすべてのユーザーとロールに制限を適用します。 

ロック レベルは **CanNotDelete** または **ReadOnly** に設定できます。 ポータルでは、ロック レベルはそれぞれ **[削除]** と **[読み取り専用]** と表示されます。

* **CanNotDelete** は、正規ユーザーはリソースの読み取りと変更を実行できますが、削除は実行できないことを示します。 
* **ReadOnly** は、正規ユーザーはリソースの読み取りを実行できますが、リソースの更新は実行できないことを示します。 このロックの適用は、すべての正規ユーザーのアクセス許可を、**閲覧者**ロールによって与えられるアクセス許可に制限することに似ています。 

> [!TIP]
> **ReadOnly** ロックを適用する場合は気を付けてください。 読み取り操作のように見える場合でも、実際は追加のアクションが必要な操作もあります。 たとえば、**ReadOnly** ロックをストレージ アカウントに設定すると、すべてのユーザーがキーを一覧表示できなくなります。 返されるキーは書き込み操作に使用できるため、キーの一覧表示操作は POST 要求を介して処理されます。 **ReadOnly** ロックを App Service リソースに設定すると、Visual Studio のサーバー エクスプローラーの操作には書き込みアクセスが必要となるため、Visual Studio のサーバー エクスプローラーはリソース用のファイルを表示できなくなります。

親スコープでロックを適用すると、そのスコープ内のすべてのリソースは同じロックを継承します。 後で追加するリソースも、親からロックを継承します。 継承されるロックの中で最も制限の厳しいロックが優先されます。

Resource Manager のロックは、管理ウィンドウで実行され、`https://management.azure.com` に送信される操作で構成される操作のみに適用されます。 ロックは、リソースが独自の機能を処理する方法を制限しません。 リソースの変更は制限されますが、リソースの操作は制限されません。 たとえば、SQL Database に ReadOnly ロックを設定すると、データベースの削除または変更を実行できなくなりますが、 データベース内のデータの作成、更新、または削除は実行できます。 データのトランザクションは `https://management.azure.com` に送信されないため、これらの操作は許可されます。

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>組織でロックを作成または削除できるユーザー
管理ロックを作成または削除するには、`Microsoft.Authorization/locks/*` アクションにアクセスできる必要があります。 組み込みロールのうち、**所有者**と**ユーザー アクセス管理者**にのみこれらのアクションが許可されています。
