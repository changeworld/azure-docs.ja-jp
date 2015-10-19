<properties 
	pageTitle="Azure Redis Cache Premium レベルの概要" 
	description="Premium レベルの Azure Redis Cache インスタンス向けの Redis の永続化、Redis クラスタリング、および VNET サポートの作成と管理方法について説明します。" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/02/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache Premium レベルの概要
Azure Redis Cache は、データへの超高速アクセスを提供することで、拡張性や応答性の高いアプリケーションの作成を可能にするマネージ キャッシュです。

新しい Premium レベルは、Standard 階層の全機能に加えて、パフォーマンスの向上、ワークロードの増加、障害復旧、セキュリティの強化などを備えたエンタープライズ対応のレベルです。Premium キャッシュ レベルの追加機能の詳細をお知りになりたい場合は、このドキュメントを引き続きお読みください。

## Standard/Basic レベルを超えるパフォーマンス
**Standard/Basic レベルを超えるパフォーマンス。** Premium レベルのキャッシュは、高速プロセッサを備え、Basic/Standard レベルと比較して優れたパフォーマンスを発揮するハードウェア上にデプロイされます。Premium レベルのキャッシュは、スループットが高く、待機時間が低くなっています。

**同じサイズのキャッシュでも Standard レベルと比べて Premium のスループットの方が高い。** たとえば、53 GB のキャッシュでは、C6 (Standard) のスループットが毎秒 15 万要求であるのに対し、P4 (Premium) は毎秒 25 万要求です。

Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[Azure Redis Cache の FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)」を参照してください。

## Redis データの保持
Premium レベルでは、Azure ストレージ アカウント内のキャッシュ データを永続化できます。Basic/Standard のキャッシュでは、データはすべてメモリ内にのみ格納されます。基盤となるインフラストラクチャに問題が発生した場合、データが失われる可能性があります。Premium レベルの Resis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。Azure Redis Cache には、[Redis の永続化](http://redis.io/topics/persistence)の RDB オプションと AOF オプション (近日公開予定) があります。

永続化の構成手順については、「[Premium Azure Redis Cache の永続性の構成方法](cache-how-to-premium-persistence.md)」を参照してください。

##Redis クラスター
53 GB を超えるキャッシュを作成するか、複数の Redis ノード間でデータを共有する場合、Premium レベルで利用可能な Redis クラスタリングを使用することができます。各ノードは、Azure によって管理される高可用性対応のプライマリ/レプリカ キャッシュのペアで構成されています。

**Redis クラスタリングでは、スケールとスループットを最大限に利用できます。** クラスター内のシャード (ノード) の数を増やすと、スループットは直線的に増加します。たとえば、10 個のシャードから成る P4 クラスターを作成すると、利用可能なスループットは毎秒 250 万要求 (25 万 * 10) となります。Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[Azure Redis Cache の FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)」を参照してください。

クラスタリングを開始する場合は、「[Premium Azure Redis Cache のクラスタリングの構成方法](cache-how-to-premium-clustering.md)」を参照してください。

##強化されたセキュリティと分離
Basic/Standard レベルで作成されたキャッシュは、パブリック インターネット上でアクセス可能です。キャッシュへのアクセスは、アクセス キーに基づいて制限されます。Premium レベルでは、さらに、指定されたネットワーク内のクライアントだけがキャッシュにアクセスできるようにすることが可能になります。Redis Cache は、[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) 内にデプロイできます。サブネット、アクセス制御ポリシー、およびその他の Redis へのアクセスをさらに制限する機能を始め、VNet のすべての機能を使用できます。

詳細については、「[Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)」を参照してください。

## 次のステップ

キャッシュを作成し、Premium レベルの新機能を確認します。

-	[How to configure persistence for a Premium Azure Redis Cache (Premium Azure Redis Cache の永続性の構成方法)](cache-how-to-premium-persistence.md)
-	[Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)
-	[How to configure clustering for a Premium Azure Redis Cache (Premium Azure Redis Cache のクラスタリングの構成方法)](cache-how-to-premium-clustering.md)
  

<!---HONumber=Oct15_HO2-->