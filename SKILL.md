# 🎧 AI Music Prompt Generator — 音乐小白版

帮助**不懂乐理、不会写 prompt** 的人，用自然语言描述想法，自动生成适合 Suno / Udio 等 AI 音乐工具的优质 prompt。

---

## 核心设计理念

这个 skill 是为「音乐小白」设计的。使用者不需要知道 BPM、调性、和弦是什么——**你只需描述你想要的感受、参考的歌、或者脑海里的画面，LLM 来帮你想办法。**

核心原则：
- **用自然语言提问，不要用术语轰炸用户。** 关于速度就问「快还是慢」，不要求写 BPM 数字。
- **用户描述感受，LLM 翻译成技术细节。** 「听起来像在雨夜的城市开车」→ LLM 帮你补全合成器、氛围音、节奏风格。
- **每次输出都提供多个选项让用户挑，不要替 TA 做唯一决定。** 给出 3-4 个不同方向，附带「这适合什么场景」的说明，让小白也能做出选择。
- **技术细节全部隐藏在后台。** 不需要也不要在主流程中展示 BPM、调性等数据——用户只需要看到「一段能复制粘贴到 Suno 的自然语言 prompt」。
- **用类比和熟悉的歌曲做参照。** 不要说「trap beat with 808 bass」，说「像周杰伦《Mojito》那种轻快摇摆的感觉」。

---

## When to Use This Skill

Invoke whenever the user wants to:
- **生成音乐 prompt**：为 Suno / Udio / 其他 AI 音乐工具生成优质 prompt
- **参考一首歌做类似的音乐**：分析参考歌的特点，生成新的 prompt
- **改造一首歌的氛围**：把快歌改慢歌、悲伤改欢快、电子改原声
- **把模糊想法变成具体 prompt**：从「想要一首夏天海边的歌」变成可用的 prompt
- **批量生成多个变体**：从同一个想法衍生 3-4 个不同方向的 prompt

**触发短语（中英文均可）：** "帮我写一个 Suno prompt"、"生成 Udio prompt"、"参考这首歌"、"把这首歌改成xx风格"、"我想做一首……的歌"

---

## Architecture (LLM 内部处理流程，不展示给用户)

```
Input: 用户自然语言描述 / 参考歌名 / 模糊想法
       ↓
① 提问引导层    → 用自然语言反问，填补关键信息缺口（情绪、速度、用途）
       ↓
② 分析/推断层   → LLM 后台补全该首歌的风格、乐器、结构（不展示技术表格给用户）
       ↓
③ Prompt 生成器 → 生成 3-4 个不同方向的 prompt + 场景说明
       ↓
④ 输出         → 用户看到的是「直观选项 + 可复制的 prompt」，无技术术语
```

---

## Step 1 — 收集信息（用自然语言问）

收到用户的请求后，先判断信息是否足够。用户可能给出以下几种输入，不管哪一种都能处理：

### 用户可能输入的类型

| 输入类型 | 例子 | 处理方式 |
|----------|------|----------|
| **具体歌名** | "想要一首像周杰伦《晴天》的歌" | 基于 LLM 训练数据中的歌曲知识，分析风格后生成 prompt，同时展示你识别出的关键特点，让用户确认 |
| **模糊感受/场景** | "想要一首适合深夜开车的歌"、"海边日落的那种感觉" | 反问 1-2 个补充问题（快/慢？有人唱/纯音乐？），然后生成 3 个不同方向的 prompt |
| **具体但非术语的描述** | "悲伤的钢琴，有雨声，慢慢变强" | 直接生成 prompt，不需要反问 |
| **两个参考的融合** | "想要《晴天》的感觉，但改成电子风格" | 分别识别两首/两种风格的特点，做融合，生成 prompt |
| **歌词/文字** | "我写了段歌词，帮我配个合适的音乐风格" | 分析歌词的情绪和节奏，推荐 2-3 个匹配的风格方向，用户选定后生成 prompt |

### 关键原则：只问必要的问题

**不要一次问太多。** 如果用户输入信息不够清晰，最多问 2 个自然语言问题。比如：

> 用户：我想写一首歌
>
> 你：好！你希望它是快节奏的还是比较舒缓的？有人声唱歌还是纯音乐？

**如果用户信息已经很具体，跳过提问，直接生成。**

### 额外说明：关于音频文件

⚠️ **当前不支持直接上传音频文件（mp3/wav/flac 等格式不在上传支持范围内）。** 如果你手头有无法直接描述的音频，可以通过以下方式：
- 主动描述你听到的内容（歌词、整体感觉、哪里让你感动、是快是慢、用什么乐器）
- 告诉 LLM 这首歌的歌手和歌名让它检索
- 如果不太确定怎么描述，LLM 可以引导你回答几个简单的选择题

> 只要你提供你能描述的信息（哪怕只是「听起来很悲伤，副歌部分突然很大声」），LLM 都可以帮你填充技术细节。你不懂的部分不需要硬着头皮写——**LLM 就是帮你做这件事的。**

---

## Step 2 — 后台分析（LLM 在脑子里做的事，不展示原始数据给用户）

当用户提供了具体参考歌曲时（比如「像周杰伦的《晴天》」），LLM 在后台完成以下分析。**这些分析结果不要直接展示给用户，而是用自然语言总结关键发现。** 技术速查表见文末「进阶模式」附录。

### 2.1 后台分析清单（LLM 内部用）

分析参考歌曲在以下维度的特点，转化为自然语言描述：

- **整体感觉**：这首歌给人什么感受？适合什么时候听？
- **节奏快慢**：快 / 中速 / 慢 / 忽快忽慢
- **调子气质**：偏悲伤还是偏欢快？（大调 = 明亮欢快，小调 = 悲伤深沉）
- **主要乐器**：旋律靠什么？节奏靠什么？有没有特别抢镜的声音？
- **人声特点**：男声/女声？温柔/有力量？轻唱/喊？有没有特殊处理（像隔着一层雾的感觉）？
- **段落变化**：比如「前奏很安静→副歌突然爆发→最后慢慢淡出」
- **声音质感**：干净精致 / 粗糙有颗粒感 / 空旷像在大厅里 / 闷闷的像老唱片

### 2.2 分析结果展示格式（自然语言，不用表格）

给用户看的时候，用一段话总结，像这样：

> 我理解的《晴天》：
> 这是一首偏悲伤的吉他流行歌，节奏中等偏慢。周杰伦的男声比较柔软、音调偏高，咬字有点含糊，给人亲切随意的感觉。整首歌从前奏的吉他独奏开始，到副歌部分情绪爆发，再到最后慢慢安静下来。声音听起来干净自然，像现场弹唱。

### 2.3 如果用户只给了模糊感受

直接跳到 Step 4 生成 prompt。LLM 根据情绪关键词自行推断风格和乐器。

---

## Step 3 — 自然语言解读（桥梁段落）

把分析结果浓缩成一段一两句话的自然语言总结。**这一步是 LLM 在后台完成的，不单独展示**——直接嵌入到每个 prompt 选项的「为什么推荐这个风格」说明中。

核心思路：把后台的技术分析转化为用户能直观理解的语言。例如后台知道「BPM 80, F minor, 吉他 + 钢琴」→ 告诉用户「这是一个偏慢、有点悲伤的吉他钢琴伴奏」。

> 这个环节对 prompt 质量至关重要——它是在数据与提示词之间搭桥。但对于小白用户，不要在输出中单独列出一个「技术分析摘要」栏目。把分析结果融化在每个选项的说明文字里。

---

## Step 4 — 生成 Prompt 选项（多选，让用户挑）

### 输出结构

每次生成 **3-4 个不同方向的 prompt 选项**，每个选项包含：

1. **风格标签**（如「🎸 原声治愈」）— 一目了然的分类
2. **一句话描述** — 用类比或熟悉歌曲做参照，帮小白做选择
3. **适合场景** — 「适合什么时候听/用」
4. **可复制的 Prompt** — 一段可以贴到 Suno/Udio 中的自然语言 prompt

### 选项设计原则

- 四个选项之间要有明显的风格差异，不要让用户觉得「这俩不是差不多吗」
- 每个选项都有不同的「适合场景」，帮助不熟悉音乐的用户做出选择
- 如果用户输入的是具体参考歌，第一个选项保持接近原风格，后三个为变形
- Prompt 本身用完整的自然语言句子，不是标签堆砌

### 示例输出结构

```
### 🎸 选项一：原声治愈风
**适合你如果：** 喜欢原版的感觉，安静、走心
**适合场景：** 一个人静静听、深夜放松

📋 Prompt:
一首中慢速的华语流行 ballad，原声吉他主导，简单干净的钢琴点缀。
男声温柔略带鼻音，咬字自然不刻意。
从前奏吉他独奏开始，慢慢加入鼓和贝斯，副歌部分情绪上扬，最后轻轻淡出。
整体声音干净自然，像在小房间里弹唱，温暖亲切。
```

（其余三个选项依不同方向设定，比如电子改编版、纯钢琴版、女生翻唱版等）

### 4.1 ☀️ 主 Prompt（适配 Suno / 中文 AI 音乐工具）

Suno 和多数 AI 音乐工具喜欢**描写性的自然语言**，不需要标签堆砌。写 prompt 的结构：

```
一段完整的自然语言描述，包含：
- 整体风格和速度（不说 BPM，说「快」「中速」「慢」）
- 用什么乐器（具体说「钢琴」「吉他」「合成器」，不要说「乐器」）
- 情绪走向（从xx到xx）
- 人声是什么样的（男/女、温柔/有力量、有没有特殊效果）
- 听起来像什么场景（「像深夜下雨天一个人在窗边」）
- 一个让人记住的点（这首歌最特别的是什么）
```

### 4.2 🎵 详细版 Prompt（适配 Udio / 高级用户）

Udio 对细节更敏感。生成一个更详细的版本（但仍用自然语言，不是术语堆砌）：

```
比主 prompt 更详细，额外包含：
- 哪里安静、哪里爆发（段落怎么安排）
- 每件乐器分别在干什么
- 声音质感（干净/粗糙/空旷/闷/老唱片味）
- 参考哪个歌手/哪个时代的味道
```

### 4.3 🔄 变体 Prompt（3-4 个不同方向）

每一个变体保持原曲/原想法的 DNA，但在风格、速度、情绪、乐器上做转换。每个都要附带：

- 一个新的风格标签（如「🌧️ 雨天电子版」）
- 一段「适合你如果」和「适合场景」说明
- 转换说明（原歌的什么保留了？什么改变了？）
- 完整的 prompt

**变体类型参考（从中每次选 3-4 个合适的方向）：**
- 风格大变（比如流行→电子、hip-hop→摇滚）
- 速度翻转（快改慢、慢改快）
- 情绪翻转（悲伤改温暖、沉闷改热烈）
- 乐器替换（电子改原声乐器、全乐队改纯钢琴）
- 人声调整（男改女、独唱改合唱、有唱改纯音乐）
- 年代变换（80年代味、未来感、老唱片味）
- 场景重新定义（派对改失眠夜、公路片改咖啡店）

---

## Step 5 — 输出格式

### 5.1 默认输出（对话中直接给）

输出结构如下：

```
## 你选的风格：xxx

### 我对原曲的理解
（一段自然语言总结 LLM 对参考歌/用户想法的理解，让用户确认方向对不对）

---

### 🎸 选项一：[风格标签]
**这种感觉像……** [用熟悉的歌/场景类比]
**适合场景：** [什么时候听/用]
**和原版比：** [保留了xx，改变了xx]

📋 Prompt:
[完整自然语言 prompt，可直接复制到 Suno/Udio]

---

### 🔄 选项二：[风格标签]
……

### 🎹 选项三：[风格标签]
……

### 🌊 选项四：[风格标签]
……
```

### 5.2 用户明确要求详细数据时

只有当用户明确说「给我详细的技术分析」「BPM 是多少」等，才输出后台数据。输出格式见文末「进阶模式」附录。

---

## Prompt 质量自查（LLM 内部自检，不输出给用户）

生成每个 prompt 后自查：
- 写了「钢琴」而不是「乐器」、写了「电子鼓」而不是「节奏」？
- 有没有明确的安静→爆发、稀疏→饱满的情绪线？
- 前奏什么样？副歌怎么样？结尾怎么收？
- 男/女、温柔/有力量、有什么特殊效果？
- 这首歌最特别的地方是什么？
- 有没有自相矛盾：「安静的爆炸感」需要调和——安静=乐器少，爆炸=情绪满，可以组合
- 有没有太啰嗦？控制在中英文字符合理长度

---

## 进阶模式（Advanced Mode）

> ⚠️ 以下内容面向**有一定音乐知识的用户**或明确要求技术细节的对话。在日常小白模式中**不要主动使用**。只有当用户明确说「给我详细数据」「BPM 是多少」「用什么调」等，才展开对应章节。

### 完整分析框架

当需要技术数据时，按以下维度分析，并以表格直接呈现给有需求的用户：

**核心指标：** BPM、调性（大调=明亮欢快，小调=悲伤深沉）、拍号

**风格标签：** 一级流派、二级子风格、年代/制作风格（80年代味、现代、低保真等）

**情绪弧线：** 按前奏→主歌→预副歌→副歌→桥段→尾奏，逐段标注情绪词和强度

**乐器：** 旋律乐器、节奏组、贝斯、和声铺底、特效/氛围音

**人声：** 男女/音色（空气感/沙哑/顺滑）、音区、唱法（气息式/力量型/说唱/耳语）、效果处理

**制作质感：** 混音风格、立体声宽度、动态、混响空间、时代参考

### Suno 风格标签速查

| Category | Tags |
|----------|------|
| **Genre** | pop, rock, electronic, hip-hop, r&b, jazz, classical, folk, metal, punk, indie, lo-fi, ambient, synthwave, house, techno, drum and bass, trap, edm, cinematic, orchestral |
| **Mood** | emotional, energetic, dark, uplifting, melancholic, aggressive, calm, dreamy, intense, nostalgic, epic, intimate, ethereal |
| **Vocal** | male vocalist, female vocalist, choir, duet, rap, spoken word, instrumental |
| **Instrument** | piano, acoustic guitar, electric guitar, synth, strings, brass, saxophone, flute, organ, bass, drums, 808 |
| **Production** | high quality, clean mix, lo-fi, vintage, modern, atmospheric, minimal, layered, spacious, raw |
| **Tempo** | slow, mid-tempo, fast, upbeat, downtempo, ballad |
| **Era** | 80s, 90s, 2000s, retro, futuristic, timeless |

### Remix 变换矩阵

| Remix Type | Transform | Keep |
|------------|-----------|------|
| **Genre Flip** | Genre, instruments | Melody, structure, lyrics |
| **Energy Shift** | BPM, intensity, drums | Chords, vocal, mood hints |
| **Mood Flip** | Major↔minor, bright↔dark | Melody, structure |
| **Stripped/Acoustic** | Electronic→organic instruments | Chords, vocal, structure |
| **Club / Festival** | BPM↑, drops added, energy↑ | Hook, vocal hooks |
| **Lo-fi / Chill** | BPM↓, degradation, warmth | Chords, melody |
| **Cinematic / Orchestral** | Full orchestra, dynamics | Theme, structure |
| **Era Swap** | Production style (80s/90s/etc.) | Song core |

### 常用风格模板

**Pop:** `<sub-genre> pop, <mood>, <tempo> BPM, <key>. <gender> vocal, <vocal style> delivery. <lead instrument>, <rhythm>, <bass>. Catchy hooks, memorable chorus, <production> production. <era> influence, radio-ready mix.`

**Electronic / EDM:** `<sub-genre>, <BPM> BPM, <key>. <build/drop structure description>. <synth type> lead, <bass type> bass, <drum style> drums. <atmospheric element>, <FX details>. <club/festival/chill> energy, <production> mix.`

**Hip-Hop / Trap:** `<sub-genre>, <BPM> BPM, <key>. <drum style> drums, <808/bass description>. <melodic element>, <texture/pad>. <vocal delivery style>, <flow description>. <mood>, <era> production.`

**Cinematic / Orchestral:** `<orchestral/cinematic/hybrid>, <BPM> BPM, <key>. <orchestra sections used>. Building from <starting dynamic> to <climax dynamic>. <emotional journey description>. <film/game/trailer> energy, <mix style>.`

**Lo-fi / Chill:** `lo-fi <genre>, slow-mid tempo, <key>. <lead instrument>, warm <bass>, <drum style> drums. Tape saturation, vinyl crackle, <reverb type>. <season/time of day> vibe, nostalgic, intimate. Study/focus/chill energy.`

**Rock / Alternative:** `<sub-genre> rock, <tempo>, <key>. <guitar style>, <amp type>, <bass>, <drum style> drums. <vocal style> vocals, <delivery>. <energy level>, <era> production. <live/studio> feel.`

### 多版本矩阵生成器

| Dimension | Version A | Version B | Version C |
|-----------|-----------|-----------|-----------|
| **Energy** | Low/Ambient | Mid/Balanced | High/Peak |
| **Mood** | Dark | Neutral | Bright |
| **Genre** | Electronic | Pop | Cinematic |
| **Tempo** | Slow (60-80) | Mid (90-120) | Fast (130-160) |

### 歌曲风格迁移工作流

当用户说「参考 A 的结构但用 B 的氛围」时：分析 A → 提取结构/乐器/人声特征；分析 B → 提取情绪/风格/制作特征；合并：A 的骨架 + B 的皮肤 = 新 prompt

### JSON 输出格式（按需）

```json
{
  "analysis": {
    "bpm": 128,
    "key": "C minor",
    "genre": ["electronic", "pop"],
    "mood_arc": "calm → emotional → energetic",
    "instruments": ["synth pads", "punchy drums", "deep bass"],
    "vocal_style": "female airy vocal with light reverb",
    "structure": [
      {"section": "intro", "start": "0:00", "end": "0:10", "mood": "calm"},
      {"section": "verse", "start": "0:10", "end": "0:40", "mood": "emotional"},
      {"section": "chorus", "start": "0:40", "end": "1:10", "mood": "energetic"},
      {"section": "bridge", "start": "1:10", "end": "1:30", "mood": "atmospheric"},
      {"section": "final chorus", "start": "1:30", "end": "2:00", "mood": "euphoric"}
    ],
    "production": "clean mix, wide stereo, modern mastering"
  },
  "suno_prompt": "...",
  "udio_prompt": "...",
  "remix_prompts": [
    {"type": "dark cinematic", "prompt": "..."},
    {"type": "lo-fi emotional", "prompt": "..."},
    {"type": "festival remix", "prompt": "..."},
    {"type": "acoustic stripped", "prompt": "..."},
    {"type": "orchestral", "prompt": "..."}
  ],
  "tags": ["emotional", "cinematic", "pop", "electronic", "female vocal"]
}
```

### 音乐标签系统

```json
{
  "genre_tags": ["electronic", "pop", "synthwave"],
  "mood_tags": ["emotional", "energetic", "uplifting", "cinematic"],
  "instrumen