---
title: PowerShell:Web サーバーのログを使用して監視する
description: Azure PowerShell を使用して App Service のデプロイと管理を自動化する方法について説明します。 このサンプルでは、Web サーバーのログを使用してアプリを監視する方法を紹介しています。
tags: azure-service-management
ms.assetid: 5805d7cd-9e56-4eba-bd85-75b013690ff5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: c9b3ab6f8115f68ead478c0de21e4661d3ca032c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437926"
---
# <a name="monitor-a-web-appwith-web-server-logs"></a>Web サーバー ログによる Web アプリの監視

このシナリオでは、リソース グループ、App Service プラン、Web アプリを作成し、Web アプリを構成して Web サーバー ログを有効にします。 次に、ログ ファイルをダウンロードして確認します。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzAccount` を実行して、Azure との接続を作成します。

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1 "Monitor a web app with web server logs")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service プランを作成します。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Web アプリを作成します。 |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Web アプリの構成を変更します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。
