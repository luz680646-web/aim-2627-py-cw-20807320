# AIM 2627 Python Coursework —— 哨兵 Sentry 控制模块

> **全部题目、规范、评分、提交见 [题面.pdf](题面.pdf)。** 本 README 只讲怎么把环境跑起来；没在这里出现的规格细节，一律以题面为准。

## 1. 环境要求

- Python 3.8+，仅标准库（不允许第三方运行时依赖）；
- 开发工具只需 `pytest`（测试）与 `autopep8`（风格，CI 会检查）；
- VS Code 打开仓库会推荐安装 `ms-python.autopep8` 插件（`.vscode/extensions.json`），保存即格式化即可过风格检查。

## 2. 快速开始

```bash
# 1. 用 GitHub 的 Use this template 创建你自己的仓库，然后 clone
git clone https://github.com/<你的用户名>/<你的仓库>.git
cd <你的仓库>   # 直接在 main 分支上开发

# 创建虚拟环境

python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

# 2. 装依赖
python -m pip install pytest autopep8

# 3. 启用 AI 会话归档钩子（课程要求，见下方第 3 节）
python -m pip install 'agent-session-commit[pre-commit]==0.1.3' -i https://pypi.org/simple
agent-session-commit install --pre-commit   # 交互选择你的 AI 助手与会话目录

# 4. 跑测试（刚到手：全部 skip，CI 是绿的）
python -m pytest

# 5. 看演示
python main.py

# 6. 打开 题面.pdf 读题，开始实现 src/main/__init__.py 里的 TODO
```

## 3. AI 会话归档（pre-commit）

本课程允许使用 AI，提交的 commit 需要携带 AI 会话归档作为透明化记录：每次 `git commit` 后，钩子会把新增会话自动 amend 进同一个提交（`.agent-sessions/bundles/`），不产生额外的归档提交。支持 Claude Code、OpenAI Codex CLI、GitHub Copilot CLI、Qoder、ZCode、Trae、Tencent CodeBuddy 等（完整名单见 [AgentLedger](https://github.com/Gentle-Lijie/AgentLedger)）。

- 配置是仓库本地的：每个 clone 运行一次 `agent-session-commit install --pre-commit`，方向键选择 agent、确认其会话目录即可；
- 不想用 TUI 可手动配置：`git config --local agent-session.agent claude`、`git config --local agent-session.source "<会话目录>"`，然后 `python -m pip install 'pre-commit>=3.2.0' && pre-commit install`；
- 归档是普通 Git 内容且会推送到公开仓库——不要在 AI 会话里粘贴令牌等敏感信息；
- 换了 agent 或目录就重跑一次安装命令；卸载：从 `.pre-commit-config.yaml` 移除该条目后重跑 `pre-commit install`。

## 4. 本地开发循环

- **写代码**：全部作业在 `src/main/__init__.py`，按题面各题规范补全每个标有 TODO 的函数；注释里标注了对应的题面主题，推荐顺序 Q1 → Q6。
- **跑测试**：`python -m pytest` —— 可见测试是规格书的一部分，未实现的函数自动 skip，实现一个、对应测试亮一个。本地全绿 ≠ 满分（见题面）。
- **看演示**：`python main.py`（等价于 `PYTHONPATH=src python -m main`），随实现进度逐段点亮，不进测试。
- **Q6 自测**：`python tools/run_seeds.py --q6`（200 张固定地图统计），单 seed 渲染 `python tools/run_seeds.py --q6 --seed <N> --render`，Bonus 模式 `python tools/run_seeds.py --bonus`。

## 5. 仓库结构（哪些能改）

| 路径 | 说明 | 能否修改 |
|---|---|---|
| `src/main/__init__.py` | 你的全部作业（TODO 所在） | ✅ |
| `README.md` | 仅末尾两个"你来写"小节 | ✅ |
| `题面.pdf` | 题面（唯一规格说明） | ❌ 勿改 |
| `src/main/legacy_patrol.py` | Q7 模块（与主体同步发布，修复其缺陷） | Q7 时 ✅ |
| `.pre-commit-config.yaml` | AI 会话归档钩子配置 | ❌ 勿改 |
| `src/tests/`、`tools/`、`.github/`、`conftest.py`、`pytest.ini`、`main.py` | 测试与基础设施 | ❌ 勿改 |

CI 只允许修改 `src/main/**`、`README.md` 与 `.agent-sessions/**`（AI 会话归档）——其余文件改了直接红；autopep8 `--diff` 非空即败。提交方式（push、问卷、commit 粒度）见题面"提交与验收"一节。

## 6. 我的设计说明（持续更新）

2026-10-07：在 AI 协助下配置项目专用 `.venv`，开始 Q1。
`hp_ratio` 先检查满血量，再用整数运算计算百分比，最后限制到 0–100。
`status_report` 复用该函数，并按题面指定的列宽生成字符串。

尚未确认的规则：电量暂按 >=60 为 OK、>=20 为 WARNING，否则 LOW；
非正 max_hp 暂返回 0，百分比暂向下取整。这些是当前实现假设，
并非题面完整公开的规则。通过可见测试不代表这些边界能通过隐藏测试。

## 7. 调试与学习记录（持续更新）

- Q1：区分 return（交给调用者的结果）和 print（显示在屏幕上）。
- 初始 Q7 含循环缺陷，环境验证先单独运行 `src/tests/test_main.py`，
  避免尚未修复的旧代码导致全套测试不终止。
- 本阶段尚未完成 Q2–Q7、个人 GitHub 仓库配置和 AI 会话归档钩子启用。

### 当前 AI 记录方式

本次协作使用 Codex 桌面应用。聊天原始 cwd 为仓库的父目录，因此
AgentLedger 0.1.3 的 codex 扫描模式无法直接匹配。现采用其 custom
导出模式：每次提交前由本机脚本刷新这一条考核聊天，导出首行明确
说明目标仓库与原始 cwd，后续内容原样保留，不修改原始会话文件。
导出目录为 `.agent-sessions/source/`（不直接提交）；课程钩子负责
把归档合并到提交。该本机导出步骤不随 clone 自动安装。

已在临时仓库验证归档生成、原始内容保留、提交数仍为一次。
正式仓库首次归档仍需完成实际 commit 后确认，尚未推送。

### Q2 日志分析（2026-10-09）

已实现 analyze_damage_log。用 _damage_events 先验证并解析整行，
确认有效后才更新统计；因此 F:10,R:bad 整行跳过，不会部分入账。
JSON 复用标准库 json，伤害要求严格正整数（拒绝布尔值、浮点数及数字字符串）。
有效 JSON 行才登记 id，重复 id 只计首次有效事件；无 id 行独立计数。

题面未明确的选择：传感器每个合法段算一次事件，avg 按事件平均；
累计伤害并列时按 front、left、right 顺序选择；允许字段两侧空白，
传感器数字采用 ASCII 十进制数字；可哈希 JSON id 按 Python 相等性去重，
数组/对象 id 视为非法。以上策略不代表已知隐藏测试规则。

验证：Q2 五个公开测试通过，Q1 四个测试回归通过；额外检查十组边界，
覆盖整行拒绝、非法类型、严格正整数、无效事件不占 id、重复及空输入等。
后续 Q3–Q7 尚未完成，完整 CI 不应视为已通过。

### Q3 仿真载体（2026-10-09）

已实现位置 setter、前进、左右转向四个方法。复用已有 Facing.delta、
is_blocked 和 _clamp_cell，不更改构造及只读属性。setter 先检查容器和
长度，再规范化坐标并拒绝障碍，全部成功后才赋值，失败保留原位置。
前进先检查电量，再计算下一格，通过障碍/边界判断决定移动或累计碰撞。
转向用明确的方向映射，位置不变且不耗电。

边界选择：setter 按模板 _clamp_cell 将元素转 int、将越界坐标夹回地图；
转换异常沿用 int，障碍位置抛 ValueError。有电时每次前进尝试耗电 1，
包括碰撞；电量 <=0 时不移动、不继续扣电、不增加碰撞。无电仍可原地转向。
部分边界未在题面完整披露，这些是与骨架一致的实现选择。

验证：Q3 五项公开测试通过，Q1–Q3 合计 14 项通过；补充检查四方向、
四边界、断电、连续转向、setter 规范化和失败回滚，演示运行正常。
Q4–Q7 尚未完成。

### Q4 单步贪心导航（2026-10-09）

已实现 next_step_toward。先计算与目标的横纵坐标差，按绝对差大小
决定尝试顺序，再检查相邻格非障碍且曼哈顿距离严格减小。
朝远离目标方向移动必然增距，因此只需检查每个轴朝目标的一侧。
无合法候选（包括已经到达目标）返回当前朝向。函数只选方向，不移动、
不修改障碍，也不擅自添加地图边界判断；移动由 Q3 负责。

两轴差相等时暂定横向优先：题面和可见测试没有完全限定这一行为。
凹形障碍可能导致无减距方向，保持朝向是 Q4 的规定，脱困留给 Q6。

验证：Q4 五项公开测试通过，Q1–Q4 合计 19 项通过；额外枚举 40000
组位置、目标、相邻障碍组合和当前方向，对照四邻域检查候选合法性、
大差轴优先及无候选回退，另检查平局和负坐标。

### Q5 哨兵决策机（2026-10-09）

已实现 decide，先检查输入契约，再按 R1–R7 顺序首条命中即返回。
复用 hp_ratio；R4/R6 共用 _engage_action，避免两处交火逻辑不一致。
本函数不修改 sensor，也不直接移动机器人。heat 保留接口，但题面规则
没有热量条件，因此不添加额外禁射逻辑。

未公开规则采用的假设：撤退恢复阈值 >=60%；持续丢失为末尾连续三帧
不可见。字段非法时数值尝试 int 转换，失败血量/满血为 0（沿 Q1 规则
得到 0%），非法或负敌距视为无穷远，未知机型按 INFANTRY。
非 list/tuple 的敌检历史保守变为单帧 False；合法容器元素按真值解释。
空列表/元组、长度超过 6、缺字段或非法 state 均抛 ValueError。
这些归一细节为当前选择，不能保证与未公开的隐藏测试完全相同。

验证：Q5 六项公开测试通过，Q1–Q5 共 25 项通过；补充检查低血量优先、
返航单帧、近远距离、两机型、连续确认、恢复/丢失阈值和非法输入；
枚举 630 组状态/帧历史，确认输出类型、输入不变及 heat 不改变结果。
Q6、Q7 与 Bonus 尚未完成。

### Q6 巡逻任务（2026-10-09）

已实现 run_patrol 和 report_to_json。每轮读取位置，调用 Q4 选择贪心
方向；发现方向受阻、不减距或重复位置时启动 _escape_route，在有限
地图中用 BFS 搜索到目标的方向队列，并执行该绕行路线。正常路段仍用
Q4，未把全部导航改成全局最短路。采用搜索脱困是自己的策略选择，
不是题目参考沿墙算法；不依赖工具内部算法、地图 seed 或测试数据。
_face_direction 通过 Q3 的转向方法对齐方向，再调用 move_forward。
BFS 用队列逐层扩展、用父节点记录还原路径；最坏时间和空间均 O(宽×高)。

统计口径：steps 计前进尝试，转向不计步（与工具参考循环一致）；
visited_count 包含起点和最终格且去重；collisions 计本次调用新增碰撞。
不重置传入载体的电量，到达目标/步数用完/电量耗尽即结束。
对不可达地图额外允许搜索确认无路后提前失败，不继续空转。
访问计数及已有碰撞处理等未明确边界按上述口径实现。
JSON 使用 sort_keys=True、紧凑分隔符、ensure_ascii=False 保持确定性。

验证：src/tests/test_main.py 为 27 passed、1 skipped（跳过 Bonus），
未运行尚有循环缺陷的 Q7。官方 seed 1–200：成功率 100%、平均碰撞 0、
平均步数比约 1.03，三项均达标；额外 seed 201–400 同样全部成功，
平均碰撞 0、步数比 1.0300。终止条件、绕行、统计、JSON 顺序检查通过。
注意 tools/run_seeds.py --render 渲染的是工具参考策略，非本实现轨迹。
Q7 与 Bonus 尚未完成，不能称整份考核已完成。
