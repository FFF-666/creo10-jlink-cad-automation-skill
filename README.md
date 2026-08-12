<p align="center">
  <img src="docs/images/creo10-jlink-automation-hero.png" alt="Creo 10 J-Link safe CAD automation workflow: sphere, API validation, and two-layer microfluidic design" width="100%">
</p>

# Creo 10 J-Link CAD Automation Skill

一套基于真实 Creo Parametric 10.0.0.0 案例整理的 Codex Skill：用 Java/J-Link 安全创建、修改、优化和验证 CAD 工作副本，并把 GUI 自动化严格限制为最后备用路线。

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

## 已验证案例

| 案例 | 自动化路线 | 状态 |
|---|---|---|
| 50 mm 球体 | Java 二进制 STL → J-Link 导入 → 再生 → 包围盒/质量属性 → 保存 | 完整通过 |
| “打印”L1 | 工作副本 → 转换为 mm → `d411=0.050 mm` → 再生/回读/保存 | 完整通过 |
| 居中主干与右下斜向出口 | 配置驱动 DXF/STL/SVG → 投影/流形/单位验证 | 中性几何通过；Creo PRT 未跑 |
| “打印2”L2 | 继续设计副本与参考轮廓 | 未完成；等待用户添加内部结构 |

球体案例测得三轴直径均为 `50.000000000 mm`、失败特征为 `0`；关闭非必要预览导出后，稳定运行总耗时中位数由 `940.708 ms` 降至 `451.460 ms`。这些结果不能推断原生 Sketch、Pattern、UDF 或复杂拓扑已验证。

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

