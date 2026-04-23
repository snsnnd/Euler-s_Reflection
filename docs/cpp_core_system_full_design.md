# 《欧拉的倒影》C++ 核心系统完整设计文档

## 一、系统目标
- 管理游戏核心 2D/3D 法则层
- 控制章节机关、平台和交互逻辑
- 支持多种二维显影方式：墙面、地面、影子轮廓、切片空间
- 提供蓝图和编辑器接口，保持可扩展性和可复用性

## 二、核心模块设计

### 1. RuleManager
**职责**：管理当前章节法则状态，提供接口查询和更新

**主要功能**：
- `SetCurrentChapter(int ChapterID)`
- `GetActiveProjectionLayer()`
- `ValidateRuleChange(NodeChange)`
- 管理全局规则数据和触发事件队列

### 2. ProjectionLayer
**职责**：封装 2D 法则层表现和玩家交互

**主要功能**：
- `ActivateLayer()` / `DeactivateLayer()`
- `GetNodeStatus(NodeID)`
- `ApplyPlayerInput(InputAction)`
- 支持多种表现方式：墙面板、地面网格、影子轮廓、切片空间

### 3. MechanismController
**职责**：控制机关、平台、门和动态环境

**主要功能**：
- `MovePlatform(PlatformID, TargetPos)`
- `OpenDoor(DoorID)`
- `TriggerEvent(EventID)`
- 响应 ProjectionLayer 状态更新，实现 3D 世界同步变化

### 4. ChapterManager
**职责**：管理每章场景数据及初始节点状态

**主要功能**：
- `LoadChapterData(ChapterID)`
- `GetChapterLayout()`
- `GetInitialNodeStates()`
- 提供跨章节数据接口给 RuleManager 和 ProjectionLayer

## 三、蓝图接口
- Blueprint 可调用函数：
  - `RuleManager::GetActiveProjectionLayer()`
  - `ProjectionLayer::ApplyPlayerInput()`
  - `MechanismController::OpenDoor()`
- 编辑器工具可通过 Python 或 Editor Utility 调用 C++ 接口进行测试和布置

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
2. 优先实现 RuleManager + ProjectionLayer + 简单 MechanismController
3. 蓝图快速绑定场景元素进行初步玩法验证
4. 后续整理成插件，提供编辑器面板和通用蓝图节点接口
5. 所有 2D 表现方式统一核心接口，按章节切换具体显影媒介

## 六、开发注意事项
- 无需修改 UE5 光照系统，可直接使用材质发光、粒子或线框实现 2D 显影层
- 保持模块化，确保不同章节共享核心逻辑
- 使用 C++ 处理性能敏感逻辑，蓝图仅负责绑定和可视化交互
- 确保 3D 与 2D 状态一致，玩家操作及时反馈到机关和平台上

此文档可作为《欧拉的倒影》核心逻辑系统开发参考，便于团队协作、后续扩展和插件化处理。