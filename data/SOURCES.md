# TUJI 中国行政区地图数据：来源、许可与范围

核验日期：2026-10-27。以下数据可追溯、可离线再生成，但本应用没有审图号，不声称是官方标准地图或最新法定行政区数据。

## 行政多边形

- 发布者：UN OCHA Regional Office for Asia and the Pacific，Humanitarian Data Exchange。
- 原数据：[China - Subnational Administrative Boundaries (COD-AB)](https://data.humdata.org/dataset/cod-ab-chn)；[机器可读元数据](https://data.humdata.org/api/3/action/package_show?id=cod-ab-chn)。
- 文件：chn_adm_ocha_2020_SHP.zip，分发年份 2020；源机构元数据说明 2024-10 复核，真实边界成立日期未知，原始来源为 unofficial publicly available sources，不能把复核年份当作边界年份。
- 许可：[Creative Commons Attribution 3.0 IGO](https://creativecommons.org/licenses/by/3.0/igo/legalcode)。保留 OCHA、数据集、许可及修改说明。无源机构背书。
- 坐标系：源 .prj 指明 GCS_WGS_1984 / EPSG:4326。未使用 GCJ-02 数据，也未对用户照片坐标施加偏移。显示采用 MapLibre Web Mercator，存储始终为 WGS84 [经度,纬度]。
- 原始 361 个 ADM2 要素，含不同实际层级；不是“361 个地级市”。按省名与中文名称匹配行政代码，保留原 PCODE。

## 实际覆盖

| 类型 | 数量 | 能力 |
|---|---:|---|
| 地级市、自治州、地区、盟等 | 332 | 独立真实区域填色 |
| 北京、上海、天津、重庆 | 4 | 直辖市整体填色 |
| 省直管县级补充单元 | 20 | 明确标明县级身份，单独填色 |
| 台湾、香港、澳门 | 3 | 保留省级区域轮廓，不编造地级数据 |
| 三沙市 | 1 | 源面不覆盖完整岛礁，仅保留原几何，禁用行政区域点亮 |

合并历史莱芜后共有 360 个几何要素、34 个省级父区域、356 个可交互单元。其中 **336 个为地级及直辖市单元，20 个为额外的县级单元**。逐区代码、名称和类型见 [china-coverage.json](china-coverage.json)。不把省直管分组代码 4190/4290/4690/6590 当作实际城市。

源数据中的莱芜与济南按 2019 国务院区划调整合并：[济南市政府说明](https://www.jinan.gov.cn/col81205/art/2019/art_81205_4884893.html)。其他更名、个别源中文错字仅校正标签与代码，不声称相应边界已经更新。原始属性与几何快照保存在 scripts/source-data/china-ocha/regions-source.geojson，转换过程见 scripts/build-china.mjs。

## 缺失和限制（不能省略）

1. 原始几何最低纬度约 18.16°N，缺少完整南海诸岛、曾母暗沙等岛礁与海域界线；钓鱼岛及附属岛屿的完整表示也未经可靠数据核验。因此当前不能声称完成中国完整疆域展示或符合公开地图表示全部要求。
2. 港澳台只提供同源省级轮廓；不制作虚构地级单元。三沙缺少可靠完整面，禁用区域填色，但仍可记录真实坐标旅行记忆。
3. 历史底图不能代表 2026 年所有县级行政调整。新设新疆直管县级市等可能落入旧上级区域，不能将本应用作法定行政归属查询。
4. 源机构称尚无 COD-EM edge-matched 版本；本项目从同一组下级多边形构造共享拓扑，确保显示中的省、市线由相同弧段派生，但没有把这说成源数据测绘误差已被修复。
5. 全国视角几何经过约 100m 简化和 100000 级量化，保留多块区域与岛屿；不适合地籍、导航或高精度测量。

已调查替代来源：上一版 geoBoundaries CHN ADM1 同样缺少上述完整岛礁，不能解决问题；DataV/高德衍生下载包未找到适合本次独立再分发的明确授权链，因此没有将开源程序许可证误当作其地理数据授权。没有手绘国界或用圆点替代缺失岛礁。

中国公开地图规范参考：[自然资规〔2023〕2号，政府部门发布](https://ghzyj.gz.gov.cn/zwgk/newzcfg/ywly/content/post_9466245.html)、[自然资源部标准地图服务系统](https://bzdt.tianditu.gov.cn/)。当前部署保留原有所有者私有访问；开放传播前仍需补充可靠完整疆域数据并核对适用审核要求，不能沿用其他地图的审图号。

## 行政代码表

[modood/Administrative-divisions-of-China](https://github.com/modood/Administrative-divisions-of-China)，版本 2824502ba4a6875bceafee81f926789635db1d00，仓库许可 WTFPL；原许可副本已保存。该表只用于名称与六位行政代码映射，不提供几何。名称匹配同时核对省级父代码；找不到的条目会阻止构建，而不是随意分配代码。

## 处理、性能与复现

原始 ZIP SHA256、下载地址、版本和操作记录见 scripts/source-data/china-ocha/provenance.json。`npm run data:build` 从已提交的原始 GeoJSON 快照离线重建。`npm run data:fetch` 可重新下载并校验原 ZIP；若哈希改变则停止，避免静默升级。

Mapshaper 构造共享拓扑、保形简化、量化；TopoJSON 约 634 KB。客户端一次加载这份全国概览，不逐城市请求远程 API。省界为不同 provinceCode 之间的共享弧与外边界，市界为相同 provinceCode 内部共享弧；相邻边界不会分别简化产生缝隙。标注点由最大多边形内部点计算，不使用落到面外的算术中心。
