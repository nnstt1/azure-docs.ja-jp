---
title: PowerShell を使用して Azure DevTest Labs に VHD ファイルをアップロードする
description: この記事では、PowerShell を使用して Azure DevTest Labs に VHD ファイルをアップロードする方法を示すチュートリアルを提供します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: d2ee739dccc60321a814862fabe1b44aac7192dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566352"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>PowerShell を使用してラボのストレージ アカウントに VHD ファイルをアップロードします

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs では、VHD ファイルを使用してカスタム イメージを作成でき、そのイメージを使用して仮想マシンをプロビジョニングできます。 次の手順は、PowerShell を使用して、VHD ファイルをラボのストレージ アカウントにアップロードする方法を示しています。 VHD ファイルをアップロードしたら、[「次のステップ」セクション](#next-steps)の記事に従って、アップロードした VHD ファイルからカスタム イメージを作成できます。 Azure のディスクと VHD の詳細については、「[マネージド ディスクの概要](../virtual-machines/managed-disks-overview.md)」を参照してください。

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順は、PowerShell を使用して VHD ファイルを Azure DevTest Labs にアップロードする方法を示しています。 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインする

1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. ラボのブレードで、 **[構成]** を選択します。 

1. ラボの **[構成]** ブレードで、 **[カスタム イメージ (VHD)]** を選択します。

1. **[カスタム イメージ]** ブレードで、 **[+追加]** を選択します。 

1. **[カスタム イメージ]** ブレードで、 **[VHD]** を選択します。

1. **[VHD]** ブレードで、 **[Upload a VHD file using PowerShell (PowerShell を使用して VHD ファイルをアップロードする)]** を選択します。

    ![PowerShell を使用した VHD のアップロード](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. **[PowerShell を使用したイメージのアップロード]** ブレードで、生成された PowerShell スクリプトをテキスト エディターにコピーします。

1. **Add-AzureVhd** コマンドレットの **LocalFilePath** パラメーターを、アップロードする VHD ファイルの場所を指すように変更します。

1. PowerShell プロンプトで、**Add-AzureVhd** コマンドレットを実行します (変更した **LocalFilePath** パラメーターを使用します)。

> [!WARNING] 
> 
> VHD ファイルのアップロードは、VHD ファイルのサイズと接続速度によっては、時間がかかる場合があります。

## <a name="next-steps"></a>次のステップ

- [Azure Portal を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-template.md)
- [PowerShell を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
