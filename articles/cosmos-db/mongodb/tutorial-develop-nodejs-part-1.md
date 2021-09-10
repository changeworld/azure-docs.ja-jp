---
title: Azure Cosmos DB の MongoDB 用 API を使用する Node.Js、Angular アプリ (パート 1)
description: このチュートリアル シリーズでは、Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成する方法について、動画を交えながら説明しています。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: ec1275027c0a145da59e44017d596229d04e1298
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038206"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用して Angular アプリを作成する
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

複数のパートから成るこのチュートリアルでは、Express と Angular を使用して Node.js で新しいアプリを作成した後、[Cosmos DB の MongoDB 用 API を使用して構成された Cosmos アカウント](mongodb-introduction.md)にそれを接続する方法を紹介します。

Azure Cosmos DB は、あらゆる規模に対応する、オープン API を備えた Microsoft の高速 NoSQL データベースです。 SLA によって裏付けられた速度と可用性、自動かつ即時のスケーラビリティ、さまざまな NoSQL エンジンに対応したオープンソース API を備えた最新のアプリを開発できます。

このマルチパート チュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * [Angular CLI で Node.js Express アプリを作成する](tutorial-develop-nodejs-part-2.md)
> * [Angular で UI を作成する](tutorial-develop-nodejs-part-3.md)
> * [Azure CLI を使用して Azure Cosmos DB アカウントを作成する](tutorial-develop-nodejs-part-4.md) 
> * [Mongoose を使って Azure Cosmos DB に接続する](tutorial-develop-nodejs-part-5.md)
> * [Post、Put、Delete の各関数をアプリに追加する](tutorial-develop-nodejs-part-6.md)

これと同じアプリを React で作成する方法については、 [React のチュートリアル ビデオ シリーズ](tutorial-develop-mongodb-react.md)をご覧ください。

## <a name="video-walkthrough"></a>ビデオ チュートリアル

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>完全なプロジェクト 

このチュートリアルでは、アプリケーションを作成する手順を段階的に説明しています。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="next-steps"></a>次のステップ

本チュートリアルのこのパートでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Cosmos DB を使った MEAN.js アプリの作成手順を簡単に説明しました。 

引き続き Node.js Express アプリを作成します。次のチュートリアル パートに進んでください。

> [!div class="nextstepaction"]
> [Angular CLI で Node.js Express アプリを作成する](tutorial-develop-nodejs-part-2.md)

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスターの仮想コアとサーバーの数のみを把握している場合は、[仮想コア数または vCPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください
