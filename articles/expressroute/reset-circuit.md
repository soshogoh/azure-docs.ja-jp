---
title: '障害が発生した回線をリセットする - ExpressRoute: PowerShell:Azure | Microsoft Docs'
description: この記事は、障害状態になっている ExpressRoute 回線をリセットする際に役立ちます。
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: e94ba2f7f6981355f4736683b891fbdced001d03
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425341"
---
# <a name="reset-a-failed-expressroute-circuit"></a>障害が発生した ExpressRoute 回線のリセット

ExpressRoute 回線での操作が正常に完了しないと、回線が "障害" 状態になることがあります。 この記事は、障害状態の ExpressRoute 回線をリセットする際に役立ちます。

## <a name="reset-a-circuit"></a>回線のリセット

1. Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 詳細については、[Azure PowerShell のインストールおよび構成](/powershell/azure/azurerm/install-azurerm-ps)をご覧ください。

2. 昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```
3. 複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
4. 使用するサブスクリプションを指定します。

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. 次のコマンドを実行して、障害状態の回線をリセットします。

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

回線はすぐに正常になります。 回線が障害状態のままである場合は、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に対してサポート チケットを申請します。

## <a name="next-steps"></a>次の手順

問題が解消しない場合は [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) にサポート チケットを申請する
