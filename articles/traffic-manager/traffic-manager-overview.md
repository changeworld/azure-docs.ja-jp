<properties 
   pageTitle="Traffic Manager について | Microsoft Azure"
   description="この記事は、Traffic Manager の内容と、これがアプリケーションに適したトラフィック ルーティングかどうかを理解するのに役立ちます"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="sewhee" />

# Traffic Manager について

Microsoft Azure Traffic Manager では、世界各地のさまざまなデータセンターで実行されている、サービス エンドポイントへのユーザー トラフィックの分散を制御できます。

Traffic Manager でサポートされるサービス エンドポイントには、Azure VM、Web Apps、およびクラウド サービスが含まれます。Azure 以外の外部エンドポイントで Traffic Manager を使用することもできます。

Traffic Manager を動作させるには、構成済みのトラフィック ルーティング方法、およびエンドポイント正常性の現在のビューに基づいて、エンド ユーザー要求を、ドメイン ネーム システム (DNS) を使用して最適なエンドポイントに送信します。その後、適切なサービス エンドポイントに、クライアントが直接接続します。

Traffic Manager では、さまざまなアプリケーションのニーズに対応するために、[トラフィック ルーティング方法の範囲](traffic-manager-routing-methods.md)がサポートされます。Traffic Manager には、[エンドポイントの正常性チェックと自動フェールオーバー](traffic-manager-monitoring.md)が用意されており、Azure リージョン全体の障害などの障害からの回復性に優れた、高可用性アプリケーションを構築できます。

## Traffic Manager の利点

Traffic Manager を使用すると、次のようなメリットが得られます。

- **重要なアプリケーションの可用性の向上** - Traffic Manager を使用すると、Azure のエンドポイントを監視し、エンドポイントがダウンしたときに自動フェールオーバー機能を提供することにより、重要なアプリケーションに高可用性を提供できます。
- **高パフォーマンスのアプリケーションのために応答性を向上** - Azure によって、世界各地に配置されたデータセンターでクラウド サービスまたは Web サイトを実行できます。Traffic Manager は、クライアントのネットワーク待ち時間が最も短いエンドポイントにエンド ユーザーを転送することで、アプリケーションの応答性を高めます。
- **ダウンタイムなしのアップグレードおよびサービス メンテナンスの実施** - メンテナンスが行われている間、Traffic Manager を使用して、トラフィックを代替エンドポイントに直接送信することで、エンド ユーザーに対するダウンタイムを発生させずに、アプリケーションのアップグレードとその他の定期的なメンテナンスをシームレスに実施できます。
- **オンプレミス アプリケーションとクラウド ベースのアプリケーションの結合** - Traffic Manager では、Azure 以外の外部エンドポイントがサポートされています。これにより、Traffic Manager は、"クラウドへのバースト"、"クラウドへの移行"、"クラウドへのフェールオーバー" など、ハイブリッド クラウドとオンプレミス デプロイメントで使用することができます。
- **大規模で複雑なデプロイに対するトラフィックの分散** - [入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)を使ってトラフィック ルーティング方法を組み合わせることで、高度で柔軟性のあるトラフィック ルーティング構成を作成し、より大規模で複雑なデプロイメントのニーズに対応できます。

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## 次のステップ

- [Traffic Manager のしくみ](traffic-manager-how-traffic-manager-works.md)の詳細を確認します。

- [Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md)機能を使用して、高可用性アプリケーションを開発する方法を確認します。

- Traffic Manager でサポートされている[トラフィック ルーティング方法](traffic-manager-routing-methods.md)の詳細を確認します。

- [Traffic Manager プロファイルを作成します](traffic-manager-manage-profiles.md)。
 

<!---HONumber=AcomDC_0824_2016-->