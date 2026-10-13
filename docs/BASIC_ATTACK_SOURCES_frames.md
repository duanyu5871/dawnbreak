# 普攻帧数据 / 判定范围 / 硬直数值查证（issue #3）

> 目标：找出 DNF 普攻、天击、空斩打的出手帧、判定框 / 攻击距离(px)、硬直的**硬数字**。只采信抓取到的页面正文；`search_web` 的 AI 摘要一律不采信（它们自己也承认"官方未公开 px 数值"）。
> 结论先说：**没有找到任何可对应到普攻 / 天击 / 空斩打的硬数字，src 零改动。** 下面是查过的来源和各自能给什么。

## 一、查过且能引用的页面文本

### 1.1 namu：경직（硬直）——只有定性描述，无数字
**来源**：<https://namu.wiki/w/던전앤파이터/경직>（curl 抓取正文）
> 던전앤파이터의 경직 시스템은 기본적으로 무기나 스킬에 따라 피격자에게 주는 경직 시간이 달라지며, 피격자의 히트리커버리란 능력치를 통해 경직 시간을 줄일 수 있다.
> 경직도가 올라가면 평타와 일부 스킬들의 경직 시간이 늘어난다.
> 경직 판정은 베기 판정과 타격 판정으로 나누어져 있다. 베기 판정은 몬스터에게 주는 경직이 짧지만 그 대신 역경직이 없으며, 타격 판정은 무기 별로 다르긴 하나 경직과 역경직이 있다.
> 역경직이란 … 공격한 유저의 공격 모션을 유지하는 시간이 길어지는 현상 … 공격속도로 약간 줄일 수 있다.
> 타격 판정 무기는 역경직이 긴 편이므로 …

对本作的含义（与现有实现一致，不改）：
- 硬直由"武器 / 技能"决定，普攻硬直随武器变；`WTYPES` 里每武器 `stun` 的设计方向正确，但**页面没有任何每武器的毫秒 / 帧数**。
- 斩击(베기)判定硬直短、无逆硬直；打击(타격)判定有硬直与逆硬直（打击类武器逆硬直长）。`COMBAT.md` 里 `stagger` / `hardness` 修正（`clamp(1+(攻−守)/250)`）对应"경직도 / 히트리커버리"，同样没有数字来源，保持自订。
- `COMBAT` 的"连击逆硬直"：本作没有；若以后要做，可按"打击判定武器（钝器 / 手套 / 臂铠）攻击后自身硬直更长，斩击判定无"实现。本轮只记录，**不实现**（无数值可抄）。

### 1.2 PVF 教程仓库：.ani 的 `[ATTACK BOX]` 写法——格式说明，不是普攻数据
**来源**：<https://github.com/springandme/dnf-pvf-tutorial>（`pvfCourse/【12】高级教程/ANI/11580_ANI基础知识点.json`、`11415_ANI写法解析.json`、`【04】技能修改/12075_气功分身攻击范围判定….json`）
> [ATTACK BOX]//攻击框 X1 Y1 Z1 X2 Y2 Z2（11580，仅给出格式）
> [ATTACK BOX]：（攻击框架）-11 -20 -15 20 40 （数值待测试）（11415，示例，作者标注"待测试"，且是特效 ani，不是普攻）
> 6个体积坐标表示范围 -500 -500 -500 1000 1000 1000（大概全屏）（12075，气功分身 ani 的人为改大示例）
- 说明：攻击框是每帧一个 6 数体积（X1 Y1 Z1 X2 Y2 Z2），`[DELAY]` 以毫秒计（`1s=1000ms`）。这解释了为什么**帧时机与攻击框只存在于每个 ani 里**；这些仓库教的是格式，没有附带 `Atk*.ani` 实际数值。

### 1.3 DFO fandom（dfo.fandom.com，MediaWiki `api.php?action=parse` 原文）
**来源**：<https://dfo.fandom.com/api.php?action=parse&page=Mage&prop=wikitext&format=json> 以及 `Launcher`、`Slayer`、`Crusader_Skills`
- 全是 2009–2010 年的短条目（1–3KB）。唯一的 px 数字是牧师增益"within 900px"（`Crusader_Skills`），与普攻无关。
- `Launcher` 条目原文："A launcher's true strenghs come from its skills, as they usually dont use thier normal attacks."（只是定性，印证神枪手普攻不是主要输出）。
- 搜索 `hitbox / pixel / frames / hitstun / stagger` 在该 wiki **零命中**。

### 1.4 GitHub 仓库搜索（`api.github.com/search/repositories`，q=`dnf pvf` 等）
命中的都是工具 / 文档类：`Zageku/DNF_pvf_python`、`flwmxd/DNF-Porting`、`heihuo000/DNF-PVF-file-Reference`（ANI/ATK/CHR 格式知识库，示例文件为自拟示例）、`LHCGreg/DFOToolBox`（NPK 工具）、`springandme/dnf-pvf-tutorial`（见 1.2）。**没有仓库提交官方 `Atk*.ani` / 职业 `.chr` 数据**（版权数据，公开仓库里没有），`search/code` 需要登录，无法做代码级检索。

## 二、查了但没有可用内容

| 来源 | 结果 |
|---|---|
| <https://namu.wiki/w/던전앤파이터/시스템>、`/용어`、`/무기`、`/아이템/무기` | 正文无 `판정 범위 / 프레임 / px / 사거리 / 공격 범위` 等数字行（抓下来的页面里一条都没有）；`/무기` 只有"공격속도 증가…等选项需装备该武器才生效" |
| dfo-world.com 武器页 | 301 跳转到 Cloudflare 内容页，抓不到正文 |
| `search_web`：`DFO basic attack frame data`、`DNF 空斩打 天击 攻击范围 像素` | 摘要自称"官方不公开 px 数值、需靠视频 / 解包实测"，**不采信、也无可引用页面** |
| `search_web`：`dnf pvf skl ani attack box` | 只返回工具仓库（见 1.4） |

## 三、对 src 的处理

**零改动。** 没有任何"有来源且与 src 冲突"的数值：
- 天击 / 空斩打 `p_launcher` 的出手时刻、判定框：**未找到**。
- 各武器普攻 reach(px)：**未找到**。（既有 `BASIC_ATTACK_SOURCES_sword_fighter.md` 里 namu 给的只是排序，已落实在 `SWORD_FEEL.reach`。）
- 每武器 / 每段硬直具体数值：**未找到**（只有 1.1 的定性结论）。

## 四、仍未找到（明确列出）

1. 任何职业普攻每段的出手帧 / 有效帧 / 收招帧（ms）。
2. 普攻 / 天击 / 空斩打的 `[ATTACK BOX]` 实际坐标（X/Y/Z，px）。
3. 每武器的硬直时长（ms 或帧），斩击 vs 打击判定的具体硬直差值。
4. 天击、空斩打的浮空高度 / 击退距离数值。
5. namu 里的 `판정 범위 / 공격 범위 px` 文字：目前抓到的上述页面都没有；职业子页 `/스킬` 之前几轮已查过（见其他 `BASIC_ATTACK_SOURCES_*.md`）。

可行的后续途径（不在本任务范围内）：逐帧视频测量（B 站 / YouTube 的"判定框显示"视频）；dnf.gamedb / dundefine 的技能表是**技能**倍率与冷却，也不含普攻 px，需要时单独抓取验证。
