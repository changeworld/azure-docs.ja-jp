<properties
   pageTitle="会社のインターネット ドメインで Traffic Manager ドメインが参照されるようにする"
   description="この記事は、会社のドメイン名で Traffic Manager ドメイン名が参照されるようにするために役立ちます。"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# 会社のインターネット ドメインで Traffic Manager ドメインが参照されるようにする

会社のドメイン名で Traffic Manager ドメイン名が参照されるようにするには、インターネット DNS サーバー上の DNS リソース レコードを変更して、Traffic Manager プロファイルのドメイン名に会社のドメイン名をマップする CNAME レコード タイプを使用します。Traffic Manager プロファイルの [構成] ページの **全般** セクションで、Traffic Manager ドメイン名を確認できます。

たとえば、会社のドメイン名 **www.contoso.com** で Traffic Manager ドメイン名 **contoso.trafficmanager.net** を参照させるには、次のように DNS リソース レコードを更新します。

    www.contoso.com IN CNAME contoso.trafficmanager.net

これで、*www.contoso.com* へのすべてのトラフィックが *contoso.trafficmanager.net* にリダイレクトされるようになります。

>[AZURE.IMPORTANT]*contoso.com* などの第 2 レベル ドメインで、Traffic Manager ドメインを参照させることはできません。これは、第 2 レベルのドメイン名の CNAME レコードを許可しない DNS プロトコルの制限です。

## 関連項目

[Traffic Manager での負荷分散方法について](traffic-manager-load-balancing-methods.md)

[Traffic Manager の構成タスク](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Traffic Manager の概要](traffic-manager-overview.md)
 

<!---HONumber=August15_HO6-->