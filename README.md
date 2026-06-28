---
AIGC:
  ContentProducer: '001191110102MAD55U9H0F10002'
  ContentPropagator: '001191110102MAD55U9H0F10002'
  Label: '1'
  ProduceID: '5d701f1e-9a62-4e1c-baf3-c641dea9fb34'
  PropagateID: '5d701f1e-9a62-4e1c-baf3-c641dea9fb34'
  ReservedCode1: '99c65d2c-787f-4d0b-b933-abd0de7fc436'
  ReservedCode2: '99c65d2c-787f-4d0b-b933-abd0de7fc436'
---

# 2025 RM Sentry — JSU

江苏大学 RoboMaster 战队 2025 赛季哨兵机器人电控代码。

基于 [HNUYueLuRM/basic_framework](https://github.com/HNUYueLuRM/basic_framework)（湖南大学跃鹿战队 2022-2023 通用嵌入式框架）二次开发。

## 项目概述

- **兵种**: 哨兵 (Sentry)
- **MCU**: STM32F407IG (RoboMaster C 型开发板)
- **RTOS**: FreeRTOS
- **赛季**: 2025

## 硬件配置

| 模块 | 型号 / 说明 |
|------|-------------|
| 云台 | 大 yaw (达妙 DM4310) + 小 yaw (GM6020) + pitch (GM6020) |
| 底盘 | 4 × DM4310 全向轮 / 麦轮 |
| 发射 | 摩擦轮 (2 × M3508) + 拨弹盘 (M2006) + 弹舱盖 (MG90 舵机) |
| IMU | BMI088 (板载) + 达妙 DM-IMU |
| 遥控 | DT7 / DR16 |
| 视觉通信 | USB 虚拟串口 (上位机) |
| 超级电容 | 超电控制板 (CAN 通信) |

## 代码结构

```
├── application/        # 应用层 — 机器人各子系统任务
│   ├── chassis/        #   底盘控制
│   ├── gimbal/         #   云台控制
│   ├── shoot/          #   发射机构控制
│   └── cmd/            #   指令分发 (遥控器/视觉/裁判系统)
├── modules/            # 模块层 — 电机/IMU/裁判系统/超级电容等驱动
│   ├── motor/          #   电机驱动 (DJI / DM / HT / LK / 舵机)
│   ├── imu/            #   姿态解算 (INS)
│   ├── algorithm/      #   PID / 滤波器
│   ├── referee/        #   裁判系统数据解析
│   ├── super_cap/      #   超级电容通信
│   ├── can_comm/       #   多板 CAN 通信
│   └── ...
├── bsp/                # BSP 层 — 硬件外设抽象 (CAN/USART/SPI/I2C/USB/...)
├── Src/ & Inc/         # CubeMX 生成的 HAL 初始化代码
├── Drivers/            # STM32 HAL 库
├── Middlewares/        # FreeRTOS
├── docs/               # 开发笔记与协议文档
│   ├── 经验.md          #   调试经验与踩坑记录
│   ├── 待解决的问题.md   #   已知问题追踪
│   ├── 规则.md          #   裁判系统数据协议
│   ├── jsdx.md          #   视觉通信协议 (上位机串口数据帧格式)
│   └── upstream_framework_readme.md  # 上游框架原始 README
└── .Doc/               # 上游框架文档 (环境配置/架构指南/PID 整定等)
```

## 核心功能

- **云台控制**: 大 yaw 巡航 + 自瞄，姿态闭环 (角度/速度双环)，支持达妙 IMU 与板载 IMU 双源
- **底盘控制**: 全向运动解算，功率限制，超级电容功率管理
- **发射控制**: 摩擦轮调速 + 拨弹盘定量供弹 + 弹舱盖舵机 + 单发限位 (微动开关)
- **视觉通信**: USB 虚拟串口接收上位机自瞄/导航指令，支持小陀螺判断与开火建议
- **裁判系统**: 完整协议解析，实时功率/血量/弹量监测
- **多板协同**: CAN 总线主从通信，支持主控板 + 超电板分布式架构

## 编译与烧录

1. 修改 `application/robot_def.h` 中的机器人配置宏
2. 安装 arm-gnu 工具链 (arm-none-eabi-gcc)，详见 `.Doc/VSCode+Ozone使用方法.md`
3. VSCode 中运行构建任务 (Ctrl+Shift+B)
4. 通过 J-Link / ST-Link / DAP 下载

## 致谢

- 上游框架: [HNUYueLuRM/basic_framework](https://github.com/HNUYueLuRM/basic_framework) — 湖南大学跃鹿战队
- 姿态解算参考: 哈尔滨工程大学创梦之翼 四元数 EKF
- 裁判系统解析参考: 深圳大学 RoboPilot 2021 英雄开源

## 许可证

本项目代码遵循 MIT 许可证 (与上游框架一致)，详见 [LICENSE](LICENSE)。

> AI生成