---
title: Azure Kinect 人体跟踪关节
description: 了解 Azure Kinect DK 中的人体帧、关节、关节坐标和关节层次结构。
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 移植, 人体, 跟踪, 关节, 层次结构, 骨骼, 连接
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "74978314"
---
# <a name="azure-kinect-body-tracking-joints"></a>Azure Kinect 人体跟踪关节

Azure Kinect 人体跟踪可以同时跟踪多个人体。 每个人体包括一个 ID，用于在帧与运动骨架之间进行临时关联。 可以使用 `k4abt_frame_get_num_bodies()` 获取在每个帧中检测到的人体数。

## <a name="joints"></a>关节

关节位置和方向是相对于全局深度传感器参考帧的估算值。 位置以毫米为单位指定。 方向以规范化四元数表示。

## <a name="joint-coordinates"></a>关节坐标

每个关节的位置和方向构成了其自身的关节坐标系。 所有关节坐标系是相对于深度摄像头 3D 坐标系的绝对坐标系。

> [!NOTE]
> 关节坐标是轴方向上的坐标。 轴方向广泛用于商业头像、游戏引擎和渲染软件。 使用轴方向可以简化镜像运动，例如将双臂抬起 20 度。

![关节坐标](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>关节层次结构

骨架包括 32 个关节，关节层次结构按照从人体中心向四肢的流向分布。 每个连接（骨骼）将父关节与子关节链接起来。 下图演示了人体上的关节位置和连接方式。

![关节层次结构](./media/concepts/joint-hierarchy.png)

下表列举了标准的关节连接。

|索引 |关节名称     | 父关节   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |NECK           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | NECK           |
| 27   |NOSE           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>后续步骤

[人体跟踪索引图](body-index-map.md)
