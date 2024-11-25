---
title: Azure IoT Hub をアップグレードする | Microsoft Docs
description: IoT Hub の価格とスケール レベルを変更して、メッセージング機能とデバイス管理機能を強化します。
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: lizross
ms.openlocfilehash: b6604dd246e4a98e58251a3de1c05365e405cfc2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551503"
---
# <a name="how-to-upgrade-your-iot-hub"></a>IoT Hub のアップグレード方法

IoT ソリューションの成長と共に、Azure IoT Hub をスケールアップできます。 Azure IoT Hub は、使用する機能が異なる顧客に対応するために、基本 (B) と標準 (S) という 2 つのレベルで提供されています。 各レベルには 3 つのサイズ (1、2、および 3) があり、サイズによって 1 日に送信できるメッセージの数が決まります。

デバイスの数が増え、さらに機能が必要になったら、次の 3 つの方法で、ニーズに合うように IoT Hub を調整できます。

* IoT Hub 内のユニットを追加する。 たとえば、B1 レベルの IoT Hub に 1 ユニットを追加すると、1 日あたり 400,000 のメッセージが追加されます。

* IoT Hub のサイズを変更する。 たとえば、B1 レベルから B2 レベルに移行すると、各ユニットがサポートできる 1 日あたりのメッセージ数が増加します。

* 上位レベルにアップグレードする。 たとえば、同じメッセージング機能を利用して高度な機能にアクセスできるように、B1 レベルから S1 レベルへアップグレードします。

これらの変更は、すべてが既存の操作を中断せずに実行できます。

お使いの IoT ハブをダウングレードする場合は、ユニットを削除して、IoT ハブ のサイズを縮小できます。ただし、下位レベルにダウングレードすることはできません。 たとえば、S2 レベルから S1 レベルに移動できますが、S2 レベルから B1 レベルに移動することはできません。 1 つのレベル内の [Iot Hub エディション](https://azure.microsoft.com/pricing/details/iot-hub/)は、IoT Hub あたり 1 種類だけ選択できます。 たとえば、S1 と B3 や S1 と S2 などの異なるエディションのユニットの組み合わせではなく、S1 のユニットを複数持つ IoT Hub を作成できます。

これらの例は、ソリューションが変化したときに IoT Hub をどのように調整するかを理解するためものです。 各レベルの機能に関する具体的な情報については、「[IoT Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub/)」を常に確認するようにしてください。

## <a name="upgrade-your-existing-iot-hub"></a>既存の IoT Hub をアップグレードする

1. [Azure Portal](https://portal.azure.com/) にサインインし、IoT Hub に移動します。

2. 左側の ナビゲーション ウィンドウから **[価格とスケール]** を選択します。

3. Hub のレベルを変更するには、**[価格とスケール レベル]** を選択します。 新しいレベルを選択し、**[選択]** をクリックします。

4. Hub のユニット数を変更するには、**[IoT Hub ユニット]** の下に新しい値を入力します。

5. **[保存]** を選択して変更を保存します。

IoT Hub が調整されますが、構成は変更されません。

Basic レベルの IoT Hub と Standard レベルの IoT Hub のパーティションの上限は 32 です。 ほとんどの IoT Hub では、4 つのパーティションのみ必要です。 IoT Hub の作成時にパーティション制限が選択され、device-to-cloud メッセージがこれらのメッセージの同時閲覧者数に関連付けられます。 Basic レベルから Standard レベルに移行してもこの値は変わりません。

## <a name="next-steps"></a>次の手順

[適切な IoT Hub のレベルを選択する方法](iot-hub-scaling.md)に関する記事で詳細を確認します。