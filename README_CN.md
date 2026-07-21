[English](README.md) | [中文](README_CN.md)

# Flysky PL18 ExpressLRS 发射模块

一个自制的 Flysky Paladin PL18 遥控器内置 ExpressLRS 发射模块，利用原装 FRM301 模块的外壳和射频模块进行改装。

## 概述

Flysky PL18 出厂时标配 FRM301 外置发射模块。本项目将 FRM301 的内部 PCB 替换为定制载板，使用 **Waveshare ESP32-S3-Tiny** 作为协议桥接，并复用 FRM301 的 **HF200 射频模块** 作为 2.4GHz ExpressLRS 前端。最终得到的是一套功能齐全、即插即用的 ExpressLRS 发射模块，可直接装入原有模块仓，通过 5 针 SPORT 接口与遥控器通信。

### 特性

- FRM301 模块的即插即用替换方案 — 复用原装外壳
- Waveshare ESP32-S3-Tiny 运行 ExpressLRS 固件
- HF200 2.4GHz 射频模块，支持天线分集切换
- 双 WS2812B-2020 RGB 状态指示灯
- 引导按钮，方便刷写固件
- 完全由遥控器 5V 供电轨供电

## 照片

<img src="docs/module_assembled_top.jpg" width="400" alt="组装好的模块 — 顶面" />
<img src="docs/module_assembled_bottom.jpg" width="400" alt="组装好的模块 — 底面" />
<img src="docs/module_in_casing.jpg" width="400" alt="模块装入 FRM301 外壳" />
<img src="docs/module_installed.jpg" width="400" alt="模块安装在 PL18 模块仓中" />

## 物料清单

| 位号 | 物料 | 数量 | 备注 |
|------|------|:---:|------|
| — | PL18-ELRS PCB | 1 | 使用本仓库中的 Gerber 文件下单 |
| — | FRM301 拆机模块 | 1 | 拆取 HF200 射频模块和塑料外壳 |
| U1 | Waveshare ESP32-S3-Tiny | 1 | [产品页面](https://www.waveshare.com/esp32-s3-tiny.htm) |
| U2 | HF200 射频模块 | 1 | 从 FRM301 上拆焊 |
| D1, D2 | WS2812B-2020 | 2 | 2.0×2.0mm PLCC4 封装 RGB LED |
| R1, R2 | 10kΩ 0603 电阻 | 2 | |
| SW1 | KMR2 轻触开关 | 1 | 3.2×4.2mm |
| J1 | 5 针 2.54mm 排针 | 1 | DS-B01F-A-S2 或等效型号 |

## 引脚定义

### PL18 接口 (J1)

| 引脚 | 信号 | ESP32-S3 GPIO | 说明 |
|:---:|:------:|:-------------:|------|
| 1 | SPORT | IO04 | FrSky Smart Port（双向遥测） |
| 2 | GND | — | 地 |
| 3 | +5V | — | 遥控器 5V 电源输入 |
| 4 | NC | — | 未连接 |
| 5 | NC | — | 未连接 |

### ESP32-S3 至 HF200 射频模块

| ESP32-S3 GPIO | HF200 引脚 | 功能 |
|:-------------:|:---------:|------|
| IO04 | — | SPORT（至 PL18 接口） |
| IO06 | TXEN | 发射使能 |
| IO07 | RXEN | 接收使能 |
| IO08 | DI01 | 双向数据 |
| IO09 | BUSY | 忙碌状态（输入） |
| IO10 | SPI_NSS | SPI 片选 |
| IO11 | SPI_MOSI | SPI MOSI |
| IO12 | SPI_CLK | SPI 时钟 |
| IO13 | SPI_MISO | SPI MISO |
| IO14 | RESET | HF200 复位 |
| IO15 | L_ANT_SW | 左天线切换 |
| IO16 | R_ANT_SW | 右天线切换 |
| IO17 | SW1 | 引导按钮（低电平有效） |
| IO18 | D1 DIN | WS2812B LED 数据线 |

### 供电

| 电源轨 | 来源 | 供电对象 |
|--------|------|----------|
| +5V | PL18 接口 (J1) | ESP32-S3-Tiny (U1)、HF200 (U2) |
| +3V3 | ESP32-S3 内部稳压器 | WS2812B LED (D1, D2)、L_POWER 上拉 (R2) |

## 原理图与 PCB

PCB 设计使用 **KiCad 10.0** 完成。

- **层数：** 2 层 FR4，厚度 1.6mm
- **铜厚：** 35μm（1oz）
- **表面处理：** HASL 或 ENIG
- **板框：** 定制外形，匹配 FRM301 内部腔体

### KiCad 项目文件

| 文件 | 说明 |
|------|------|
| `PL18_ELRS.kicad_pro` | KiCad 项目文件 |
| `PL18_ELRS.kicad_sch` | 原理图 |
| `PL18_ELRS.kicad_pcb` | PCB 布局 |
| `Multi.kicad_sym` | 自定义符号库 |
| `Multi.pretty/` | 自定义封装库 |

自定义封装包括与拆机零件匹配的 `WS_ESP32S3_Tiny`（23 针 SMT 模块）和 `HF200-SMT`（22 针射频模块）封装。

## 组装

1. **拆取 HF200。** 打开 FRM301 模块，从原 PCB 上小心拆焊 HF200 射频模块。将塑料外壳留待后续使用。
2. **焊接 HF200 (U2)。** 将 HF200 模块安装在 PL18-ELRS PCB **背面** 的 U2 封装位置。
3. **焊接 ESP32-S3-Tiny (U1)。** 将其安装在 **正面** 的 U1 封装位置。
4. **焊接无源元件和 LED。** 在 **正面** 安装 R1、R2（10kΩ 0603）、D1、D2（WS2812B-2020）和 SW1（KMR2 开关）。
5. **焊接排针 (J1)。** 将 5 针排针安装在 **背面**，确保针脚朝向模块后方，以便与 PL18 仓内接口正确对齐。
6. **装入外壳。** 将组装好的 PCB 装入拆下的 FRM301 塑料外壳中。

## 固件

Flysky PL18 的 ExpressLRS 目标机定义维护于：

**[github.com/richardclli/ELRS-targets](https://github.com/richardclli/ELRS-targets)**

### 刷写

1. 使用 ExpressLRS Configurator，选择 **Flysky PL18** 目标。
2. 通过 USB 将 ESP32-S3-Tiny 连接到电脑。
3. 在上电（或连接 USB）时按住引导按钮 (SW1)。
4. 使用 configurator 刷写固件。

刷写完成后，将 ExpressLRS 配置为使用 **SPORT** 协议进行遥测。

## 生产制造

本仓库根目录中包含全套制造输出文件：

| 文件 | 说明 |
|------|------|
| `PL18_ELRS-F_Cu.gbr` | 顶层铜皮 |
| `PL18_ELRS-B_Cu.gbr` | 底层铜皮 |
| `PL18_ELRS-F_Mask.gbr` | 顶层阻焊 |
| `PL18_ELRS-B_Mask.gbr` | 底层阻焊 |
| `PL18_ELRS-F_Paste.gbr` | 顶层钢网 |
| `PL18_ELRS-B_Paste.gbr` | 底层钢网 |
| `PL18_ELRS-F_Silkscreen.gbr` | 顶层丝印 |
| `PL18_ELRS-B_Silkscreen.gbr` | 底层丝印 |
| `PL18_ELRS-Edge_Cuts.gbr` | 板框外形 |
| `PL18_ELRS-PTH.drl` | 金属化孔钻孔 |
| `PL18_ELRS-NPTH.drl` | 非金属化孔钻孔 |

下单 PCB 时，将所有 Gerber 和钻孔文件打包压缩，上传至您选择的 PCB 厂家（JLCPCB、PCBWay 等），并指定以下参数：

- **层数：** 2
- **厚度：** 1.6mm
- **材质：** FR4
- **铜厚：** 1oz
- **表面处理：** HASL（手工焊接建议选择 ENIG）

## 开源协议

本项目采用 **MIT 协议** 开源 — 详见 [LICENSE](LICENSE) 文件。
