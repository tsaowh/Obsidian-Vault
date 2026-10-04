可以。這次我把**今天稍早的第一次 CLSR 報告視為舊 baseline 不算**，重新依你後來確定的新 prompt 邏輯跑一次。

這次採用的範圍是：

> **最新正式一期 Volume 62（September 2026）整期重新納入 issue-level screening**  
> ＋  
> **Volume 62 出刊後，目前已 online / future-assigned 至 Volume 63（December 2026）的新文章**

其中 Volume 62 已經是正式出版卷；目前官方保存資訊已涵蓋到 Volume 62，而 ScienceDirect 已經有多篇文章標為未來的 **Volume 63, December 2026**，所以這一批我視為「季度之間的 online incremental layer」。[ISSN Portal](https://portal.issn.org/resource/ISSN/2212-4748?utm_source=chatgpt.com)

這次也完全照你新規則：

- 所有文章先篩，但**只輸出 A**
- 不硬湊 A 數量
- A 有全文才做完整 Deep Read
- abstract-only 只做 preliminary assessment
- 先白話，再進法律、技術、理論與方法
- 趨勢判斷使用整期 metadata，不只看 A
- 正式一期做 issue-level synthesis，online-first 則做增量分析

ScienceDirect 的完整 TOC 直接抓取有時會遇到 403，所以我以個別 ScienceDirect article pages，加上 SSRN、institutional repositories 與出版 metadata 交叉核對。這是這次 discovery 的限制。

---

# 一、這次真正留下的 A

我重新篩完後，認為這批最值得你花時間的有以下 **10 篇**。

| Paper                                                                    | Status        | Topic                                | Method                                                      | Technical depth                      | Access                                         | 建議                              |
| ------------------------------------------------------------------------ | ------------- | ------------------------------------ | ----------------------------------------------------------- | ------------------------------------ | ---------------------------------------------- | ------------------------------- |
| **What are AI systems? Rethinking the core definition in the EU AI Act** | Vol.62        | AI Act definition                    | interdisciplinary doctrinal + technical conceptual analysis | High                                 | Full text — OA                                 | **Must Read**                   |
| **Compliance under the EU AI Act**                                       | Vol.62        | AI compliance                        | qualitative empirical                                       | Medium                               | Full text — OA                                 | **Must Read**                   |
| **Mind the competitiveness gap**                                         | Vol.62        | AI Act extraterritoriality           | empirical legal research                                    | Medium                               | Full text — OA / lawful preprint               | **Must Read**                   |
| **Traceability for privacy**                                             | Vol.62        | compliance infrastructure            | legal-technical architecture analysis                       | **High**                             | Full text — OA / SSRN                          | **Must Read**                   |
| **The enforced technical mandate**                                       | Vol.62        | deepfake / biometric fraud           | doctrinal-functional comparative                            | High                                 | Full text — OA / repository                    | Read                            |
| **The constitutional foundation of explanation rights**                  | Vol.62        | explanation / public law             | constitutional + doctrinal                                  | Low–Medium technically, High legally | Full text — OA / SSRN                          | Read                            |
| **Rules for thee but not for me**                                        | Vol.62        | privacy enforcement                  | quantitative empirical legal research                       | Medium                               | Full text — OA                                 | Read                            |
| **Post-GDPR regulatory enforcement of UK data protection**               | future Vol.63 | regulatory enforcement               | doctrinal + public enforcement data                         | Low technically                      | Full text — OA / SSRN                          | Read                            |
| **Third-party countermeasures in cyberspace**                            | future Vol.63 | cyber governance / international law | doctrinal + state-practice analysis                         | Medium                               | **Abstract / substantial public preview only** | Track; full text when available |
| **The Data Embassy Model**                                               | future Vol.63 | digital sovereignty / infrastructure | international/EU law + infrastructure analysis              | Medium–High                          | Full text — OA / SSRN                          | Read                            |

---

# 二、A 級 Deep Read

## 1. What are AI systems? Rethinking the core definition in the EU AI Act

Philip Meinel、Kevin Baum、Holger Hermanns、Emily Pöppelmann、Markus Langer、Anne Lauber-Rönsberg  
Volume 62, 106373  
**Full text — Open Access** [DOI](https://doi.org/10.1016/j.clsr.2026.106373?utm_source=chatgpt.com)

### 白話先說

這篇在問一個看似最基本、其實非常難的問題：

> **AI Act 到底憑什麼判斷一套軟體是「AI system」？**

現在 Article 3(1) 把 **“infers … how to generate outputs”** 當成核心。

問題在於，從 computer science 看，AI 和「普通軟體」並沒有一道自然的牆。Rule-based system、statistical model、machine learning model 可以互相混合。

作者認為，把「技術看起來夠不夠複雜」當成界線會抓錯重點；真正值得法律關注的是：

> **人到底還能不能預測、理解、重建和控制系統的行為。**

他們稱這個方向為 **limited controllability**。[DOI](https://doi.org/10.1016/j.clsr.2026.106373?utm_source=chatgpt.com)

### 法律＋技術核心

作者先追蹤：

Commission proposal  
→ Council  
→ Parliament  
→ OECD definition  
→ 最終 Article 3(1)

可以看到 EU 從「列舉 AI techniques」逐漸走向 technology-neutral definition。

但是 _inference_ 自己又變成新的模糊詞。

作者使用 COMPAS 等 borderline systems 顯示：

> 技術比較簡單 ≠ 風險比較低。

一套 rule-based system 完全可以把 machine-learning-derived statistical variables 納入規則後，做出高度影響基本權的決策。

所以：

**technical complexity → 不是好的 legal proxy。**

### 研究方法值得學什麼？

這篇是非常典型的好 technology-law paper：

1. 找出法律概念。
2. 回到 technical reality。
3. 發現法律所使用的 proxy 與技術現象不一致。
4. 用具體 borderline case 測試。
5. 再提出新的 functional criterion。

不是：

> 「AI 很複雜，因此法律應該改革。」

而是：

> **法律界線究竟捕捉了哪一個 technical property？這個 property 和 regulation purpose 有沒有關係？**

這種研究結構很值得你學。

### 限制

limited controllability 並不會神奇地產生一條 binary test。

Predictability、reconstructability、human intervention 本身都是程度問題。

所以它改善了**概念方向**，但沒有完全解決 operationalisation。

### 對你最值得留下的研究 gap

下一步不是再寫：

> 「AI definition 有問題。」

而是：

> **limited controllability 能否轉化為可執行、可稽核的 legal-technical test？**

這就是很漂亮的 AI × Law 題目。

---

# 2. Compliance under the EU AI Act: How Firms Navigate Regulatory Complexity

David Restrepo Amariles、Pablo Marcello Baquero  
Volume 62, 106390  
**Full text — Open Access** [科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001318?utm_source=chatgpt.com)

### 白話先說

法律規定寫完之後，公司實際上不是拿著一本 AI Act 一條一條打勾。

它同時面對：

> AI Act  
> GDPR  
> NIS2  
> CRA  
> DSA  
> DORA  
> product safety rules  
> sectoral rules

所以真正的問題是：

> **企業到底怎麼把這些重疊的規範變成組織內真正能執行的 compliance process？**

### 方法

作者以：

- 10 家企業 semi-structured questionnaires
- 4 家企業深入訪談

共 **14 家 European firms** 做 exploratory qualitative research。[DOI](https://doi.org/10.1016/j.clsr.2026.106390?utm_source=chatgpt.com)

研究特別看三個面向：

> **roles → processes → technology**

結果不是「企業正在全面整合 compliance」。

而是作者所稱：

> **selective integration**

能共用的：

- risk categories
- controls
- policies
- governance

就共用。

法規本身有特殊要求時則分開處理。

### 很重要的一點

作者把 regulatory complexity 看成的不只是：

> 法規很多。

而是一個 multilayer ecosystem：

- laws
- guidelines
- standards
- different regulated roles
- supply-chain relations
- internal legal / technical / DPO / security teams

所以 compliance 本身成為一個 **adaptive governance process**。[DOI](https://doi.org/10.1016/j.clsr.2026.106390?utm_source=chatgpt.com)

### 研究方法值得學

這篇非常值得你看 methodology。

因為它示範：

> doctrinal assumption  
> ↓  
> 不直接認定公司「應該這樣 compliance」  
> ↓  
> 去問 regulated actors 實際怎麼做  
> ↓  
> 再從 empirical evidence 建 conceptual model

這比純粹讀 AI Act 後設計一套 imaginary compliance framework 強很多。

### 限制

作者自己很保守。

14 家不能代表「歐洲企業」。

而且願意接受研究的公司本身，很可能 already have relatively developed compliance functions。

因此它比較適合：

> **hypothesis-generating**

而不是 population-wide causal claims。

---

# 3. Mind the competitiveness gap: Measuring the AI Act's extraterritorial reach

Kamil Szostak、Gijs van Dijck、Konrad Kollnig  
Volume 62, 106357  
Online **16 June 2026**  
**Full text — OA；亦有 lawful SSRN version**。[社會科學研究網絡](https://papers.ssrn.com/sol3/Delivery.cfm/5932214.pdf?abstractid=5932214&mirid=1&type=2&utm_source=chatgpt.com)

### 白話先說

AI Act 說：

> 即使公司不在歐洲，只要 AI output 在 EU 被使用，也可能受 AI Act 管。

法律寫得很遠，不代表國外公司真的理你。

這篇就在測：

> **這個 extraterritorial rule 在現實中到底有沒有行為效果？**

### 方法非常漂亮

研究者分析 **53 家 AI companies** 的：

- Terms of Service
- Acceptable Use Policies

並在 AI Act Article 5 penalties 開始適用的 **2025-08-02 前後**比較文件變化。

研究結果顯示，沒有 EU establishment 的 foreign firms compliance 很低；約只有 **14%** 在文件中提到 AI Act prohibitions，而有 EU presence 的企業比例高得多。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26000982?utm_source=chatgpt.com)

### 重要的 technical nuance

作者知道不能簡單用：

> 「模型技術上禁止某行為」

判斷 compliance。

LLM safety constraints 並不能可靠辨識 user 的真正目的，而且可能被繞過。

因此 terms / usage policies 本身成為有意義的 compliance signal。[ResearchGate](https://www.researchgate.net/publication/409553080_Mind_the_competitiveness_gap_Measuring_the_AI_Act%27s_extraterritorial_reach?utm_source=chatgpt.com)

### 貢獻

這篇最重要的不是：

> EU 競爭力受到傷害。

那還是一個需要更多證據的延伸判斷。

真正貢獻是：

> **把 extraterritoriality 從 doctrine 變成可量測的 empirical question。**

以前法律論文常：

> Article 2 scope 很廣  
> → Brussels Effect 很強。

這篇問：

> **regulated actors 有沒有真的改變行為？**

這就是更成熟的 regulatory scholarship。

### 你應該學的

如果未來研究台灣 AI regulation、資安 regulation 或採購 regulation：

不要只問：

> 法律要求什麼？

也可以問：

> **regulated entities 有沒有留下可觀察的 compliance behaviour？**

---

# 4. Traceability for privacy

Maitrayee Pathak  
Volume 62, 106372  
online metadata 顯示 **1 July 2026**；SSRN final revision 3 August 2026。[TGRS](https://globalresearchspace.com/space?star=W7166840104&utm_source=chatgpt.com)

### 白話先說

這篇想解決的是：

> EU 數位法律要求企業「負責、可稽核、能證明 compliance」，但法律自己沒有提供一套共同的證據基礎設施。

作者主張建立：

> **traceability layer**

也就是整個資料生命週期都留下可以驗證：

- 誰處理
- 為什麼處理
- 根據什麼 legal basis
- 資料往哪裡走
- 做了什麼
- 誰負責

的 evidence。

### 這篇真正有技術內容

作者比較：

**public / permissioned blockchain**

**attestation protocol（OTrace）**

**W3C Verifiable Credentials**

然後不是宣布某一個技術勝出，而是指出不同 architecture 做的事情不同。

例如：

- blockchain：immutability 強，但 GDPR controller identity、erasure、scalability 有問題。
- VC：適合 identity、consent、selective disclosure，但不適合 high-volume backend logging。
- attestation agent：適合持續 backend events，但本身需要信任與治理制度。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001136?utm_source=chatgpt.com)

因此提出：

> **EU-Trace**

把：

> user-controlled authorization via VC

和

> continuous logging via delegated Traceability Agent

分開。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001136?utm_source=chatgpt.com)

### 為什麼這篇很適合你

這不是典型：

> 「blockchain 可以幫助 GDPR。」

它真正做的是：

**法律功能**  
→ demonstrable accountability

**證據需求**  
→ lifecycle traceability

**技術比較**  
→ 哪個 architecture 能提供哪些 evidence

**制度設計**  
→ DGA intermediary / EUDI Wallet 怎麼嵌進去。

這是很接近你希望的 **50% technology / 50% law**。

### 重要限制

作者提出的 EU-Trace 還是一個 **architectural proposal**。

並沒有 deployment trial 可以證明：

- performance
- governance cost
- interoperability
- incentives
- actual regulator usability

都成立。

所以它非常適合做「下一篇研究」的起點，而不是把 proposal 當 proven solution。

---

# 5. The enforced technical mandate

Felipe Romero-Moreno  
Volume 62, 106376  
**Full text — OA；institutional repository 有 published PDF。** [科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001173?utm_source=chatgpt.com)

### 白話先說

作者認為 deepfake fraud 已經不是「有人偶爾做一支假影片」。

當 deepfake、voice cloning、identity verification attack 都可以服務化時：

> **Fraud-as-a-Service**

只靠事後抓詐騙犯不夠。

法律必須逼 deployment architecture 本身具備防偽能力。

### 三層架構

作者提出：

**Layer 1 — Source Control**

處理 biometrics、identity verification 和 erasure。

**Layer 2 — Distribution Control**

處理 deepfake detection、provenance、platform dissemination。

**Layer 3 — Accountability**

處理 corporate liability。 [科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001173?utm_source=chatgpt.com)

更具體的方案包括：

- **NIST IAL2**
- zero-retention biometric standards
- **C2PA provenance**
- 把 biometric integrity 與 **ISO 20022 financial messaging** 相連。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001173?utm_source=chatgpt.com)

### 研究上真正重要的是什麼？

不是那些 proposal 一定正確。

而是：

> **technology-forcing regulation**

法律不只是說：

> 不可以發生 deepfake fraud。

而是開始說：

> deployment architecture 必須具備某種 technical safeguard。

這和 automotive safety、cybersecurity regulation 的邏輯很像。

### 我會比較謹慎的地方

作者的 architecture 很積極。

C2PA、identity assurance、financial messaging interoperability 能不能實際形成完整 anti-fraud control chain，是需要 empirical / engineering validation 的。

所以要把：

> normative design

和

> technically demonstrated effectiveness

分開。

這也是你讀科技法律文章應該養成的習慣。

---

# 6. The constitutional foundation of explanation rights in EU digital regulation

Melanie Fink、Simona Demková  
Volume 62, 106391  
**Full text — OA；lawful SSRN version。** [科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X2600132X?utm_source=chatgpt.com)

### 白話先說

現在很多 EU 法規都有「你要解釋 automated decision」：

- GDPR
- DSA
- AI Act

但它們各自長出來，容易互相斷裂。

作者問：

> **這些 explanation rights 背後，有沒有一個共同的法律原理？**

答案是：

> **constitutional duty to state reasons**

### 作者把 fragmentation 分成兩種

**Inter-instrumental fragmentation**

同一個 organization 同時被 GDPR、DSA、AI Act 管。

**Intra-instrumental fragmentation**

真正有 technical knowledge 的可能是 provider；

真正對當事人負 explanation duty 的卻是 deployer。

也就是：

> **responsibility 和 information 分離。** [科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X2600132X?utm_source=chatgpt.com)

### 核心論證

作者不是要拿 constitutional principle 把 sectoral rules 消滅。

而是：

> constitutional principles 提供 value / normative orientation  
> sectoral law 提供 domain-specific precision。

兩者互補。

並透過兩個 scenarios 測試：

- overlapping regulatory roles
- multi-actor AI decision chain。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X2600132X?utm_source=chatgpt.com)

### 研究寫作最值得學

這是一個非常乾淨的 doctrinal structure：

> fragmentation  
> → 找上位 normative foundation  
> → 建 integrated framework  
> → 用 scenarios test  
> → 分清 interpretation 能解決什麼、legislation 才能解決什麼。

如果你之後往公法／AI regulation 寫作，這種 structure 很值得模仿。

---

# 7. Rules for thee but not for me: Selective privacy enforcement in Chinese court judgments

Hui Zhou、Genia Kostka  
Volume 62, 106392  
**Full text — Open Access**。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001331?utm_source=chatgpt.com)

### 白話先說

很多國家都有很漂亮的 privacy law。

但真正重要的另一個問題是：

> **法院對私人企業和政府機關，是不是用同樣標準執法？**

作者不是靠案例印象，而是蒐集 **6,370 件中國 civil / criminal judgments**。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001331?utm_source=chatgpt.com)

### 核心結果

在 civil cases 中：

> 原告對 public organisations 的勝訴情況顯著比較差。

但在 criminal cases：

> public affiliation 並沒有呈現同樣清楚的 advantage。

而 PIPL 被引用時，刑事刑期與 fines 反而更高，且不限於 private actors。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001331?utm_source=chatgpt.com)

所以研究結果不是簡單：

> 「中國隱私法只管民間、不管政府。」

而是更細緻：

> **selective enforcement 是 context-dependent。**

### 對你的方法論價值

這篇最值得學的是：

> **law on books → law in judgments**

如果未來你研究：

- AI governance
- 資安法規
- 個資法
- 行政機關 technology regulation

可以考慮：

> statute analysis + large-scale case analysis

而不是一直停在條文解釋。

---

# 三、Volume 62 出刊後的新 A

這就是你剛才討論的第二層：

> **正式 issue 之外的 monthly incremental layer。**

目前已有若干文章先被指派到 **Volume 63, December 2026**。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001501?utm_source=chatgpt.com)

---

## 8. Post-GDPR regulatory enforcement of UK data protection

David Erdos  
future Volume 63, 106395  
**Full text — OA；Cambridge / SSRN lawful version。** [科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001367?utm_source=chatgpt.com)

### 白話先說

GDPR 最大威懾之一理論上是：

> regulator 可以重罰。

但英國的問題是：

> **法律給 regulator 很大武器，不等於 regulator 真的使用。**

最新版資料顯示 ICO 每年平均收到 **43,000+ complaints**，但 2018/19–2025/26 平均每年只有 **6.8 fines**、約 **1.8 enforcement notices**。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001367?utm_source=chatgpt.com)

作者接著不是只責怪 ICO，而是看：

- Tribunal
- Ombudsman
- High Court
- Parliament
- EHRC

是否形成有效的 **oversight of the regulator**。

### 為什麼值得 A？

它問的是科技監管非常根本的一件事：

> **誰監管 regulator？**

法律研究常停在：

> regulated company compliance。

這篇往上一層：

> regulator 本身的 enforcement discretion 有沒有 accountability？

這和你偏好的公法監管非常接近。

### 值得延伸

AI Act implementation 以後同樣會碰到：

> competent authority 有權 ≠ 真正有效 enforcement。

所以 privacy regulation 的 enforcement scholarship 很可能成為 AI governance 很重要的比較素材。

---

## 9. Third-party countermeasures in cyberspace

Khalifa Alkuwari、Anas Abdelrahman  
future Volume 63, 106396  
目前我可確認到相當完整的 abstract / section preview，但**沒有確認 lawful complete full-text copy，因此這裡只做 preliminary assessment**。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001379?utm_source=chatgpt.com)

### 白話先說

假設 A 國被 B 國網攻。

國際法傳統上允許受害國採取 countermeasures。

但如果：

> B 的 cyber operation 威脅整個國際社會，

C、D、E 國能不能一起採取本來可能違反國際義務的 countermeasures？

這就是這篇的核心。

### 可以確認的論證架構

作者區分：

- **lex lata**：現在的法律究竟允許什麼
- **lex ferenda**：法律未來應該怎麼發展

分析：

- Articles 42, 49, 54 of ASR
- ICJ jurisprudence
- state position papers
- Tallinn Manual 2.0
- WannaCry
- NotPetya
- Albania 2022
- EU Cyber Diplomacy Toolbox。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001379?utm_source=chatgpt.com)

作者的結論相當克制：

> 現有 law 尚不足以說 collective countermeasures 已合法化；

但 state practice 出現 coordinated response 的方向。

### 為什麼值得追？

這是真正的：

> **cyber capability / networked harm → challenge traditional public international law structure**

不是把一般法律問題加上「cyber」而已。

而且 attribution、proportionality、third-state infrastructure 都是 technology environment 真正改變法律操作條件的地方。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001379?utm_source=chatgpt.com)

**但因為目前沒有確認合法全文，我不進一步替作者製造 limitations 或 gaps。**

---

## 10. The Data Embassy Model

Mando Rachovitsa  
future Volume 63, 106409  
SSRN posted **24 September 2026**；Volume 63 December 2026。  
**Full text — OA / lawful SSRN。** [科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001501?utm_source=chatgpt.com)

### 白話先說

一般直覺是：

> 國家的關鍵資料要有 sovereignty，就應該放在自己國境裡。

Estonia 卻把 critical governmental data 與 essential services 放在 Luxembourg 的 data centre。

問題就變成：

> **資料和伺服器在外國領土，國家還能不能保持 digital sovereignty？**

### 技術背景其實不複雜

核心 infrastructure 邏輯是：

> geographical redundancy
> 
> - continuity of government
> - cyber resilience.

真正複雜的是法律：

server physically in Luxembourg  
→ Luxembourg territorial jurisdiction

但 Estonia 又需要：

> operational control + inviolability + protection。

因此以 **sui generis bilateral treaty** 賦予 data centre 類似 diplomatic protection 的地位。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26001501?utm_source=chatgpt.com)

文章再把它放到：

- diplomatic law
- EU cybersecurity law
- international humanitarian law
- sovereignty

分析。

### 為什麼這題很好？

因為它不是：

> 「科技發展造成法律問題。」

而是：

> **infrastructure architecture 改變了法律原本默認的 territorial assumption。**

這是很高品質的 technology-law research question。

它也說明：

> digital sovereignty 不等於 data localisation。

有時候把 infrastructure 放在境外，反而增加 resilience，並透過法律安排維持 control。

---

# 四、Monthly Comparative Table

|Paper|Topic|Method|Technical Depth|Research Novelty|與你的研究關聯|
|---|---|---|---|---|---|
|What are AI systems?|AI Act scope|doctrinal + CS conceptual|**High**|High|**Very High**|
|Compliance under AI Act|Compliance governance|qualitative empirical|Medium|High|**Very High**|
|Mind the competitiveness gap|Extraterritoriality|quantitative/content empirical|Medium|**High**|High|
|Traceability for privacy|compliance infrastructure|legal-technical architecture|**High**|**High**|**Very High**|
|Enforced technical mandate|Deepfake / cyber fraud|comparative doctrinal + technical|High|High|**Very High**|
|Constitutional explanation rights|explanation / public law|doctrinal / constitutional|Low–Med|High|**Very High**|
|Rules for thee…|Privacy enforcement|quantitative empirical|Medium|High|High|
|Post-GDPR enforcement|Regulatory enforcement|empirical public data + doctrinal|Low|High|**Very High**|
|Third-party cyber countermeasures|Cyber governance|doctrinal + state practice|Medium|High|High|
|Data Embassy|Digital sovereignty|infrastructure + international law|Med–High|**High**|**Very High**|

---

# 五、CLSR Topic Map：這一季真正發生什麼變化？

第一次建立 Topic Map 時，historical baseline 還不能假裝有多年 quantitative time series；不過和我能核對到的 **Volume 60 / 61** 代表性內容相比，已經看得到一個方向。

Volume 60 還有很多偏向：

- AI in adjudication
- DSA systemic risks
- technology adoption 的 general legal implications。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26000015?utm_source=chatgpt.com)

Volume 61 已經開始增加：

- agentic AI + GDPR
- adaptive AI regulation
- algorithmic fairness audits
- legal-tech / computable legal systems。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26000830?utm_source=chatgpt.com)

到了 Volume 62，則很明顯增加：

> **AI regulation operationalisation**

包括：

- AI system definition
- extraterritorial compliance
- organizational compliance
- explanation
- safeguards
- traceability
- enforcement。

所以我目前把 Topic Map 判成：

**AI Governance：Intensifying，而且從 rule design → implementation**

**Privacy & Data Governance：Intensifying，從 rights → enforcement / infrastructure**

**Cybersecurity：穩定，但開始出現 technology-forcing / international response 類更具體問題**

**Digital Evidence / Authenticity：Emerging**

**Digital Sovereignty：Emerging → Developing**

這個判斷未來每一個 formal issue 再累積，可信度會愈來愈高。

---

# 六、本期最重要的 3 個研究趨勢

## 1. AI regulation 從「法律怎麼寫」進到「法律怎麼真正運作」

Volume 62 最明顯。

我們同時看到：

> AI 是什麼  
> → What are AI systems?

> 公司到底守不守  
> → Compliance under the AI Act

> 國外公司到底理不理  
> → Mind the competitiveness gap

> 怎麼解釋 decision  
> → Explanation rights

這代表 AI-law scholarship 正在從：

> **normative regulatory design**

往：

> **operational / empirical regulation**

移動。

**成熟度：Developing。**

---

## 2. Accountability 正逐漸變成 evidence infrastructure 問題

這條線我覺得最值得你注意。

Traceability paper 說：

> compliance 需要 machine-verifiable evidence。

Deepfake paper 說：

> authenticity 需要 provenance infrastructure。

Explanation paper 說：

> 有 responsibility 的人可能拿不到 necessary information。

而未來 AI systems 又會有：

- logs
- model versions
- agent state
- data provenance
- human intervention records。

所以核心慢慢變成：

> **法律要求 accountability，但什麼 technical evidence 才能真正讓 accountability 可執行？**

**成熟度：Emerging。**

這一條非常符合你的研究定位。

---

## 3. 「法律存在」與「法律有效」開始被分開研究

這個訊號來自：

- AI Act extraterritorial compliance
- corporate compliance
- Chinese privacy judgments
- UK ICO enforcement。

它們共同問：

> statute 看起來很強，現實結果呢？

也就是：

**law on the books**  
≠  
**compliance behaviour**  
≠  
**enforcement**  
≠  
**actual protection**

這是 CLSR 從 doctrinal technology law 往更成熟 empirical regulatory scholarship 前進的一個很好訊號。

**成熟度：Developing。**

---

# 七、Research Radar

這次我只留 **3 題**。

## 1. Shared Compliance Evidence Layer

### 問題

能不能建立一套跨：

> AI Act + GDPR + NIS2 + DSA

的共同 compliance evidence architecture？

動機非常直接：

- Compliance paper → companies 只能 selective integration。
- Traceability paper → 技術上可以設計 shared evidence layer。
- Explanation paper → information 在不同 actors 之間分散。[DOI](https://doi.org/10.1016/j.clsr.2026.106390?utm_source=chatgpt.com)

### 真正未解決的地方

不是：

> 「blockchain 可以不可以？」

而是：

> 哪些 legal obligations 可以共享同一份 machine-readable evidence？

同時怎麼避免：

> logging 太多 → data minimisation / privacy risk。

### 可行方法

**requirements mapping**  
＋  
**technical architecture**  
＋  
**case-study audit / prototype**

這題目前是我最推薦你留下來的。

---

## 2. AI regulatory effectiveness 應該怎麼量？

現在已經有：

- ToS compliance
- organizational interviews
- public risk perceptions
- enforcement statistics。

但這些其實量的是不同東西：

> formal compliance  
> organizational compliance  
> perceived legitimacy  
> regulator action。

還缺：

> **一個 regulation 是否真的降低 AI harm / improve safety 的 empirical framework。**

方法可以：

> legal indicators + operational metrics + enforcement outcomes。

這很適合科技法律與 public-law regulation。

---

## 3. Mutable technical systems 要保存什麼 evidence 才能 judicially review？

這一題不完全直接由單篇 CLSR article 提出，而是由：

> traceability + explanation + digital evidence + agent developments

共同推導。

核心問題：

> AI system 被 challenge 時，法院／監管機關到底需要什麼 evidence？

可能包括：

- model/version
- input
- data provenance
- logs
- risk assessment
- human review
- system configuration
- policy state。

但：

> 全部保存又可能違反 privacy / security / proportionality。

這正是法律與系統設計真正會碰面的地方。

**Emerging。**

---

# 八、What I Should Read This Month

依你目前要建立科技法律研究能力，而不是單純補知識，我會只挑 **3 篇全文精讀**。

### Must Read 1

**What are AI systems? Rethinking the core definition in the EU AI Act**

因為它最能讓你學：

> **technical reality 如何真正改變 legal concept。**

這篇適合學「科技法律題目怎麼形成」。

[ScienceDirect article](https://doi.org/10.1016/j.clsr.2026.106373?utm_source=chatgpt.com)

### Must Read 2

**Traceability for privacy**

因為它最接近你想做的：

> **technology × law 各半**

而且是從 legal function 出發選 technical architecture，不是硬把新科技套法律。

[Lawful SSRN version](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5678966&utm_source=chatgpt.com)

### Must Read 3

**Compliance under the EU AI Act**

因為它會補你比較缺的一塊：

> **empirical technology-law research**

尤其值得看：

- research question
- questionnaire/interview design
- 如何從小樣本建立 exploratory conceptual model
- limitations 怎麼寫。

[ScienceDirect article](https://doi.org/10.1016/j.clsr.2026.106390?utm_source=chatgpt.com)

如果還有餘力，再讀：

**Mind the competitiveness gap** 的 Method + Results，學 quantitative / observable compliance research；以及 **The constitutional foundation of explanation rights**，學純 doctrinal argumentation 的結構。[科學通訊](https://www.sciencedirect.com/science/article/pii/S2212473X26000982?utm_source=chatgpt.com)

---

## 這次依新 prompt 重跑後，我會怎麼總結 Volume 62？

跟今天第一次的版本相比，我現在會把重點收得更集中：

> **Volume 62 最重要的訊號，不只是「AI Act 文章很多」，而是 technology-law scholarship 正從規則設計進入 regulation operationalisation：法律的 scope 怎麼定、企業怎麼執行、境外企業是否遵守、監管者是否 enforcement、以及 compliance 最後要靠什麼 technical evidence 才能證明。**

而正式 issue 之外，Volume 63 目前 online 出現的 **Data Embassy、UK enforcement、cyber countermeasures** 又讓下一層訊號開始浮現：

> **digital infrastructure、state capacity 與 public-law accountability 正變成 CLSR 另一條值得追的線。**

我認為這個版本才真正符合你現在修改後的 CLSR prompt：**整期用來看研究地圖與趨勢；月度 online articles 用來提早抓 signal；輸出只留下真正值得讀的文章。**