# 《欧拉的倒影》C++ 核心系统完整设计文档（覆盖版）

## 一、系统目标
- 管理游戏核心 2D/3D 法则层和章节法则状态
- 控制机关、平台、门、桥和动态环境
- 支持多种二维显影方式：墙面、地面、影子轮廓、切片空间
- 提供蓝图接口和编辑器接口，支持快速原型和长期可扩展性

## 二、核心模块设计

### 1. RuleManager
**职责与实现**：
- 管理每个章节的法则状态和全局规则
- 提供接口给 ProjectionLayer、MechanismController 和蓝图调用
- 保存每章节的当前 2D/3D 显影模式、节点状态、事件触发状态和机关依赖关系
- 方法接口:
  - `SetCurrentChapter(int ChapterID)`
  - `GetActiveProjectionLayer()`
  - `ValidateRuleChange(NodeChange)`
  - `TriggerGlobalEvent(EventID)`
  - `QueryNodeStatus(NodeID)`
- 异步事件处理，保证规则状态与 3D 对象同步

### 2. ProjectionLayer
**职责与实现**：
- 封装 2D 法则层显示与玩家交互逻辑
- 支持多种表现形式统一接口处理
- 方法接口:
  - `ActivateLayer()` / `DeactivateLayer()`
  - `UpdateLayerVisuals()`
  - `ApplyPlayerInput(InputAction)`
  - `RotateNode(NodeID, Rotation)`
  - `MoveNode(NodeID, Position)`
- 内部维护节点数据结构（位置、状态、可移动性）、连线关系、约束条件
- 与 RuleManager 同步状态，保证 3D 世界逻辑更新

### 3. MechanismController
**职责与实现**：
- 控制机关、平台、门、桥梁、动态物体
- 响应 ProjectionLayer 和 RuleManager 状态变化
- 方法接口:
  - `MovePlatform(PlatformID, TargetPos)`
  - `OpenDoor(DoorID)`
  - `TriggerEvent(EventID)`
  - `ResetMechanism(MechanismID)`
- 内部逻辑:
  - 与 RuleManager 的依赖关系匹配
  - 物理状态同步（碰撞、位置、旋转）
  - 触发条件验证

### 4. ChapterManager
**职责与实现**：
- 管理章节数据、布局和初始节点状态
- 方法接口:
  - `LoadChapterData(ChapterID)`
  - `GetChapterLayout()`
  - `GetInitialNodeStates()`
- 保存每章 2D/3D 显影层的默认状态
- 支持配置文件解析 (JSON/CSV) 用于快速数据更新
- 提供跨章节数据接口，支持资源加载和状态重置

## 三、蓝图接口
- Blueprint 可调用函数:
  - `RuleManager::GetActiveProjectionLayer()`
  - `ProjectionLayer::ApplyPlayerInput()`
  - `MechanismController::OpenDoor()`
  - `ChapterManager::LoadChapterData()`
- 编辑器工具可通过 Python 或 Editor Utility 调用 C++ 接口进行测试和布局
- 蓝图用于场景绑定、玩家操作触发、UI反馈

## 四、2D/3D 显影层映射
| 章节 | 2D 表现方式 |
|---|---|
| 序章 | 墙面二维显影板 |
| 镜面花园 | 镜面/水面显影 |
| 映射工坊 | 墙面蓝图/机械图纸 |
| 钟摆塔 | 地面规则层 |
| 连线水道 | 地面网络拓扑图 |
| 投影回廊 | 影子轮廓交互 |
| 无穷观测所 | 局部切片空间 |
| 投影终章 | 多种二维表现汇合 |

## 五、系统开发建议
1. 原型阶段先集成在项目中，插件化可后期处理
2. 优先实现 RuleManager + ProjectionLayer + 基础 MechanismController
3. 蓝图快速绑定场景元素进行玩法验证
4. 后续整理成插件，提供编辑器面板和通用蓝图节点接口
5. 统一核心接口管理所有 2D 显影方式，按章节切换显影媒介
6. 事件驱动机制，保证玩家操作与 3D 世界即时同步
7. 每章可配置 2D 显影层参数，便于调试与关卡设计

## 六、注意事项
- 无需修改 UE5 光照系统，可直接使用材质发光、线框或粒子表现 2D 层
- 保持模块化，确保跨章节可复用
- C++ 处理性能敏感逻辑，蓝图负责可视化和场景绑定
- 确保 3D 与 2D 状态一致，玩家操作即时反馈至机关和平台
- 提前规划节点和约束数据结构，以支持复杂关卡扩展