<properties
   pageTitle="Power BI Embedded の新機能"
   description="Power BI Embedded の新機能に関する最新情報を入手できます。"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/06/2016"
   ms.author="owend"/>

# Power BI Embedded の新機能

**Power BI Embedded** の更新は定期的にリリースされます。ただし、すべてのリリースにユーザー向けの新機能が含まれているわけではありません。一部のリリースでは、バックエンド サービス機能に重点を置いています。ここでは、ユーザー向けの新機能について説明します。必ず頻繁に確認してください。

## 2016 年 8 月 31 日

このリリースの新機能は次のとおりです。

- すべての新しい JavaScript SDK で[高度なフィルタリングと ページ ナビゲーション](power-bi-embedded-interact-with-reports.md)がサポートされます。
- Power BI Embedded がカナダ中部データセンターでサポートされるようになりました。[データセンターのステータス](https://azure.microsoft.com/status/)に関するページをご覧ください。

## 2016 年 7 月 11 日

このリリースの新機能は次のとおりです。

-    **お知らせ:** Power BI Embedded サービスがプレビューではなくなり、一般公開 (GA) されました。
-    すべての REST API が **/beta** から **/v1.0** に移行されました。
-    .NET および JavaScript SDK が **v1.0** 用に更新されました。
-    API キーを使用して、Power BI API 呼び出しを直接認証できるようになりました。埋め込みに必要なのはアプリ トークンだけです。この一環として、プロビジョニング用トークンと開発用トークンは、v1.0 API では使用されなくなりましたが、2016 年 12 月 30 日までベータ版で引き続き機能します。詳細については、「[Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)」をご覧ください。
-    行レベルのセキュリティ (RLS) でアプリ トークンと埋め込みレポートがサポートされます。詳細については、「[Power BI Embedded の行レベルのセキュリティ](power-bi-embedded-rls.md)」をご覧ください。
-    サンプル アプリケーションが更新され、すべての **v1.0** API 呼び出しに対応しました。
-    Power BI Embedded で Azure SDK、PowerShell、CLI がサポートされます。
-    視覚化データを **.csv** にエクスポートできるようになりました。
-    Microsoft Azure と同じすべての言語/ロケールで Power BI Embedded がサポートされるようになりました。詳細については、[Azure の言語](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)に関するページをご覧ください。

<!---HONumber=AcomDC_0831_2016-->