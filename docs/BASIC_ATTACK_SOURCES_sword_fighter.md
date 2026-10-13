# 鬼剑士 / 格斗家普攻：联网查证记录（issue #3）

> 结论先说：**能真正取到页面正文的来源里，没有任何关于"普攻段数 / 动作 / 武器距离·硬直数值 / 阿修罗普攻伤害类型"的记载。** 所以不再凭猜测改动；首轮提交 `e347a8fd` 里没有来源的改动已回退，只保留有出处的部分。

## 一、取到了正文的来源（可引用）

### 1. DFO 英文 wiki · Slayer 页
- URL：<https://dfo.fandom.com/wiki/Slayer>（API：`https://dfo.fandom.com/api.php?action=query&prop=revisions&rvprop=content&titles=Slayer`）
- 原文：
  - "Uses **Short Swords**, **Great Swords**, **Katanas**, **Bludgeons** and (in the case of the **Blade Master** sub-class) **Lightsabres**."
  - "**Blade Master:** … He wields the powerful Beam Sword and is able to **attack faster than any of the other Slayer subclasses**."
  - "The **basic three-step attack combo** is enough for most of the monsters you'll face…"
  - "the speed of their attacks isn't comparable to that of a Fighter or Gunner"
- 能说明：①武器五类、光剑只有剑魂（Blade Master）用；②光剑 / 剑魂攻速最快；③基础鬼剑士普攻是 3 段（本作 atk1-3 + 里·鬼剑术后续，和这一点一致）。
- **不能说明**：太刀 / 短剑 / 钝器 / 巨剑之间的攻速排序，也没有任何"攻击距离 / 僵直"的描述。

### 2. DFO 英文 wiki · Asura 页
- URL：<https://dfo.fandom.com/wiki/Asura>
- 原文："Though blind, an Asura can master **magical attacks** and defensive techniques, and sharpen skills that utilize **waves** and long-range weapons."
- 能说明：阿修罗偏魔法、波动。**不能说明**普攻本身是物理 / 魔法 / 独立。

### 3. DFO 英文 wiki · Fighter / Striker / Nen Master / Berserker 页
- URL：<https://dfo.fandom.com/wiki/Fighter>、<https://dfo.fandom.com/wiki/Striker>
- 原文（Striker）："Her weapons of choice are **Boxing Gloves** … **strike faster than any other subclass of fighter**."
- 原文（Fighter，机翻残句）：武器为长手套 / 指虎（knuckles）/ 爪（claws）/ 拳击手套；Brawler："uses blades, poisons and other items"。
- 不含任何普攻段数。

### 4. 本仓库已有整理：`docs/SKILLS_OFFICIAL_sword.md`
- 凯贾：普攻变 4 连**暗属性魔法**（第 395 行）；波动刻印：普攻附带波动剑气（第 486 行）；鬼人化：专属 4 段普攻 + 2 段跑攻（第 595 行）；狂暴二刀流 / 里·鬼剑术：随武器变化的第二套普攻（第 62、342 行）。
- 这些是之前整理的二手资料，本次未能用网页独立复核，沿用现有实现不动。

## 二、没取到正文的来源（如实记录）

| 来源 | 结果 |
|---|---|
| namu.wiki 던전앤파이터 / 귀검사 / 아수라（多种 URL，含百分号编码） | 全部 HTTP 404 |
| dnf.fandom.com（旧站）/ dnf.fandom.com/api.php | 403；API 能通但页面均不存在 |
| baike.baidu.com 阿修罗 | 403 |
| dnf.qq.com 官网专题页 | 404 |
| wiki.biligame.com/dnf/鬼剑士、阿修罗 | 能取到，但是前端渲染外壳（只有导航 / 站点链接），无词条正文；该站 api.php 返回空 |
| zh.moegirl.org.cn 地下城与勇士 | 只有总览，无普攻 / 武器内容；api 未授权 |
| `search_web` 综述 | 返回 AI 生成的摘要，**自相矛盾**（同一问题里给出"光剑极快 / 太刀快 / 短剑普通"的排序，但对钝器、巨剑只给"缓慢"，对阿修罗普攻、段数完全没有来源），引用是 vertexaisearch 跳转链接、无法核对原文。**按"不采信未验证内容"处理，不据此改代码。** |

因此下列问题**网上没有查到可靠答案**，代码保持原状：

- 阿修罗普攻伤害类型（物理 / 魔法 / 独立）→ 保持**物理**（首轮改成独立的改动已回退）。
- 狂战 / 剑影 / 剑魂 / 鬼泣普攻段数与动作 → 保持现有实现（见上第 4 点）。
- 格斗家 散打 / 街霸 / 柔道 / 气功师普攻段数（含"独门武功 5 段""气功虎爪 5 段"）→ 无来源，不改（格斗家文件本次未动）。
- 武器对普攻攻击距离 / 硬直的具体倍率 → 没有来源。`SWORD_FEEL` 的 reach / stun 倍率（早于首轮存在）保留，但已在代码注释里标明"平衡取值、无网页来源"；攻速排序（光剑最快）有上面 DFO wiki 佐证。

## 三、对代码的处理

- 回退 `e347a8fd` 中无来源的改动：阿修罗 `indep` 与紫色刀光、跑攻补斜斩、狂战跑攻 / 跳攻红斩、剑影幻鬼补刀、里·鬼剑术短剑弧度。
- 保留 `e347a8fd` 中凯贾 jatk 暗属性魔法（`SKILLS_OFFICIAL_sword.md` 395 行有记载）。
- 不新增 / 不改格斗家文件。

## 四、如果要继续做

需要能打开的一手来源，任选其一：①让人把 namu.wiki 对应词条正文（귀검사 / 격투가 / 아수라 的"기본 공격"小节）贴进来；②本地游戏客户端的技能 / 动作脚本（`.skl`、`.ani`）；③B 站 / NGA 的实测视频帧数。拿到后再按这份文档的格式补充并改代码。
