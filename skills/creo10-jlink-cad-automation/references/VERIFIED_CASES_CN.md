# 真实案例、证据与边界

本文只记录本次工作资料中存在真实日志或输出证据的结果。路径已脱敏，详细本机路径保存在不提交的 `.local/LOCAL_PROVENANCE_CN.md`。

## 1. 50 mm 球体：完整通过

实际路线：

```text
Java 生成高质量二进制 STL 球体
→ J-Link Session.ImportNewModel(... IMPORT_NEW_STL ...)
→ Solid.Regenerate(null)
→ Solid.ListFailedFeatures()
→ Solid.GetGeomOutline()
→ Solid.GetMassProperty(null)
→ Model.Save()
→ 保存后状态和文件哈希验证
```

关键结果：

| 项目 | 结果 |
|---|---|
| Creo | 10.0.0.0 |
| JDK | Amazon Corretto OpenJDK 11.0.32；以 `--release 8` 编译 |
| 入口类 | `com.guoming.creo.sphere.SphereAutoApp` |
| 三轴直径 | `50.000000000 mm` |
| 包围盒 | `(-25,-25,-25)` 到 `(25,25,25) mm` |
| 失败特征 | `0` |
| Creo 体积 | `65420.646860984 mm³` |
| Creo 表面积 | `7852.229421666 mm²` |
| 保存 | `model.saved=true`，保存后 modified=false |
| 鼠标建模 | `0` |
| 输出 PRT SHA-256 | `800F606504D5BCC5E62859DCF879C37E4C8DCA3F31ADC891B0230AB28A319A1E` |
| 输出 STL SHA-256 | `E9D69A537D7D3F776655271CE9D92218721E8EF7B0DB676054683EE0268D3BCE` |
| 保护基线 PRT | SHA-256 前后均为 `769EC09A45A0D0BB8750CB1D66372EA5CAF3F2EBAFB9F66DC38F81EAE192A177` |

理论体积约 `65449.84695 mm³`、理论表面积约 `7853.98163 mm²`；Creo 回读与理论值的差异来自网格近似，均在项目容差内。通过判定不只依赖理论值，还依赖再生、失败特征、包围盒、质量属性、保存和文件证据。

性能验证：1 次预热不计入；6 次稳定运行结果一致。关闭非必要预览后中位数由 `940.708 ms` 降至 `451.460 ms`。

边界：该模型是 STL 导入零件，不是原生草绘/旋转球特征。`diameter.mm` 尚未驱动源码；孔、Pattern、Relations、UDF 和复杂拓扑未由此案例验证。

## 2. MTRN4062 自动重设计：分层说明

一组 Java 生成 STL → J-Link 导入的 L1/L2 PRT 曾真实完成再生、失败特征检查、参数回读、质量属性和保存：

- 固定端口中心：`(4.5,3.5)`、`(4.5,12.5)`、`(22.5,3.5) mm`。
- L1 厚度 `0.050 mm`，L2 厚度 `0.035 mm`。
- L1/L2 完整再生 PASS、失败特征 0、保存后 modified=false。
- 运行分段：会话 `11.133 ms`，几何生成 `102.621 ms`，L1 `511.933 ms`，L2 `6577.245 ms`，总计 `7370.647 ms`。

后续改善后的最终 STL/DXF 又通过严格离线检查：单位 mm、闭合/流形、无退化面、L2 投影在 L1 内。但这批改善后的中性文件没有再次导入新的 Creo PRT，因此不能合并声称“最终后处理 STL 已重新在 Creo 验证”。

## 3. 居中主干版本：仅中性几何通过

参数化配置生成的 centered-trunk DXF/STL/SVG 通过：

- 设计包络 `27 × 16 mm`。
- 水平主干后接 `-45°` 斜线到右下出口。
- L1 `0.050 mm`、L2 `0.035 mm`。
- L2 默认 6 条纹路，宽 `0.080 mm`、中心距 `0.10 mm`、长度 `0.40 mm`、角度 `35°`。
- `L2_PROJECTED_INSIDE_L1=PASS`、DXF/STL mm 交叉检查 PASS、STL 非退化/流形 PASS。
- 报告明确写入 `CREO_EVIDENCE=SKIPPED_NEUTRAL_ONLY`。

因此它可作为可编辑的配置驱动几何参考，但还没有 Creo PRT 端到端证据。

## 4. 当前“打印”L1：完整通过

保护源 SHA-256：`C4D1A4C635B75ADC4D5B0F9F95E0F7B9B1B6CADF88539D4986374B71A41661D1`，前后未变。

工作副本结果：

- 单位：mm，`millimeter Kilogram Sec (mmKs)`。
- 拉伸特征 ID `404`；稳定深度尺寸符号 `d411=0.050000000 mm`。
- 几何厚度回读 `0.050000000 mm`。
- XY 包围 `20.000000000 × 13.186557697 mm`。
- 保留参考曲线特征 ID `40`；旧示例曲线 ID `222`。
- 完整再生 PASS、失败特征 0、参数/尺寸回读 PASS。
- 工作副本 SHA-256：`BE53E8B68C21F0B0F83F809C52102D263F93AFE4CB0553AA5DD256BE6C75618B`。

真实关键手法：原模型数值维度最初处于英寸主单位语境；工作副本使用 `SetPrincipalUnits` 与 `UNITCONVERT_SAME_DIMS` 转为 mm，再设置 `d411`、再生、回读并保存。不得按第一个维度猜测深度。

## 5. 当前“打印2”L2：尚未最终验收

用户明确还要增加内部细节。当前副本：

- SHA-256 `B9281D526486396E3A823DD047E641223CD6543999DC8E0DB01C73B8C17BEA46`。
- 仍继承一个 L1 拉伸实体并含两条曲线，不满足“只留空白 L2 轮廓 Sketch”的严格目标。
- 参考 DXF 为 mm、1 条闭合 LWPOLYLINE、178 顶点；XY 边界 `X=3.5..23.5`、`Y=2.5..13.5 mm`，与 L1 共用坐标源。
- 目标 L2 厚度 `0.035 mm`，最终再生和回读尚未运行。

正确标签：`NOT_READY_USER_DETAILS_PENDING` / `DELIVERED_FOR_CONTINUED_DESIGN`。用户完成叶片/导流柱后才运行最终拉伸、再生、失败特征、厚度、孔位、投影和保存验收。

## 6. 不应重复的尝试

- J-Link 冷启动回调中无活动模型上下文调用 `ExecuteFeatureOps`：`XToolkitBadContext`。
- 同一回调直接查找未确认模板：`XToolkitNotFound`。
- 反复冷启动等待上下文：触发 `ORDER_DEBUG` trail 循环，浪费时间和磁盘。
- 把 DXF Import Feature 或 STL 导入特征声称为原生、逐尺寸可编辑 Sketch。
- 因球体成功就推断复杂 L2、Pattern、UDF 或 Object TOOLKIT 授权已验证。

