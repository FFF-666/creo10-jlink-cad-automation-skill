# Creo 10 / J-Link 安全自动化 SOP

## 1. 适用范围与环境

本 SOP 适用于 Creo Parametric 10.0.0.0、Java/J-Link、参数化工作副本、DXF/STL 中性几何和 CAD 性能审查。真实成功案例使用过：

```text
Creo: C:\PTC_Creo10\Parametric\bin\parametric.exe
JDK 11: C:\Creo10_Codex_JDK11
J-Link library: C:\PTC_Creo10\Common Files\text\java\otk.jar
```

这些路径是本机验证记录，不是所有电脑的固定路径。先只读检查，未经要求不升级 Creo、JDK 或库。

## 2. 只读盘点

目的：确定环境、模型状态和可用 API，不改变外部状态。

检查：

- `parametric.exe`、JDK、`otk.jar` 的存在、版本和大小。
- `protk.dat`、`config.pro`、入口类、JAR、工作目录和输出目录。
- 当前 Creo/xtop 进程，避免接管用户会话或并发写同一目录。
- 源模型、工作副本、报告和输出的文件哈希。
- Java 源码中是否在循环里反复连接会话、再生、保存、激活窗口、重绘或导出。
- 本机 PTC 示例和 APIWizard 对目标方法的支持状态。

预期：得到可复核清单；未发生文件或 Creo 模型写入。

## 3. 自动化路线决策

| 情况 | 首选路线 |
|---|---|
| 只改已有尺寸/参数 | 打开参数化副本 → 批量设置 → 单次再生 → 回读 → 验证 → 保存 |
| 重复结构 | 一个基础特征 → Pattern/阵列/Relations；避免 Java 逐个绘制 |
| 简单新特征 | 仅使用本机已验证的 J-Link/API 方法 |
| 复杂新拓扑 | 先评估参数化模板、骨架、UDF、Pattern 或完整 Object TOOLKIT |
| API 暂时不能完成 | 记录阻塞、示例和替代路线；得到明确许可后才使用 GUI |

免费 J-Link/pfc 可以可靠连接、导入、再生、读取、测量和保存，但本机 APIWizard 把 `pfcSolid.Solid.CreateFeature(...)` 标为当前版本未实现。不要据此声称能从零创建原生 Sketch。真正原生特征可评估模板/UDF，或在许可确认后评估 `startup otk_java` 与 wfc API。

## 4. 工作副本与防覆盖

1. 计算源模型 SHA-256。
2. 创建新目录和新模型名；同一 Creo 会话不要复用已加载模型名。
3. 复制/检索模型时验证输入与输出路径不同。
4. 所有操作只对副本执行。
5. 保存后再次计算源哈希，必须与步骤 1 一致。

不得直接修改 `.prt` 二进制。不得把删除、抑制或另存操作指向 original/source_original。

## 5. Java 构建与注册

经本机真实案例验证的构建形式：

```powershell
& 'C:\Creo10_Codex_JDK11\bin\javac.exe' --release 8 -encoding UTF-8 -cp 'C:\PTC_Creo10\Common Files\text\java\otk.jar' -d '<PROJECT_ROOT>\build\classes' '@<PROJECT_ROOT>\build\sources.txt'
& 'C:\Creo10_Codex_JDK11\bin\jar.exe' cfm '<PROJECT_ROOT>\dist\app.jar' '<PROJECT_ROOT>\src\main\resources\META-INF\MANIFEST.MF' -C '<PROJECT_ROOT>\build\classes' .
```

使用 [protk.dat.template](../assets/protk.dat.template) 和 [config.pro.template](../assets/config.pro.template)。真实成功注册采用：

```text
startup java
java_app_class <fully.qualified.EntryClass>
java_app_classpath <PROJECT_ROOT>\dist\app.jar
delay_start true
```

Creo 从启动工作目录中的 `config.pro` 读取 `jlink_java_command` 和 `protkdat`。经验证案例由用户在“辅助应用程序”中启动应用；这不是用鼠标绘制几何。

## 6. 标准模型处理流程

### 参数化模板路线

```text
复制模板
→ 缓存 Session/Model/Parameter/Feature
→ 确认主单位
→ 批量写参数
→ 单次完整 Regenerate
→ ListFailedFeatures()==0
→ 回读全部关键参数
→ 包围盒/质量属性/特征数验证
→ CheckIsSaveAllowed
→ Save 一次
→ 文件和源哈希验证
```

尺寸不能按“第一个维度”猜测。先枚举特征和维度，记录稳定特征 ID/名称、`GetSymbol()`、类型、参考状态和值；按稳定符号定位。改单位前必须理解转换模式，真实 L1 案例使用 `SetPrincipalUnits` 与 `UNITCONVERT_SAME_DIMS` 保持数值并转换为 mm。

### 中性几何路线

Java 可生成 DXF/STL，再由 J-Link `ImportNewModel` 导入新模型。中性几何必须先检查单位、边界、闭合轮廓、退化三角形、非流形边、重复面和层间投影。导入得到导入特征，不等于原生参数化草绘。

### DXF 参考曲线路线

可评估 `IntfDXF_Create`、`ImportFeatAttr_Create`、`SetJoinSurfs(false)`、`SetMakeSolid(false)` 与 `CreateImportFeat`，并验证曲线、Z 平面和 XY 边界。本机存在相关 J-Link 示例，但这个调用组合尚未在“打印2”项目完整实跑，不能标为已验证原生 Sketch。

## 7. 最终验证门

至少记录：

```text
SESSION_CONNECT_MS=
MODEL_OPEN_MS=
PARAMETER_READ_MS=
PARAMETER_UPDATE_MS=
SKETCH_CREATION_MS=
FEATURE_CREATION_MS=
BATCH_FEATURE_CREATION_MS=
REGENERATION_MS=
SAVE_MS=
EXPORT_MS=
REPORT_WRITE_MS=
TOTAL_MS=
```

通过条件：

- 完整再生成功；`ListFailedFeatures()` 为 0。
- 单位和全部关键尺寸/参数回读正确。
- 几何包围盒、厚度、端口/孔位、特征数符合预期。
- 需要时，质量属性与理论或基线值在容差内。
- 保存前允许保存；保存后未修改状态为 false。
- 输出文件存在且哈希可记录。
- 保护源哈希未改变。

任何一项失败：不保存、不宣称成功，保留报告并输出最小阻塞点。

## 8. 回滚与交接

- 删除或隔离失败的输出副本，不动源模型。
- 恢复到最近一次已验证的工作副本，而不是覆盖式回退。
- 记录已验证、已实现未验证、仅建议三种状态。
- 交接必须包含入口类、构建命令、注册文件、输出、哈希、最后成功测试、已知问题和下一步。

## 9. 禁止事项

- 不读取聊天记忆后就编造 API、路径、命令或结果。
- 不因为屏幕上看到几何就判定成功。
- 不在每个循环里获取 Session、再生、保存、切换窗口、重绘或导出。
- 不把球体成功推断为复杂模型、孔、Pattern、UDF 或原生 Sketch 已通过。
- 不反复冷启动等待 J-Link 上下文；一次受控重试后应停止并诊断。
- 不默认使用鼠标坐标或屏幕识别。

