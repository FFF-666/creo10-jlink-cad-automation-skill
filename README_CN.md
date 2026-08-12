<p align="center">
  <img src="docs/images/creo10-jlink-automation-hero.png" alt="Creo 10 J-Link 安全 CAD 自动化：球体、API 验证与两层微流道" width="100%">
</p>

# Creo 10 J-Link CAD Automation Skill

这是从一组真实的 Creo Parametric 10.0.0.0、Java/J-Link 球体和微流道案例中整理出的可复用 Codex Skill。它把安全修改、工作副本、参数回读、完整再生、几何校验、性能优化和 GUI 备用路线固化为同一套流程。

一句话定位：**让 Codex 通过可验证的 API 流程操作 Creo，而不是默认模拟人工点击。**

## 能做什么

- 为 Creo 10 Java/J-Link 项目做只读环境盘点和 API 路线选择。
- 在不覆盖原始 `.prt` 的条件下修改工作副本。
- 校验再生、失败特征、参数/尺寸、包围盒、质量属性、保存状态和文件哈希。
- 设计两层微流道、居中主干、斜向出口和凸起混合结构。
- 区分原生参数化特征、导入特征、中性 DXF/STL 验证和仅屏幕可见结果。
- 通过分段计时、批量更新、单次再生和延迟导出优化性能。

## 仓库结构

```text
skills/creo10-jlink-cad-automation/
├─ SKILL.md
├─ agents/openai.yaml
├─ references/
│  ├─ AUTOMATION_SOP_CN.md
│  ├─ DESIGN_PATTERNS_CN.md
│  ├─ PERFORMANCE_FAILURES_CN.md
│  └─ VERIFIED_CASES_CN.md
└─ assets/
   ├─ config.pro.template
   ├─ job.properties.template
   └─ protk.dat.template
```

## 使用

将 `skills/creo10-jlink-cad-automation` 复制到个人 Codex skills 目录，或从本仓库安装。新任务中明确调用：

```text
使用 $creo10-jlink-cad-automation 审查并安全修改这个 Creo 10 工作副本，完成再生、参数回读和保存验证。
```

## 发布范围

本仓库只包含原创说明、脱敏案例数据和配置模板，不包含 Creo 安装文件、PTC `otk.jar`、JDK、`.prt`、大型 STL、课程原件、密码或令牌。实际运行路径和本机证据保存在被 `.gitignore` 排除的 `.local/` 中。

## 已知边界

- 已完整验证：50 mm 球体的 Java 生成二进制 STL → J-Link 导入 → 再生 → Creo 几何/质量属性回读 → 保存。
- 已完整验证：当前“打印”L1 工作副本的 mm 单位、`d411=0.050 mm`、几何厚度、失败特征为 0 和保存结果。
- 仅中性几何验证：居中主干/右下斜向出口版本的 DXF/STL/SVG。
- 尚未最终验收：用户仍在添加内部细节的“打印2”L2。
- 尚未验证：在免费 J-Link/pfc 路线中从零创建真正原生、可逐尺寸编辑的 Sketch 特征树。

## 许可与商标

原创文本和模板使用 MIT License。Creo、Creo Parametric、J-Link、Object TOOLKIT 和 PTC 是其各自权利人的产品或商标；本项目不分发其专有组件，也不代表 PTC 官方支持。
