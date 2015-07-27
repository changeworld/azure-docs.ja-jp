<properties pageTitle="不規則なサインイン アクティビティ" description="Microsoft の機械学習アルゴリズムで異常と識別されたサインインを含むレポート。"" services="active-directory" documentationCenter="" authors="kenhoff" manager="ilanas" editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="kenhoff"/>

# 不規則なサインイン アクティビティ

| 説明 | レポートの場所 |
| :-------------     | :-------        |
| <p>このレポートには、機械学習アルゴリズムによって、「異常」と識別されたサインインが含まれます。異常としてサインイン試行をマーキングする理由には、予期しないサインイン、時間と場所、またはこれらの組み合わせが含まれます。これは、ハッカーがこのアカウントを使用してサインインを試行したことを示している可能性があります。機械学習アルゴリズムは、「異常」または「不審」として、イベント分類します。「不審」は、セキュリティ侵害の可能性が比較的高いことを示します。</p><p>このレポートの結果には、これらのサインインと、各サインインに関連付けられた分類、場所、およびタイムスタンプが表示されます。</p><p>30 日以内に、10 以上の異常なサインイン イベントが検出された場合は、グローバル管理者に電子メール通知が送信されます。aad-alerts-noreply@mail.windowsazure.com 安全な差出人は、セーフ リストに登録してください。</p> | [ディレクトリ] > [レポート] タブ |

<!---HONumber=July15_HO3-->