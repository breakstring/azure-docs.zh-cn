---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018339"
---
请注意“性能”选项卡左窗格上的“概率阈值”滑块   。这是预测被视为正确时所需具有的置信度（用于计算精度和召回率）。 

当使用较高的概率阈值来解释预测调用时，它们往往会以高精度返回结果，但以召回率为代价 &mdash; 检测到的分类是正确的，但许多仍未被检测到。 使用较低的概率阈值则恰恰相反 &mdash; 实际分类会被检测到，但该集合内有更多误报。 考虑到这一点，应该根据项目的特定需求设置概率阈值。 稍后，在客户端接收预测结果时，应使用与此处所用概率阈值相同的概率阈值。