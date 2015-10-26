<properties 
 pageTitle="Azure Scheduler のプランと課金" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="08/04/2015" 
 ms.author="krisragh"/>
 
# Azure Scheduler のプランと課金

## ジョブ コレクション プラン

ジョブ コレクションは、Azure Scheduler における課金対象のエンティティです。複数のジョブを格納するジョブ コレクションは、Free、Standard、および Premium の 3 つのプランで提供されます。以降では、これらのプランについて説明します。

|**ジョブ コレクション プラン**|**ジョブ コレクションあたりのジョブの最大数**|**最大繰り返し頻度**|**サブスクリプションあたりのジョブ コレクションの最大数**|**制限**|
|:---|:---|:---|:---|:---|
|**Free**|ジョブ コレクションあたり 5 個のジョブ|1 時間に 1 回。1 時間に 1 回を超えてジョブを実行できません。|1 つのサブスクリプションに最大で 1 個の Free ジョブ コレクションが許可されます。|[HTTP 送信承認オブジェクト](scheduler-outbound-authentication.md)を使用できません。
|**Standard**|ジョブ コレクションあたり 50 個のジョブ|1 分に 1 回。1 分に 1 回を超えてジョブを実行できません。|1 つのサブスクリプションに最大で 100 個の Standard ジョブ コレクションが許可されます。|Scheduler の完全な機能セットへのアクセス|
|**Premium**|ジョブ コレクションあたり 50 個のジョブ|1 分に 1 回。1 分に 1 回を超えてジョブを実行できません。|1 つのサブスクリプションに最大で 10,000 個の Premium ジョブ コレクションが許可されます。詳細については、<a href="mailto:wapteams@microsoft.com">お問い合わせ</a>ください。|Scheduler の完全な機能セットへのアクセス|

## ジョブ コレクション プランのアップグレードとダウングレード

ジョブ コレクション プランは、Free、Standard、および Premium の間でいつでもアップグレードまたはダウングレードできます。ただし、Free ジョブ コレクションにダウングレードする場合、次のいずれかの理由でダウングレードが失敗することがあります。

- サブスクリプションに既に Free ジョブ コレクションが存在している。
- ジョブ コレクション内のジョブに、Free ジョブ コレクションのジョブに許可されているよりも高い繰り返し頻度が設定されている。Free ジョブ コレクションで許可される最大繰り返し頻度は 1 時間に 1 回です。
- ジョブ コレクションに 5 つを超えるジョブが含まれている。
- ジョブ コレクション内のジョブに、[HTTP 送信承認オブジェクト](scheduler-outbound-authentication.md)を使用する HTTP アクションまたは HTTPS アクションが含まれている。

## 課金および Azure プラン

Free ジョブ コレクションについては、課金されません。100 を超える Standard ジョブ コレクション (Standard 課金単位 10 単位) を使用している場合は、Premium プランにすべてのジョブ コレクションを移行することをお勧めします。

1 つの Standard ジョブ コレクションと 1 つの Premium ジョブ コレクションを使用している場合、Standard プランの 1 課金単位_と_ Premium プランの 1 課金単位が請求されます。Scheduler サービスでは、Standard または Premium のどちらかに設定されているアクティブなジョブ コレクションの数に基づいて課金されます。これについては、次の 2 つのセクションで詳しく説明します。

## Standard 課金単位

Standard 課金単位には、最大 10 個の Standard ジョブ コレクションを含めることができます。Standard ジョブ コレクションでは、ジョブ コレクションあたり最大で 50 個のジョブを含めることができるため、Standard 課金単位 1 単位でサブスクリプションに最大 500 個のジョブを含めることができます (これは、1 か月あたり約 2,200 万件のジョブ実行に相当します)。

使用している Standard ジョブ コレクションが 1 ～ 10 個の場合、Standard 課金単位 1 単位分が課金されます。使用している Standard ジョブ コレクションが 11 ～ 20 個の場合、Standard 課金単位 2 単位分が課金されます。使用している Standard ジョブ コレクションが 21 ～ 30 個の場合、Standard 課金単位 3 単位分が課金されます。それ以上の数についても同様の計算になります。

## Premium 課金単位

Premium 課金単位には、最大 10,000 個の Premium ジョブ コレクションを含めることができます。Premium ジョブ コレクションでは、ジョブ コレクションあたり最大で 50 個のジョブを含めることができるため、Premium 課金単位 1 単位でサブスクリプションに最大 500,000 個のジョブを含めることができます (これは、1 か月あたり約 220 億件のジョブ実行に相当します)。

使用している Premium ジョブ コレクションが 1 ～ 10,000 個の場合、Premium 課金単位 1 単位分が課金されます。使用している Premium ジョブ コレクションが 10,001 ～ 20,000 個の場合、Premium 課金単位 2 単位分が課金されます。それ以上の数についても同様の計算になります。

このように、Premium ジョブ コレクションでは、Standard ジョブ コレクションと同じ機能を提供する一方で、大量のジョブ コレクションを必要とするアプリケーションのために割引価格を実現しています。

## 課金とアクティブ状態

ジョブ コレクションは、サブスクリプション全体が課金に関する問題のために一時的な無効状態にならない限り、常にアクティブです。ジョブ コレクションが課金されないようにするには、_Free_ プランに設定するか、またはジョブ コレクションを削除します。

1 回の操作でジョブ コレクション内のすべてのジョブを無効にすることができますが、その操作を行ってもジョブ コレクションの課金状態は変更されず、ジョブ コレクションに対して_依然として_課金されます。同様に、空のジョブ コレクションもアクティブと見なされ、課金の対象となります。

## 料金

料金の詳細については、「[Scheduler 料金](http://azure.microsoft.com/pricing/details/scheduler/)」を参照してください。

## 関連項目
 
 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)
 
 [Scheduler Concepts, Terminology, and Entity Hierarchy (Scheduler の概念、用語集、エンティティ階層構造)](scheduler-concepts-terms.md)

 [管理ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [How to Build Complex Schedules and Advanced Recurrence with Azure Scheduler (Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法)](scheduler-advanced-complexity.md)

 [Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)

 [Scheduler PowerShell Cmdlets Reference (Scheduler PowerShell コマンドレット リファレンス)](scheduler-powershell-reference.md)
 
 [Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Scheduler の制限、規定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Scheduler 送信認証](scheduler-outbound-authentication.md)
  

<!---HONumber=Oct15_HO3-->