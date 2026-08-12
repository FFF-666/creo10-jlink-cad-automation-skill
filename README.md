<p align="center">
  <img src="docs/images/creo10-jlink-automation-hero.png" alt="Creo 10 J-Link safe CAD automation workflow: sphere, API validation, and two-layer microfluidic design" width="100%">
</p>

# AI-Assisted Automatic CAD with Creo 10 & J-Link

这是一个使用 AI 辅助规划、Java/J-Link 执行、Creo 原生能力建模并以真实回读证据验收的自动 CAD 项目。它把自然语言设计需求转化为可重复的参数与几何操作，同时把工作副本、单位检查、完整再生、尺寸回读、质量属性和文件校验纳入同一条安全流水线。

项目面向一类小型双层流道工件：基底层形成三端口的分流/汇流通道，中心主流道沿水平方向布置，并通过斜向连接到下游出口；第二层预留导流柱、斜置叶片或重复微结构，用于改善流体扰动与混合。设计要求包括毫米单位、固定端口位置、受控层高、层间投影对齐、可继续编辑，以及未经验证不得覆盖源模型。

[中文详细说明](README_CN.md) · [本地验收报告](VALIDATION_REPORT_CN.md) · [安全与发布边界](SECURITY.md)

## 核心原则

```text
J-Link / Creo API
→ 参数化模板与批量参数修改
→ Creo 原生命令、Relations、Pattern、UDF
→ Mapkey 或 Trail
→ 鼠标/键盘 GUI 自动化（仅在明确授权后）
```

- 不直接编辑 `.prt` 二进制，不覆盖 original/source_original。
- 修改前创建唯一工作副本并记录源文件 SHA-256。
- 再生失败、存在失败特征、单位错误或回读不一致时禁止保存。
- 不能只凭屏幕可见结果判断成功；必须核验再生、参数、几何、保存和文件证据。
- PTC API 不确定时先检查本机示例和 APIWizard，禁止编造方法。

## 工程案例与验证状态

| 案例 | 自动化路线 | 状态 |
|---|---|---|
| 50 mm 球体 | Java 二进制 STL → J-Link 导入 → 再生 → 包围盒/质量属性 → 保存 | 完整通过 |
| 基底流道层 L1 | 工作副本 → 转换为 mm → `d411=0.050 mm` → 再生/回读/保存 | 完整通过 |
| 居中主干与右下斜向出口 | 配置驱动 DXF/STL/SVG → 投影/流形/单位验证 | 中性几何通过；Creo PRT 未跑 |
| 混合结构层 L2 | 继续设计副本与参考轮廓 | 未完成；等待补充内部导流结构 |

球体案例测得三轴直径均为 `50.000000000 mm`、失败特征为 `0`；关闭非必要预览导出后，稳定运行总耗时中位数由 `940.708 ms` 降至 `451.460 ms`。这些结果不能推断原生 Sketch、Pattern、UDF 或复杂拓扑已验证。

## AI 辅助约束草绘实例

<p align="center">
  <img src="docs/images/creo-sketch-constraints-case.png" alt="Creo 10 中三端口双层流道工件的约束草绘、尺寸与基准检查" width="100%">
</p>

上图展示了真实的 Creo 草绘阶段：三处圆形端口由 Y 形汇流段、居中水平主流道和斜向出口连接；蓝色尺寸与几何约束用于控制端口位置、支路夹角、通道宽度、转折位置和基准关系。AI 的职责不是替代工程验收，而是帮助整理需求、选择 API/模板路线、生成或修改参数、发现过度约束与单位风险，并在保存前执行再生和回读验证。

该截图只用于说明约束复杂度和自动化目标。它不代表图中所有尺寸均为最终制造值；正式版本仍应把关键参数集中到模板或配置文件中，减少密集的手工尺寸链。

## 安装

将 Skill 目录复制到个人 Codex skills 目录：

```text
skills/creo10-jlink-cad-automation/
```

或从本仓库安装后，在新任务中调用：

```text
使用 $creo10-jlink-cad-automation 审查并安全修改这个 Creo 10 工作副本，完成再生、参数回读和保存验证。
```

## 内容导航

- [Skill 入口](skills/creo10-jlink-cad-automation/SKILL.md)
- [完整自动化 SOP](skills/creo10-jlink-cad-automation/references/AUTOMATION_SOP_CN.md)
- [两层微流道与可编辑设计模式](skills/creo10-jlink-cad-automation/references/DESIGN_PATTERNS_CN.md)
- [性能优化和失败路径](skills/creo10-jlink-cad-automation/references/PERFORMANCE_FAILURES_CN.md)
- [真实案例与验证边界](skills/creo10-jlink-cad-automation/references/VERIFIED_CASES_CN.md)
- [J-Link 注册和任务模板](skills/creo10-jlink-cad-automation/assets)

## 公开发布范围

仓库只包含原创文档、配置模板和原创封面图，不包含 Creo/JDK 安装文件、PTC `otk.jar`、`.prt`、STL、课程原件、令牌或用户路径。本机原始证据保存在被 Git 忽略的 `.local/`。

> Creo、Creo Parametric、J-Link、Object TOOLKIT 和 PTC 是各自权利人的产品或商标。本项目不分发其专有组件，也不代表 PTC 官方支持。

## License

[MIT](LICENSE)
