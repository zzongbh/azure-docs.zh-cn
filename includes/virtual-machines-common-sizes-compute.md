---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 9602e8d73e5aca650dd20da34a9aa675b508ada7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
<!-- F-series, Fs-series* -->

计算优化的 VM 大小具有较高的 CPU 内存比，适用于中等流量 Web 服务器、网络设备、批处理进程和应用程序服务器。 本文针对此分组中每种大小提供有关 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽的信息。

Fsv2 系列基于 Intel® Xeon® Platinum 8168 处理器，它具有 2.7 GHz 的基本核心频率和 3.7 GHz 的最大单核超频。 Intel 可扩展处理器上全新的 Intel® AVX-512 指令对于单精度和双精度浮点运算可为向量处理工作负荷提供高达 2 倍的性能提升。 换而言之，对于任何计算工作负荷，它们的处理速度相当快。 

凭借较低的每小时定价，Fsv2 系列在基于每个 vCPU 的 Azure 计算单位 (ACU) 的 Azure 产品组合中具有最高性价比。 

F 系列基于 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 处理器，该处理器使用 Intel Turbo Boost 技术 2.0，可实现高达 3.1 GHz 的时钟速度。 此 CPU 性能与 Dv2 系列的 VM 相同。  

对于需要更快的 CPU，但是每个 vCPU 不需要太多内存或临时存储的工作负荷来说，F 系列 VM 是一个极佳选择。  诸如分析、游戏服务器、Web 服务器和批处理等工作负荷从 F 系列的优势中获益。

Fs 系列具有 F 系列的所有优势（在高级存储的基础上）。

## <a name="fsv2-series-sup1sup"></a>Fsv2 系列 <sup>1</sup>

ACU：195 - 210

| 大小             | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大 NIC 数/预期网络带宽 (MBps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | 中等                                       |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | 中等                                       |
| Standard_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | 高                                           |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | 高                                           |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | 极高                                 |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | 极高                                 |
| Standard_F72s_v2<sup>2, 3</sup> | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | 极高                                 |

<sup>1</sup> Fsv2 系列 VM 的 Intel® 超线程技术功能

<sup>2</sup> 超过 64 vCPU 的 VM 需要以下受支持的来宾 OS 之一：Windows Server 2016、Ubuntu 16.04 LTS、SLES 12 SP2 和 Red Hat Enterprise Linux、CentOS 7.3 或带 LIS 4.2.1 的 Oracle Linux 7.3

<sup>3</sup> 实例与专用于单个客户的硬件隔离。

## <a name="fs-series-sup1sup"></a>Fs 系列 <sup>1</sup>

ACU：210 - 250

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数/预期网络带宽 (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4,000 / 32 (12) |3,200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8,000 / 64 (24) |6,400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16,000 / 128 (48) |12,800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32,000 / 256 (96) |25,600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64,000 / 512 (192) |51,200 / 768 |8 / 12000 |

MBps = 每秒 10^6 字节，GiB = 1024^3 字节。

<sup>1</sup> Fs 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/virtual-machines/windows/premium-storage.md)。


<br>

## <a name="f-series"></a>F 系列

ACU：210 - 250

| 大小         | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 临时存储的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000/46/23                                           | 4/4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000/93/46                                           | 8/8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000/187/93                                         | 16/16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000/375/187                                        | 32/32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000/750/375                                        | 64/64x500                       | 8 / 12000           |


<br>


