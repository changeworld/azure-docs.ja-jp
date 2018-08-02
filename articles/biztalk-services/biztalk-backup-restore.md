---
title: BizTalk Services でのバックアップの作成と復元 | Microsoft Docs
description: BizTalk Services には、バックアップと復元の機能が備わっています。 バックアップを作成して復元する方法を説明すると共に、何がバックアップされるのかを確認します。 MABS、WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 90cf2d0ddbba47a856bf1299a101c5185873b5d8
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214414"
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk サービス: バックアップと復元に関するページ

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk サービスには、バックアップ機能と復元機能が備わっています。 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> ハイブリッド接続は、エディションにかかわらずバックアップされません。 ハイブリッド接続を再作成する必要があります。


## <a name="before-you-begin"></a>開始する前に
* バックアップと復元は、どのエディションでもできるとは限りません。 「 [BizTalk サービス: エディションのチャート](biztalk-editions-feature-chart.md)」を参照してください。
* バックアップ コンテンツを同じ BizTalk サービスに復元することも、新しい BizTalk サービスに復元することもできます。 同じ名前を使用して BizTalk サービスを復元するには、既存の BizTalk サービスを削除する必要があります。さらにその名前が使用可能である必要があります。 BizTalk サービスを削除した後、同じ名前が使用可能になるまで長い時間がかかる場合があります。 同じ名前が使用可能になるまで待つことができない場合は、新しい BizTalk サービスに復元します。
* BizTalk サービスの復元先は、同じエディション以上となります。 バックアップを実行したときよりも下位のエディションへの BizTalk サービスの復元はサポートされていません。
  
    たとえば、Basic Edition を使用したバックアップは Premium Edition に復元できます。 Premium Edition を使用したバックアップを Standard Edition に復元することはできません。
* コントロール番号の連続性を維持するために、EDI コントロール番号がバックアップされます。 前回のバックアップ後にメッセージが処理された場合、このバックアップ コンテンツを復元するとコントロール番号が重複する可能性があります。
* バッチの中にアクティブなメッセージがある場合は、バックアップを実行する **前に** そのバッチを処理してください。 バックアップを作成するときに (オンデマンドかスケジュールされたものかにかかわらず)、バッチの中のメッセージが保存されることはありません。 
  
    **アクティブなメッセージがバッチになっている状態で、それらに対応するバックアップを取得しようとしても、それらのメッセージはバックアップされないため、失われます。**
* オプション: BizTalk サービス ポータルで、管理操作をすべて停止します。

## <a name="create-a-backup"></a>バックアップの作成
バックアップはいつでも取得でき、完全にユーザーによって制御されます。 バックアップを作成するには、[Azure で BizTalk Services を管理するための REST API](https://msdn.microsoft.com/library/azure/dn232347.aspx) を使用します。

## <a name="restore"></a>Restore
バックアップを復元するには、[Azure で BizTalk Services を管理するための REST API](https://msdn.microsoft.com/library/azure/dn232347.aspx) を使用します。

### <a name="postrestore"></a>バックアップの復元後
BizTalk サービスは常に、**[中断]** 状態で復元されます。 この状態のときは、新しい環境を機能させる前に次のような構成変更を行うことができます。

* Azure BizTalk Services SDK を使用して BizTalk サービス アプリケーションを作成した場合は、そのアプリケーション内の Access Control (ACS) 資格情報が復元後の環境で動作するように更新することが必要になる可能性があります。
* 既存の BizTalk サービス環境の複製を目的として BizTalk サービスを復元します。 これに該当するときに、元の BizTalk Services ポータルでソース FTP フォルダーを使用するように契約が構成されている場合は、新たに復元された環境で別のソース FTP フォルダーを使用するように契約を更新することが必要になる可能性があります。 更新しなかった場合は、2 つの異なる契約が同じメッセージを取り出そうとする可能性があります。
* 復元操作の結果として複数の BizTalk サービス環境が存在することになった場合は、Visual Studio アプリケーション、PowerShell コマンドレット、REST API、取引先管理オブジェクト モデル (TPM OM) API でのターゲットとして必ず正しい環境を指定してください。
* 新しく復元した BizTalk サービス環境で自動バックアップを構成することをお勧めします。

## <a name="what-gets-backed-up"></a>バックアップ対象
バックアップが作成されるとき、次の項目がバックアップされます。

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>バックアップされる項目</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk Services ポータル</strong></td>
</tr> 
<tr>
<td>構成とランタイム</td> 
<td>
<ul>
<li>パートナーとプロファイルの詳細</li>
<li>パートナー契約</li>
<li>デプロイされているカスタム アセンブリ</li>
<li>デプロイされているブリッジ</li>
<li>証明書</li>
<li>デプロイされている変換</li>
<li>パイプライン</li>
<li>BizTalk Services ポータルで作成および保存されたテンプレート</li>
<li>X12 ST01 および GS01 マッピング</li>
<li>コントロール番号 (EDI)</li>
<li>AS2 メッセージ MIC 値</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Azure BizTalk Services</strong></td>
</tr> 
<tr>
<td>SSL 証明書</td> 
<td>
<ul>
<li>SSL 証明書データ</li>
<li>SSL 証明書のパスワード</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk サービスの設定</td> 
<td>
<ul>
<li>スケール ユニット数</li>
<li>エディション</li>
<li>製品バージョン</li>
<li>リージョン/データセンター</li>
<li>Access Control Service (ACS) の名前空間とキー</li>
<li>トラッキング データベースの接続文字列</li>
<li>アーカイブ ストレージ アカウントの接続文字列</li>
<li>監視ストレージ アカウントの接続文字列</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>その他の項目</strong></td>
</tr> 
<tr>
<td>トラッキング データベース</td> 
<td>BizTalk サービスを作成するときに、トラッキング データベースの詳細 (Azure SQL Database サーバーやトラッキング データベース名など) が入力されます。 トラッキング データベースは自動的にはバックアップされません。
<br/><br/>
<strong>重要</strong><br/>
トラッキング データベースが削除されてデータベースの復旧が必要である場合は、以前のバックアップが存在している必要があります。 バックアップが存在しない場合、トラッキング データベースとそのデータは復旧できません。 このような状況では、同じデータベース名の新しいトラッキング データベースを作成します。 geo レプリケーションが推奨されます。</td>
</tr> 
</table>

## <a name="next"></a>次へ
Azure BizTalk Services を作成するには、[BizTalk Services: プロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)に関するページに進みます。 アプリケーションの作成を開始するには、 [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197)に関するページを参照してください。

## <a name="see-also"></a>関連項目
* [BizTalk サービスのバックアップ](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [バックアップからの BizTalk サービスの復元](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: プロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: サービスの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk サービス: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk サービス: 発行者名および発行者キー](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Visual Studio でのプロジェクトの作成](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

