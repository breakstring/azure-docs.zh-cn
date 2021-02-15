---
title: 语音音标集 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解语音服务音标符号表如何映射到国际音标 (IPA)，以及何时使用哪个集。
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "78675327"
---
# <a name="speech-service-phonetic-sets"></a>语音服务音标集

语音服务为简短的) （包含七种语言） ( "电话组" 定义拼音字母; `en-US`、 `fr-FR` 、 `de-DE` 、 `es-ES` 、 `ja-JP` 、 `zh-CN` 和 `zh-TW` 。 语音服务电话设置通常会映射到<a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">国际拼音字母 (IPA) <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。 语音服务音标集与文本转语音服务产品中的[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 结合使用。 本文介绍这些音标集的映射方式，以及何时使用哪个音标集。

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>英语超切分音位

| 示例 1（音节首为辅音，单词首为元音） | 示例 2（元音间为辅音，单词音节核为元音） | 示例 3（音节尾为辅音，单词尾为元音） | 注释 |
|--|--|--|--|
| burger  /b er **1** r - g ax r/ | falafel  /f ax - l aa **1** - f ax  l/ | guitar  /g ih - t aa **1** r/ | 语音服务音标集在重音音节的元音后面读重音 |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | dissimilarity  /d ih - s ih **2**- m ax -  l eh 1 - r ax - t iy/ | workforce /w er 1 r k - f ao **2** r s/ | 语音服务音标集在次重音音节的元音后面读重音 |

### <a name="english-vowels"></a>英语元音

| `sapi` | `ipa` | 示例 1     | 示例 2 | 示例 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **ea**t       | f**ee**l  | vall**ey**                  |
| ih     | `ɪ`   | **i**f        | f**i**ll  |                             |
| ey     | `eɪ`  | **a**te       | g**a**te  | d**ay**                     |
| eh     | `ɛ`   | **e**very     | p**e**t   | m**eh**（极少用在单词末尾） |
| ae     | `æ`   | **a**ctive    | c**a**t   | n**ah**（极少用在单词末尾） |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**ah**（极少用在单词末尾） |
| ao     | `ɔ`   | **o**range    | c**au**se | Ut**ah**                    |
| uh     | `ʊ`   | b**oo**k      |           |                             |
| ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| ah     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| ay     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| aw     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**man | f**ew**                     |
| ax     | `ə`   | **a**go       | wom**a**n | are**a**                    |

### <a name="english-r-colored-vowels"></a>英语卷舌元音

| `sapi` | `ipa` | 示例 1    | 示例 2      | 示例 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **ear**s     | t**ir**amisu   | n**ear**   |
| eh r   | `ɛɹ`  | **air**plane | app**ar**ently | sc**ar**e  |
| uh r   | `ʊɹ`  |              |                | c**ur**e   |
| ay r   | `aɪɹ` | **Ire**land  | f**ir**eplace  | ch**oir**  |
| aw r   | `aʊɹ` | **hour**s    | p**ower**ful   | s**our**   |
| ao r   | `ɔɹ`  | **or**ange   | m**or**al      | s**oar**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **ear**th    | b**ir**d       | f**ur**    |
| ax r   | `ɚ`   |              | all**er**gy    | supp**er** |

### <a name="english-semivowels"></a>英语半元音

| `sapi` | `ipa` | 示例 1           | 示例 2  | 示例 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue**de | al**w**ays |           |
| y      | `j`   | **y**ard, f**e**w   | on**i**on  |           |

### <a name="english-aspirated-oral-stops"></a>英语口送气塞音

| `sapi` | `ipa` | 示例 1 | 示例 2   | 示例 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | ran**d**om  | ro**d**    |
| k      | `k`   | **c**ut   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>英语鼻塞音

| `sapi` | `ipa` | 示例 1        | 示例 2  | 示例 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**at, smash   | ca**m**era | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | chicke**n** |
| ng     | `ŋ`   |                  | li**n**k   | s**ing**    |

### <a name="english-fricatives"></a>英语摩擦音

| `sapi` | `ipa` | 示例 1   | 示例 2        | 示例 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ork    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| th     | `θ`   | **th**in    | empa**th**y      | mon**th**  |
| dh     | `ð`   | **th**en    | mo**th**er       | smoo**th** |
| s      | `s`   | **s**it     | ri**s**k         | fact**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | kid**s**   |
| sh     | `ʃ`   | **sh** e    | abbrevia**ti**on | ru**sh**   |
| zh     | `ʒ`   | **J**acques | plea**s**ure     | gara**g**e |
| h      | `h`   | **h**elp    | en**h**ance      | a-**h**a!  |

### <a name="english-affricates"></a>英语塞擦音

| `sapi` | `ipa` | 示例 1 | 示例 2    | 示例 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**in  | fu**t**ure   | atta**ch** |
| jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g**e |

### <a name="english-approximants"></a>英语无擦通音

| `sapi` | `ipa` | 示例 1          | 示例 2  | 示例 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**ad  | pa**l**ace | chi**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>法语超切分音位

语音服务音标集在重音音节的元音后面读重音，但是，`fr-FR` 语音服务音标集不支持 IPA 次重音“ˌ”。 如果需要 IPA 次重音，应直接使用 IPA。

### <a name="french-vowels"></a>法语元音

| `sapi` | `ipa` | 示例 1     | 示例 2       | 示例 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **a**rbre     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p**â**te        | p**a**s   |
| aa ~   | `ɑ̃`  | **en**fant    | enf**en**t      | t**em**ps |
| ax     | `ə`   |               | p**e**tite      | l**e**    |
| eh     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| eu     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| ey     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**ture    | mat**in** |
| iy     | `i`   | **i**dée      | pet**i**te      | am**i**   |
| oe     | `œ`   | **œu**f       | p**eu**r        |           |
| oh     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| oh ~   | `ɔ̃`  | **on**ze      | r**on**deur     | b**on**   |
| ow     | `o`   | **au**diteur  | b**eau**coup    | p**ô**    |
| oe ~   | `œ̃ ` | **un**        | l**un**di       | br**un**  |
| uw     | `u`   | **ou**trage   | intr**ou**vable | **ou**    |
| uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>法语辅音

| `sapi` | `ipa` | 示例 1   | 示例 2     | 示例 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)park**ing** |
| hy     | `ɥ`   | h**u**ile   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | é**c**aille   | be**c**                          |
| l      | `l`   | **l**ong    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | re**p**as     | ca**p**                          |
| r      | `ʁ`   | **r**at     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | a**ss**ez     | pa**ss**e                        |
| sh     | `ʃ`   | **ch**anter | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | in**v**enter  | rê**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | Marse**ille**                    |
| z      | `z`   | **z **éro   | rai**s**onner | ro**s**e                         |
| zh     | `ʒ`   | **j**ardin  | man**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *仅适用于某些外来单词。*

> [!TIP]
> `fr-FR` 语音服务音标集不支持以下法语连音：`n‿`、`t‿` 和 `z‿`。 如果需要，应考虑直接使用 IPA。

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>德语超切分音位

| 示例 1（音节首为辅音，单词首为元音） | 示例 2（元音间为辅音，单词音节核为元音） | 示例 3（音节尾为辅音，单词尾为元音） | 注释 |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | 语音服务音标集在重音音节的元音后面读重音 |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m  a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | 语音服务音标集在次重音音节的元音后面读重音 |

### <a name="german-vowels"></a>德语元音

| `sapi` | `ipa`     | 示例 1                             | 示例 2     | 示例 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **A**ber                              | Maßst**a**b   | Schem**a**                         |
| a      | `a`       | **A**bfall                            | B**a**ch      | Agath**a**                         |
| oh     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| eh:    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1</sup>](#de-v-1)Fasci**ae** |
| eh     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e**                          |
| iy     | `iː`      | **I**ran                              | abb**ie**gt   | Relativitätstheor**ie**            |
| ih     | `ɪ`       | **I**nnung                            | s**i**ngen    | Wood**y**                          |
| eu     | `øː`      | **Ö**sen                              | abl**ö**sten  | Malm**ö**                          |
| ow     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**ow**                        |
| oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| ey     | `e`, `eː` | **E**berhard                          | abf**e**gt    | b                                  |
| uw     | `uː`      | **U**do                               | H**u**t       | Akk**u**                           |
| uh     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| ue     | `yː`      | **Ü**bermut                           | pfl**ü**gt    | Men**ü**                           |
| uy     | `ʏ`       | **ü**ppig                             | S**y**stem    |                                    |

<a id="de-v-1"></a>
**1** 仅出现在外来单词中，例如：  Fasci**ae**。<br>
<a id="de-v-2"></a>
**2** 用作单词首部时仅出现在外来单词中，例如 **A**ppointment。  出现在首音节中的情况：'v**e**rstauen。

### <a name="german-diphthong"></a>德语复元音

| `sapi` | `ipa`       | 示例 1    | 示例 2          | 示例 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei** |
| aw     | `au`        | **au**ßen    | abb**au**st        | St**au**  |
| oy     | `ɔy`, `ɔʏ̯` | **Eu**phorie | tr**äu**mt         | sch**eu** |

### <a name="german-semivowels"></a>德语半元音

| `sapi` | `ipa` | 示例 1 | 示例 2    | 示例 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | lock**er** |

### <a name="german-consonants"></a>德语辅音

| `sapi` | `ipa` | 示例 1 | 示例 2 | 示例 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1</sup>](#de-c-1)Pu**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3</sup>](#de-c-3)Len**d**l | [<sup>4</sup>](#de-c-4)Clau**d**e |  |
| jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5</sup>](#de-c-5)Chan**g**e |
| f | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut |  | [<sup>6</sup>](#de-c-6)Gre**g** |  |
| h | `h` | **H**ausanbau |  |  |  |
| y | `j` | **J**od | Reakt**i**on | hu**i** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **l**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**ut | A**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | Huh**n** |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk** | R**ing** |  |
| p | `p` | **P**artner | abru**p**t | Ti**p** |  |
| pf | `pf` | **Pf**erd | dam**pf**t | To**pf** |  |
| r | `ʀ`、`r`、`ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| sh | `ʃ` | **Sch**ule | mi**sch**t | lappi**sch** |  |
| t | `t` | **T**raum | S**t**raße | Mu**t** |  |
| ts | `ts` | **Z**ug | Ar**z**t | Wit**z** |  |
| ch | `tʃ` | **Tsch**echien | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9</sup>](#de-c-9)Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12)Ba**ch**erach | Ma**ch**t mögli**ch**st | Schma**ch** 'i**ch** |
| z | `z` | **s**uper |  |  |  |
| zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** 仅出现在外来单词中，例如：  Pu**b**。<br>
<a id="de-c-2"></a>
**2** 软化“e”和“i”后面的“ch” <br>
<a id="de-c-3"></a>
**3** 仅出现在外来单词中，例如：  Len**d**l。<br>
<a id="de-c-4"></a>
**4** 仅出现在外来单词中，例如：  Clau**d**e。<br>
<a id="de-c-5"></a>
**5** 仅出现在外来单词中，例如：  Chan**g**e。<br>
<a id="de-c-6"></a>
**6** 用作单词尾部时仅出现在外来单词中，例如 Gre**g**。 <br>
<a id="de-c-7"></a>
**7** 仅出现在外来单词中，例如：  **Ng**uyen。<br>
<a id="de-c-8"></a>
**8** 仅出现在外来单词中，例如：  **S**taccato。<br>
<a id="de-c-9"></a>
**9** 仅出现在外来单词中，例如：  Gr**oo**ve。<br>
<a id="de-c-10"></a>
**10** IPA `x` 是所有非前元音（a、aa、oh、ow、uh、uw 和复元音 aw）后面的硬化“ch”。 <br>
<a id="de-c-11"></a>
**11** IPA `ç` 是前元音（ih、iy、eh、ae、uy、ue、oe、eu 以及复元音 ay、oy）和辅音后面的软化“ch” <br>
<a id="de-c-12"></a>
**12** *用作单词首部时仅出现在外来单词中，例如：**J**uan。用作首音节时也出现在类似于下面的单词中：Ba**ch**erach。*<br>

### <a name="german-oral-consonants"></a>德语口腔辅音

| `sapi` | `ipa` | 示例 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich     /b ax - ^ a 1 x t - l ih c/ |

> [!NOTE]
> 需要在两个不同的元音之间添加 [gs\] 音标，但这两个元音是真正复元音除外。 此口头辅音是一个喉塞音符，有关详细信息，请参阅 <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank"> 喉塞音 stop <span class="docon docon-navigate-external x-hidden-focus"> </a> </a> 。

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>西班牙语元音

| `sapi` | `ipa` | 示例 1    | 示例 2     | 示例 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **a**lto     | c**a**ntar    | cas**a**     |
| i      | `i`   | **i**bérica  | av**i**spa    | tax**i**     |
| e      | `e`   | **e**lefante | at**e**nto    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>西班牙语辅音

| `sapi` | `ipa`      | 示例 1  | 示例 2      | 示例 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**eque | co**ch**e      | Marraque**ch** |
| d      | `d`        | **d**edo   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| f      | `f`        | **f**ácil  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | a**g**ua       | tuare**g**     |
| j      | `j`        | **i**odo   | cal**i**ente   | re**y**        |
| jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | a**l**a        | corde**l**     |
| ll     | `ʎ`        | **ll**ave  | desarro**ll**o |                |
| m      | `m`        | **m**order | a**m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| nj     | `ɲ`        | **ñ**aña   | ara**ñ**azo    |                |
| p      | `p`        | **p**oca   | to**p**o       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | a**t**ar       | disque**t**    |
| th     | `θ`        | **z**ebra  | a**z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | a**j**o        | relo**j**      |

> [!TIP]
> `es-ES` 语音服务音标集不支持以下西班牙语 IPA：`β`、`ð` 和 `ɣ`。 如果需要，应考虑直接使用 IPA。

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

的语音服务电话设置 `zh-CN` 基于本机电话<a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">拼音 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>集。

### <a name="tone"></a>声调

| 拼音声调 | `sapi` | 字符示例 |
|-------------|--------|-------------------|
| mā          | ma  1  | 妈                 |
| má          | ma  2  | 麻                 |
| mǎ          | ma  3  | 马                 |
| mà          | ma  4  | 骂                 |
| ma          | ma  5  | 嘛                 |

#### <a name="example"></a>示例

| 字符 | 语音服务                |
|-----------|-------------------------------|
| 组织关系      | zu  3 - zhi 1 - guan 1 - xi 5 |
| 累进        | lei  3 -jin 4                 |
| 西宅巷       | xi  1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

的语音服务电话设置 `zh-TW` 基于本机电话<a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">注音 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>集。

### <a name="tone"></a>声调

| 语音服务声调 | Bopomofo 声调 | 示例（单词） | 语音服务音标 | Bopomofo | Pinyin（拼音） |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | empty         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ  ㄗ˙               | 一ㄥˇ  ㄗ˙  | yǐng  zi    |

#### <a name="example"></a>示例

| 字符 | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

的语音服务电话设置 `ja-JP` 基于本机电话<a href="https://en.wikipedia.org/wiki/Kana" target="_blank">假名 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>集。

### <a name="stress"></a>施加

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` substress  |

#### <a name="example"></a>示例

| 字符 | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | 中转ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***