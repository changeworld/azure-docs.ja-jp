<properties 
   pageTitle="Azure Search サービスでの使用状況と統計の監視" 
   description="Azure Search のリソースの消費量とインデックスのサイズの追跡" 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="07/08/2015"
   ms.author="heidist"/>

# Azure Search サービスでの使用状況と統計の監視

インデックスとドキュメントのサイズの増加率を追跡することで、サービスに設定した上限に達する前に、容量を事前に調整することができます。

リソースの使用状況を監視するために、数値と統計を [Azure ポータル](https://portal.azure.com)で簡単に表示できます。カスタム サービス管理ツールを作成すれば、情報をプログラムから取得することもできます。この記事では、この両方の手法について手順を説明します。

##ポータルでの数値とメトリックの表示 

1. [Azure ポータル](https://portal.azure.com)にサインインします。 

2. Azure Search サービスのサービス ダッシュボードを開きます。[ホーム] ページに、サービスのタイルが表示されます。またはジャンプ バーの [参照] で、サービスを参照することもできます。詳細な手順については、「[Create an Azure Search service in the portal](search-create-service-portal.md)」を参照してください。

[使用] セクションには、使用可能なリソースのうち、現在使用中の割合を示すメーターがあります。

  ![][1]

共有サービスのレプリカとパーティションは、それぞれ最大で 1 つであることに注意してください。また、サポート可能であるのは、合計 10,000 のドキュメントまたは 50 MB のデータまで (いずれかの先に達した方まで) です。

##REST API を使用したインデックス統計情報の取得

Azure Search REST API または .NET SDK を使用することにより、プログラムからサービス メトリックにアクセスできます。[インデクサー](https://msdn.microsoft.com/library/azure/dn946891.aspx)を使用して Azure SQL Database または DocumentDB からのインデックスの読み込む際には、必要な数値を取得するために追加されている API を使用できます。

  + [Get Index Statistics](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [ドキュメントの数](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [インデクサーの状態の取得](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## 次のステップ

「[制限および制約](https://msdn.microsoft.com/library/azure/dn798934.aspx)」を参照して、既存の容量が不十分である場合は、必要とされるパーティションとレプリカの組み合わせについて判断します。

サービス管理の詳細については、「[Microsoft Azure で検索サービスを管理する](search-manage.md)」を参照してください。

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 

<!---HONumber=August15_HO6-->