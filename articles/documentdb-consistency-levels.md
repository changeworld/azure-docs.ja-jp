<properties 
	pageTitle="DocumentDB の一貫性レベル" 
	description="DocumentDB には 4 つの一貫性レベルと、それに関連付けられたパフォーマンス レベルがあります。これを利用して、アプリケーション開発者は、一貫性、可用性、待機時間のトレードオフを予測のうえ決定できます。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2015" 
	ms.author="mimig"/>

#DocumentDB の一貫性レベル
強い一貫性 (Strong) を選ぶか、結果的な一貫性 (Eventual) を選ぶかは、多くの開発者を悩ませる問題です。現実には二者択一ではなく、その間にも、いくつかの一貫性レベルが存在します。実世界のほとんどのシナリオでは、一貫性、可用性、待機時間のトレードオフをきめ細かく選べることが、アプリケーションにとっての有益性につながります。DocumentDB には、明確に定義された 4 つの一貫性レベルが用意されており、それぞれにパフォーマンス レベルが関連付けられています。そのため、アプリケーション開発者は、確かな見通しに基づいて一貫性、可用性、待機時間のトレードオフを検討することが可能です。  
 
データベース アカウント、データベース、コレクション、ユーザー、アクセス許可など、すべてのシステム リソースには常に、読み取りとクエリに関して強い一貫性が確保され、それ以外の一貫性レベルを選ぶことはできません。一貫性レベルが適用されるのは、ユーザー定義のリソースだけです。DocumentDB では、ドキュメント、添付ファイル、ストアド プロシージャ、トリガー、UDF を含め、ユーザー定義リソースを対象としたクエリと読み取り操作に関して、次の 4 種類の一貫性レベルを利用できます。 

 - Strong
 - Bounded staleness 
 - Session
 - Eventual 

このように、きめ細かな一貫性レベルが明確に定義されていることによって、一貫性、可用性、パフォーマンスの最適なトレードオフを検討することができます。これらの一貫性レベルに、予測を裏切らないパフォーマンス レベルが組み合わさることで、アプリケーションにおける結果の一貫性が確実に保たれます。   

##一貫性レベル
データベース アカウントには既定の一貫性レベルを構成できます。既定の一貫性レベルは、対象となるデータベース アカウント下のすべての (全データベースの) コレクションに適用されます。既定では、ユーザー定義リソースに対して発行されたすべての読み取りとクエリに、データベース アカウントに指定された既定の一貫性レベルが使用されます。ただし、[x-ms-consistency-level] という要求ヘッダーを指定すれば、特定の読み取り/クエリ要求の一貫性レベルを引き下げることもできます。DocumentDB のレプリケーション プロトコルには 4 種類の一貫性レベルが存在します。以下、それぞれのレベルについて簡単に説明します。 

>[AZURE.NOTE] 今後のリリースでは、既定の一貫性レベルをコレクションごとに上書きできるようにする予定です。  

**Strong**: レプリカのマジョリティ クォーラムによってコミットされた後でのみ、書き込み結果が見える状態になることが保証されます。書き込みは、プライマリとセカンダリのクォーラムとの両方によって同期的にかつ永続的にコミットされるか、または中止されるか、のどちらかとなります。読み取りは常に、マジョリティ リード クォーラムによって確認応答されます。未コミットの書き込みや部分的な書き込みがクライアントから見えることはありません。読み取りの内容は常に、確認応答が返された最新の書き込み結果であることが保証されます。   
 
強い一貫性では、データの一貫性に関して揺るぎない保証が得られますが、読み取りと書き込みのパフォーマンス レベルは最も低くなります。  

**Bounded staleness**: 書き込みが伝播するトータルな順序は保証されますが、読み取りに関しては、書き込みよりも最大で K プレフィックス分の遅れが生じる可能性があります。読み取りに関しては常に、レプリカのマジョリティ クォーラムから確認応答が返されます。読み取り要求の応答に、その相対的な鮮度が (K の観点で) 記述されます。  

Bounded staleness は、読み取りの一貫性に関して、より予測可能な動作となり、書き込みの待機時間は最も小さくなります。読み取りは、マジョリティ クォーラムからの確認応答を伴うため、読み取りの待機時間はシステムで最短とはなりません。    

**Session**: Strong や Bounded Staleness の一貫性レベルで得られるグローバルな一貫性モデルとは異なり、"Session" の一貫性は、特定のクライアント セッションを意図して適用されます。モノトニックな読み取りと書き込み、さらに、自己の書き込みの読み取り可能性が保証されているため、通常の要件であれば Session の一貫性で満たすことができます。Session の一貫性を想定した読み取り要求は、クライアントから要求されたバージョン (セッション Cookie に含まれている) を返すことができるレプリカに対して発行されます。  

Session の一貫性により、セッションの期間中における読み取りデータの一貫性に関して予測可能性が確保され、書き込みの待機時間は最も短くなります。まれなケースを除いて、読み取りの待機時間も短くなります。読み取る内容は、単一のレプリカから返されます。  

**Eventual**: 最も弱い形態の一貫性であり、クライアントは、過去に見た値よりも古い値をいずれ取得する可能性があります。長期間書き込みがなければ、グループ内のレプリカはやがて収束します。読み取り要求は、任意のセカンダリによって処理されます。  

Eventual の一貫性は、読み取りの一貫性という点は最も弱いですが、読み取りと書き込みの待機時間は最短となります。 

##クエリの一貫性
既定では、ユーザー定義リソースに関するクエリの一貫性レベルは、読み取りの場合と同じです。既定では、コレクションに対するドキュメントの挿入、置換、削除のたびに、インデックスが同期的に更新されます。これにより、クエリには、ドキュメントの読み取りと同じ一貫性レベルが保証されます。DocumentDB は、書き込みを最適化し、同期インデックス メンテナンスによってドキュメントの持続的書き込みをサポートすると共に、クエリの一貫性を確保していますが、特定のコレクションのインデックスが遅れて更新されるように構成することもできます。インデックスの遅延作成は、書き込みのパフォーマンスを飛躍的に高める効果があり、ワークロードで主に読み取りの負荷が激しい状況での一括インジェストに最適です。  

インデックス作成モード|	読み取り|	クエリ  
-------------|-------|---------
一貫性 (既定)|	Strong、Bounded staleness、Session、Eventual の中から選択|	Select from Strong、Bounded staleness、Session、Eventual の中から選択|
遅延|	Strong、Bounded staleness、Session、Eventual の中から選択|	Eventual  

読み取り要求と同様、[x-ms-consistency-level] という要求ヘッダーを指定することによって、特定のクエリ要求の一貫性レベルを引き下げることができます。  

##参照
-	Doug Terry 著: Replicated Data Consistency Explained Through Baseball (レプリケート データの一貫性を野球にたとえると)   
[http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry 著: Session Guarantees for Weakly Consistent Replicated Data (弱一貫性レプリケート データのためのセッション保証)   
[http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi 著: Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story (先進的な分散データベース システム設計における一貫性のトレードオフ: CAP 以外の考慮事項について)   
[http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html) 
-	Peter Bailis、Shivaram Venkataraman、Michael J. Franklin、Joseph M. Hellerstein、Ion Stoica 著: Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums (現実的なパーシャル クォーラムのための Probabilistic Bounded Staleness (PBS))   
[http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels 著: Eventual Consistent - Revisited (結果的な一貫性 - 改訂版)    
[http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)

<!--HONumber=47-->
