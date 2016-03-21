<properties
   pageTitle="Traffic Manager プロファイルの無効化、有効化、または削除 | Microsoft Azure"
   description="この記事では、Traffic Manager プロファイルを操作する方法について説明します。"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="joaoma" />

# プロファイルの無効化、有効化、または削除


構成されたエンドポイントに対するユーザーの要求を参照しないように、既存の Traffic Manager プロファイルを無効にすることができます。Traffic Manager プロファイルを無効にすると、プロファイル自体とプロファイルに含まれる情報は元のまま残ります。このため、Traffic Manager インターフェイスで編集することもできます。プロファイルを再度有効にする場合は、Azure ポータルから設定するだけで、参照が再開されます。Traffic Manager プロファイルは、Azure ポータルで作成すると自動的に有効になります。

## プロファイルを無効にするには

1. インターネット DNS サーバー上の DNS リソース レコードを変更して、適切なレコードの種類と、別の名前またはインターネット上の特定の場所の IP アドレスを指すポインターを使用します。つまり、Traffic Manager プロファイルのドメイン名を指す CNAME リソース レコードを使用しなくなるように、インターネット DNS サーバー上の DNS リソース レコードを変更します。
1. Traffic Manager プロファイルをこのように設定すると、トラフィックがエンドポイントに送信されなくなります。
1. 無効にするプロファイルを選択します。プロファイルを選択するには、[Traffic Manager] ページのプロファイル名の横の列をクリックして、プロファイルを強調表示します。プロファイルの名前または名前の横の矢印をクリックしないでください。クリックすると、プロファイルの設定ページへ移動します。
1. プロファイルを選択した後に、ページ下部にある [無効化] をクリックします。

## プロファイルを有効にするには

1. 有効にするプロファイルを選択します。プロファイルを選択するには、[Traffic Manager] ページのプロファイル名の横の列をクリックして、プロファイルを強調表示します。プロファイルの名前または名前の横の矢印をクリックしないでください。クリックすると、プロファイルの設定ページへ移動します。
1. プロファイルを選択した後に、ページ下部にある [有効化] をクリックします。
1. CNAME レコードの種類を使用するようにインターネット DNS サーバー上の DNS リソース レコードを変更します。これによって、会社のドメイン名が Traffic Manager プロファイルのドメイン名に対応付けられます。詳細については、「[会社のインターネット ドメインで Traffic Manager ドメインが参照されるようにする](traffic-manager-point-internet-domain.md)」を参照してください。
1. トラフィックは再度、エンドポイントに送信されるようになります。

## プロファイルの削除


1. インターネット DNS サーバー上の DNS リソース レコードが、Traffic Manager プロファイルのドメイン名を指す CNAME リソース レコードを使用していないことを確認します。
1. 削除するプロファイルを選択します。プロファイルを選択するには、[Traffic Manager] ページのプロファイル名の横の列をクリックして、 
1. プロファイルを強調表示します。プロファイルの名前または名前の横の矢印をクリックしないでください。クリックすると、プロファイルの設定ページへ移動します。
1. プロファイルを選択した後に、ページ下部にある [削除] をクリックします。

## 次のステップ

[Traffic Manager - エンドポイントの無効化または有効化](disable-or-enable-an-endpoint.md)

[フェールオーバーのルーティング方法の構成](traffic-manager-configure-failover-routing-method.md)

[ラウンド ロビンによるトラフィック ルーティング方法の構成](traffic-manager-configure-round-robin-routing-method.md)

[パフォーマンスによるトラフィック ルーティング方法の構成](traffic-manager-configure-performance-routing-method.md)

[Traffic Manager の機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)

<!---HONumber=AcomDC_1210_2015-->