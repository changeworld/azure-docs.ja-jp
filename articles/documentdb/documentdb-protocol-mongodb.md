---
title: "DocumentDB における MongoDB のプロトコル対応とは | Microsoft Docs"
description: "DocumentDB における MongoDB のプロトコル対応とは 管理されたクラウドベースのサービスである Azure DocumentDB を、MongoDB 用に記述されたアプリのデータ ストアとして使用できるようにするものです。"
keywords: "MongoDB とは"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 2de367287a4bee05694070dbeeb4d4a458d47f11
ms.openlocfilehash: 38d811a7a08a6f8a6dd07a29e9a49bb34810ee91


---
# <a name="what-is-documentdb-protocol-support-for-mongodb"></a>DocumentDB における MongoDB のプロトコル対応とは

MongoDB 向けに作成されたアプリのデータ ストアとして DocumentDB データベースを使用できるようになりました。 つまり、MongoDB データベースの既存の[ドライバー](https://docs.mongodb.org/ecosystem/drivers/)を使用することで、MongoDB 用に記述されたアプリケーションが DocumentDB と通信し、MongoDB データベースではなく DocumentDB データベースを使用できるようになりました。 多くの場合、接続文字列を変更するだけで、MongoDB から DocumentDB に使用を切り替えることができます。 この機能を使用すると、既にある MongoDB のスキルとツールを無駄にすることなく、DocumentDB の完全に管理されたスケーラブルな NoSQL データベースを利用する MongoDB データベース アプリケーションを簡単に作成し、Azure クラウドで実行することができます。

## <a name="what-is-the-benefit-of-using-documentdb-protocol-support-for-mongodb"></a>MongoDB 用の DocumentDB プロトコル サポートを使用するメリット
**サーバー管理が不要** - DocumentDB は完全に管理されたサービスです。つまり、インフラストラクチャまたは仮想マシンを自分で管理する必要がありません。 DocumentDB は、20 を超える [Azure リージョン](https://azure.microsoft.com/regions/services/)で使用できます。

**無限のスケール** - スループットとストレージを分けてエラスティックにスケールできます。 容量を追加して&1; 秒あたり数百万件の要求に簡単に対応できます。

**エンタープライズ グレード** - DocumentDB では、ローカルとリージョンで障害が発生した場合に 99.99% の可用性とデータ保護を実現する、複数のローカル レプリカがサポートされます。 DocumentDB には、エンタープライズ グレードの[コンプライアンス認定](https://www.microsoft.com/trustcenter)とセキュリティ機能があります。 

**MongoDB 互換性** - MongoDB 用の DocumentDB プロトコル サポートは、MongoDB との互換性が確保されています。 既存のコード、アプリケーション、ドライバー、ツールを使用して DocumentDB を操作できます。 

詳細については、Scott Hanselman と DocumentDB プリンシパル エンジニアリング マネージャー Kirill Gavrylyuk による次の Azure Friday ビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>開始方法
[Azure Portal](https://portal.azure.com) で MongoDB 用のプロトコル サポートを使用して DocumentDB アカウントを作成し、接続を新しいアカウントにスワップします。 

"*これで終了です。*"

詳細な手順については、[アカウントの作成](documentdb-create-mongodb-account.md)に関する記事と[アカウントへの接続](documentdb-connect-mongodb-account.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
* [MongoDB 用のプロトコル サポートを使用した DocumentDB アカウントの作成](documentdb-create-mongodb-account.md)に関するチュートリアルに従って、DocumentDB アカウントを作成します。
* [MongoDB 用のプロトコル サポートを使用した DocumentDB アカウントへの接続](documentdb-connect-mongodb-account.md)に関するチュートリアルに従って、アカウントの接続文字列の情報を取得する方法について学習します。
* チュートリアル「[MongoDB のプロトコル対応の DocumentDB アカウントで MongoChef を使用する](documentdb-mongodb-mongochef.md)」に従って、MongoChef で DocumentDB データベースと MongoDB アプリの接続を作成する方法について学習します。
* MongoDB のプロトコル対応 DocumentDB の [サンプル](documentdb-mongodb-samples.md)を体験します。




<!--HONumber=Jan17_HO3-->


