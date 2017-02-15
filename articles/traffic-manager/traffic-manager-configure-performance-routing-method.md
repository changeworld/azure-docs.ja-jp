---
title: "パフォーマンスによるトラフィック ルーティング方法の構成 | Microsoft Docs"
description: "この記事では、Traffic Manager でパフォーマンスによるトラフィック ルーティング方法を構成する方法について説明します。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 6dd23b8e-0ed5-4ea4-b5ae-018f42e72688
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51a3f970059f4b83240cb61411dbf612209d9293


---
<!-- repub for nofollow -->

# <a name="configure-performance-traffic-routing-method"></a>パフォーマンスによるトラフィック ルーティング方法の構成
世界各地 (「リージョン」と呼びます) に配置されたさまざまなデータセンターにあるクラウド サービスと Web サイト (エンドポイント) に対するトラフィック ルーティングを行うために、要求元のクライアントから最も待機時間の短いエンドポイントに受信トラフィックを転送することができます。 通常、待機時間が最も低いデータセンターは、地理的に最も近いエンドポイントと一致します。 パフォーマンスによるトラフィック ルーティング方法を選択すると、トラフィックを最も短い待機時間に基づいて振り分けることができますが、振り分け時にネットワークの構成や負荷のリアルタイムの変化を考慮することはできません。 Azure Traffic Manager に用意されているさまざまなトラフィック ルーティング方法の詳細については、 [Traffic Manager でのトラフィック ルーティング方法について](traffic-manager-routing-methods.md)の記事を参照してください。

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>一連のエンドポイント間で、最も短い待機時間に基づいてトラフィック ルーティングを行う
1. Azure クラシック ポータルの左側のウィンドウで、 **[Traffic Manager]** アイコンをクリックして [Traffic Manager] ウィンドウを開きます。 まだ Traffic Manager プロファイルを作成していない場合は、「 [Traffic Manager プロファイルの管理](traffic-manager-manage-profiles.md) 」に記載された手順に従って、基本的な Traffic Manager プロファイルを作成してください。
2. Azure クラシック ポータルの Traffic Manager ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、そのプロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
3. プロファイルのページで、ページの上部にある **[エンドポイント]** をクリックし、構成に含めるサービス エンドポイントが存在することを確認します。 プロファイルにエンドポイントを追加または削除する手順については、「 [Traffic Manager でのエンドポイントの管理](traffic-manager-endpoints.md)」を参照してください。
4. プロファイルのページで、上部にある **[構成]** をクリックし、構成ページを開きます。
5. **トラフィック ルーティング方法の設定**で、トラフィック ルーティング方法が **[パフォーマンス]** に設定されていることを確認します。他の方法に設定されている場合は、ドロップダウン リストから **[パフォーマンス]** をクリックします。
6. **[監視の設定]** が適切に構成されていることを確認します。 監視を構成することで、オフラインになっているエンドポイントにトラフィックが送信されなくなります。 エンドポイントを監視するには、パスとファイル名を指定する必要があります。 スラッシュ "/" は、相対パスの有効な入力値で、ファイルがルート ディレクトリ (既定のディレクトリ) にあることを意味します。 監視の詳細については、「 [Traffic Manager の監視について](traffic-manager-monitoring.md)」を参照してください。
7. 構成の変更が完了したら、ページの下部にある **[保存]** をクリックします。
8. 構成の変更をテストします。 詳細については、「 [Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)」を参照してください。
9. Traffic Manager プロファイルが設定されて機能したら、権限のある DNS サーバー上の DNS レコードを編集して、会社のドメイン名が Traffic Manager ドメイン名を参照するようにします。 これを行う方法の詳細については、「 [会社のインターネット ドメインで Traffic Manager ドメインが参照されるようにする](traffic-manager-point-internet-domain.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[会社のインターネット ドメインで Traffic Manager ドメインが参照されるようにする](traffic-manager-point-internet-domain.md)

[Traffic Manager のルーティング方法](traffic-manager-routing-methods.md)

[フェールオーバーのルーティング方法の構成](traffic-manager-configure-failover-routing-method.md)

[ラウンド ロビンによるトラフィック ルーティング方法の構成](traffic-manager-configure-round-robin-routing-method.md)

[Traffic Manager の機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)

[Traffic Manager  - プロファイルの無効化、有効化、または削除](disable-enable-or-delete-a-profile.md)

[Traffic Manager - エンドポイントの無効化または有効化](disable-or-enable-an-endpoint.md)




<!--HONumber=Nov16_HO3-->


