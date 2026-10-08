
這篇我讀下來，評價其實比剛才那篇 **What are AI systems?** 高不少。原因不是它更「技術」，而是它的研究問題、證據和結論之間咬得比較緊：**它提出一個可以觀察的制度問題，拿多年執法資料證明，再去追究為什麼既有監督機制沒有把問題修正。**

這篇是 David Erdos 單獨撰寫。他是 Cambridge Faculty of Law 的 Professor of Law and the Open Society，長期研究 data protection、ICO accountability、Brexit 後英國資料保護法；這篇其實也是他多年研究 ICO 執法問題的延伸，而不是突然跨進一個陌生題目。[SSRN](https://papers.ssrn.com/sol3/cf_dev/AbsByAuth.cfm?per_id=523682&utm_source=chatgpt.com)

# 白話先說

整篇其實就在問：

> **GDPR 號稱有非常強的執法武器，但如果主管機關幾乎不用，GDPR 還剩多少實際威嚇力？**

GDPR 的制度想像大致是：

```
違反資料保護法
       ↓
Data Protection Authority 調查
       ↓
reprimand / enforcement order / fine
       ↓
企業產生 compliance incentive
       ↓
資料保護權真正落實
```

但 Erdos 發現英國實際上比較像：

```
每年 4 萬多件 complaints
       ↓
ICO
       ↓
大量案件沒有進入正式強制程序
       ↓
極少 fine
極少 enforcement notice
       ↓
而 Tribunal / Court / Ombudsman / Parliament
也沒有有效逼 ICO 改變
```

所以這篇真正研究的不是：

> 「GDPR 規範夠不夠強？」

而是：

> **有很強的 substantive law，但 enforcement institution 不執法，會發生什麼事？**

這個問題其實比「GDPR 罰款最高 4%」重要得多。

==note start==

這篇文章真正關心的，不是 GDPR 的規範寫得夠不夠嚴，而是**再強的法律，如果監管機關不積極執法，實際效果仍可能非常有限**。Erdos 以英國資訊專員辦公室（ICO）為例指出，雖然每年收到大量資料保護申訴，但正式罰款與執法命令長期非常少；更重要的是，法院、審裁處、監察專員與國會等外部監督機制，也沒有有效迫使 ICO 改變這種低度執法模式。因此，文章把問題從「企業有沒有違反 GDPR」往上一層推進到「**監管機關是否真正履行執法義務，以及誰來監督監管機關本身**」。它最重要的啟示是：法律的威嚇力不只取決於最高罰則多重，更取決於違法後**實際被調查、被認定違法、被要求改正或受罰的可能性**。

- **GDPR（一般資料保護規則）**  
    歐盟最核心的個人資料保護法，不只規定企業如何合法蒐集、使用個資，也授權監管機關調查、命令改正和開罰。這篇關心的重點是：這些強大的法律工具有沒有真的被用出來。
- **ICO（Information Commissioner’s Office，英國資訊專員辦公室）**  
    英國負責個資保護與資訊權利監管的機關，可以受理申訴、調查違法、命令改善和裁罰。文章的核心批評，就是 ICO 擁有很強的法定權力，但長期正式執法的件數很少。
- **Complaint｜申訴**  
    指個人認為自己的資料保護權受到侵害，向 ICO 要求處理。申訴數量本身不代表每件都真的違法，但可以反映監管機關面臨的案件規模與社會需求。
- **Reprimand｜正式譴責**  
    監管機關正式認定某個組織違反資料保護規則並加以譴責，但未必伴隨金錢罰款。它屬於正式矯正措施，因此比一般建議或非正式警告更具法律與聲譽效果。
- **Enforcement notice｜執法命令**  
    監管機關正式要求組織停止某種違法行為、修改處理方式，或在一定期限內完成改善。它的重要性在於，即使沒有罰款，也能直接迫使違法狀態被改正。
- **Formal enforcement｜正式執法**  
    指監管機關真正動用法律授權的正式手段，例如罰款、譴責或執法命令，而不是只用電話、信件或協調方式處理。文章用正式執法的稀少，來衡量 ICO 是否存在長期執法不足的問題。
- **Substantive law｜實體法**  
    指法律直接規定人民或企業有哪些權利、義務與禁止事項，例如什麼情況下可以處理個資。這篇的重點是，實體法即使寫得很強，如果沒有有效執法，實際保障仍可能很弱。
- **Compliance incentive｜守法誘因**  
    指企業因為預期違法可能被查、被要求改善或受罰，而產生主動遵守法律的動機。如果違法後幾乎沒有實際後果，這種守法誘因就會降低。
- **Tribunal｜審裁處**  
    英國用來處理某些行政與資訊權利爭議的專門裁判機構，功能介於行政機關與一般法院之間。這篇關心的是，審裁處是否有足夠權力去糾正 ICO 對個案或執法策略的判斷。
- **Judicial review｜司法審查**  
    法院通常不是重新替 ICO 判斷每一件資料保護案件，而是檢查 ICO 的決定是否合法、是否有程序錯誤、是否不合理或濫用裁量。這代表法院對監管機關的介入通常有一定限度。
- **Ombudsman｜監察專員**  
    負責處理人民對政府機關行政失當的申訴，例如拖延、處理不公或程序不當。放在這篇裡，就是另一條可以用來監督 ICO 本身是否妥善履職的外部管道。
- **Parliamentary oversight｜國會監督**  
    指國會透過委員會、調查、聽證或報告監督監管機關是否妥善執行法律。文章關心的是，當司法機關不願深度介入監管裁量時，國會能否補上監督缺口。
- **Second-order accountability｜第二層監督／對監管者的問責**  
    第一層是 ICO 監督企業有沒有守法；第二層則是追問「誰來監督 ICO 有沒有認真監督企業」。這是本文最重要的制度問題之一。
- **Regulatory discretion｜監管裁量**  
    指法律允許監管機關在案件優先順序、是否處分、採取哪種措施等事項上自行判斷。真正的爭議是，這種裁量有沒有大到最後變成「可以幾乎不採取正式執法」。
- **Under-enforcement｜執法不足**  
    指法律雖然存在、監管機關也有權力，但實際採取的執法行動不足以實現法律目的。它不是「完全沒執法」，而是執法強度與違法規模、權利保障需求之間出現明顯落差。
- **Enforcement probability｜實際被執法的機率**  
    指違法行為從被發現、被調查，到最後被要求改正或受罰的實際可能性。企業真正感受到的法律威嚇力，往往比「法條最高罰多少」更取決於這個機率。

==note end==

---

# 一、最重要的數字：法律很兇，執法卻很弱

正式刊登版本已經更新到 **2018/19–2025/26**。

這段期間 ICO：

- 平均每年收到 **超過 43,000 件**涉及 data protection infringement 的 complaint；
- 平均每年只有 **6.8 件 fines**；
- 平均每年只有 **1.8 件 enforcement notice actions**。[科學直接](https://www.sciencedirect.com/science/article/pii/S2212473X26001367?utm_source=chatgpt.com)

工作論文較早版本甚至特別指出：

> 2024/25 年只有 **2 件罰款**，總額約 £3.8 million，而且 **0 件 enforcement notice**。[SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6247240&utm_source=chatgpt.com)

作者甚至整理出一個很值得注意的趨勢：

```
ICO 的收入、資源
        ↑
        
formal enforcement
        ↓
```

他早期簡報顯示，ICO data protection income 從 2017/18 約 £22m 成長至 2023/24 約 £80m，但正式 enforcement 並沒有同步增加。[www.slideshare.net](https://www.slideshare.net/slideshow/public-enforcement-of-uk-data-protection-promise-reality-and-future-f89a/277667696?utm_source=chatgpt.com)

所以作者要排除一個很直覺的解釋：

> 「可能是 ICO 沒錢、沒有人。」

至少單靠資源不足，很難完整解釋這個趨勢。

---

# 二、這篇真正厲害的地方不是「罰太少」

只寫：

> ICO 罰款太少，所以 GDPR 執法失敗。

其實會很弱。

因為 regulator 本來就不應該用「罰款數量」當 KPI。

可能存在：

```
warning
reprimand
audit
negotiated compliance
enforcement notice
informal intervention
```

企業可能甚至在沒有罰款時就改善。

Erdos 有意識到這個問題。

因此他不只看 fine，而一起看：

> **其他 formal corrective measures 有沒有補上。**

結果 enforcement notices 同樣非常少；他的較早統計甚至顯示 reprimands 在後期也下降。[www.slideshare.net](https://www.slideshare.net/slideshow/public-enforcement-of-uk-data-protection-promise-reality-and-future-f89a/277667696?utm_source=chatgpt.com)

所以論證變成：

```
不是：

罰款少
→ 所以 enforcement 弱

而是：

罰款少
+
formal enforcement notices 少
+
reprimands 趨弱
+
complaints 很多
+
外部監督又沒有有效糾正
        ↓
整體呈現 systemic under-enforcement
```

這個論證就強很多。

---

# 三、文章第二層才是真正有意思的地方：誰來監督 regulator？

這是整篇我最喜歡的部分。

一般想到資料保護，你會畫：

```
Company
   ↓
ICO 監督
```

作者卻再問一層：

```
Company
   ↓
ICO
   ↓
誰監督 ICO？
```

這就從 **data protection law** 進到了：

> **administrative law / public law / regulatory governance**

作者逐一檢查：

```
ICO
 │
 ├─ Tribunal
 ├─ High Court / judicial review
 ├─ Parliamentary and Health Service Ombudsman
 ├─ Parliament
 └─ Equality and Human Rights Commission
```

他的結論是：

> **這些 accountability mechanisms 過去整體都沒有有效阻止 ICO 走向低強制執法模式。** [科學直接](https://www.sciencedirect.com/science/article/pii/S2212473X26001367?utm_source=chatgpt.com)

這其實是文章真正的學術價值。

==note start==

這一段真正要證明的，不是單純「ICO 開罰太少」，而是英國資料保護執法可能存在**結構性的低度執法問題**。Erdos 先用長期數據指出，ICO 每年收到大量申訴，但罰款、執法命令與其他正式矯正措施都非常少；同時，ICO 的資源並沒有同步縮減，因此很難把原因單純歸咎於「沒錢、沒人」。更重要的是，法院、審裁處、監察專員、國會與平等人權機構等外部監督者，也沒有有效迫使 ICO 改變這種執法模式。因此，文章的核心從「ICO 罰得夠不夠多」進一步變成：**當監管機關長期不積極使用法定權力時，現有制度是否有能力監督並糾正監管機關本身？**

- **Data protection infringement｜違反資料保護法**  
    指企業或政府處理個人資料時違反 GDPR 等資料保護規則，例如沒有合法依據就蒐集、使用或揭露個資。是否真的構成違法，仍需經過具體調查與判斷。
- **Formal corrective measures｜正式矯正措施**  
    指監管機關為了終止或修正違法行為而正式採取的措施，包括譴責、命令改正、限制資料處理及罰款等。作者用這個較廣的概念避免把「執法」錯誤地等同於「開罰」。
- **Negotiated compliance｜協商式守法／協商改善**  
    監管機關不是立刻開罰，而是與組織協商改善措施，要求其自願修正違法或高風險做法。它的優點是彈性，但如果過度依賴，也可能降低法律的威嚇效果。
- **Informal intervention｜非正式介入**  
    指 ICO 透過電話、信件、建議或私下溝通要求組織改善，而不啟動正式處分程序。這種方式可能有效，但也比較難從公開資料判斷到底有多大實際效果。
- **Systemic under-enforcement｜結構性執法不足**  
    不是偶爾幾件案件沒處理好，而是長期、持續地很少使用正式執法工具，使法律的實際執行強度低於制度原本期待的程度。
- **Regulator｜監管機關**  
    指依法負責監督特定領域的行政機關，例如 ICO 就是英國資料保護領域的監管機關。它不只負責解釋規則，也有調查、命令改善和裁罰權。
- **Accountability mechanisms｜問責／監督機制**  
    指用來確保監管機關本身依法、合理履行職責的制度，例如法院、國會、審裁處或監察專員。這篇的重要問題就是：**如果 ICO 自己不積極執法，這些機制能不能逼它改變？**
- **Administrative law｜行政法**  
    研究政府機關有哪些權力、如何使用權力，以及法院如何監督行政機關。這篇從資料保護法往行政法深入，因為它開始問「ICO 是否有依法履行自己的職責」。
- **Public law｜公法**  
    處理國家權力、政府機關與人民權利之間關係的法律領域。ICO 是否受監督、監管裁量有沒有界線，本質上就是公法問題。

==note end==

---

# 四、Tribunal 為什麼以前幫不上忙？

這裡稍微法律一點，但概念很好懂。

資料當事人向 ICO complaint：

```
我的 GDPR 權利被侵犯
       ↓
ICO 沒有好好處理
       ↓
我去 Tribunal
```

直覺會以為 Tribunal 可以說：

> 「ICO 判斷錯了，重新處理。」

但 **Killock / Veale** 一系列案件採取相當限制性的看法：

Tribunal 主要可以處理 ICO 是否有：

- 回覆 complaint；
- 推進 complaint；

而不是全面重新審查：

> **ICO 對 GDPR infringement 的 substantive merits 判斷是否正確。**

作者簡報引用 Upper Tribunal 的核心思路就是：

> ICO 是 expert regulator，國會沒有讓 Tribunal 變成一個全面重做資料保護監管判斷的機構。[www.slideshare.net](https://www.slideshare.net/slideshow/public-enforcement-of-uk-data-protection-promise-reality-and-future-f89a/277667696?utm_source=chatgpt.com)

白話：

> **法院不是第二個 ICO。**

問題是，這樣就形成 accountability gap。

---

# 五、Delo 案也是關鍵

**Delo v Information Commissioner [2023] EWCA Civ 1141** 對作者很重要。

法院基本上接受：

> ICO 有 duty to handle a complaint，但不代表每一件 complaint 都必須作出一個最終、完整的「有違法／沒有違法」裁判。

ICO 可以：

```
收到個人 complaint
      ↓
不完全處理成 individual enforcement case
      ↓
把資訊拿去支援更廣泛的 industry investigation
```

作者認為這又擴大了 regulator 的 discretion。[www.slideshare.net](https://www.slideshare.net/slideshow/public-enforcement-of-uk-data-protection-promise-reality-and-future-f89a/277667696?utm_source=chatgpt.com)

這裡的核心 tension 是：

### Regulator 的觀點

> 我只有有限資源，要做 strategic enforcement。

vs.

### Data subject 的觀點

> GDPR 明明給我 rights；我 complaint 以後，你卻不告訴我到底有沒有違法？

這個衝突很有研究價值。

---

# 六、作者後來找到一個很重要的反擊武器：CJEU 的 Land Hessen

這裡我認為是全文法律上最重要的一段。

CJEU 在 **TR v Land Hessen, C-768/21** 說：

主管機關確實**不必每次 GDPR infringement 都罰款**。

也就是：

```
GDPR violation
≠ 必定 fine
```

但 supervisory authority 有更大的義務：

> **它必須作出適當反應，以 remedy 已經發現的 infringement。**

完全不使用 corrective powers，應當只是例外，例如：

- 違法已完全被補救；
- 後續 processing 已符合法規；
- 不採 corrective action 不會破壞 GDPR 的 strong enforcement。 [A&L Goodbody](https://www.algoodbody.com/insights-publications/to-act-or-not-to-act-are-supervisory-authorities-obliged-to-take-corrective-action-when-personal-data-has-been-breached?utm_source=chatgpt.com)

這個區別非常重要。

作者不是主張：

> 每一件違法都罰錢。

而是：

> **一旦確認 infringement，regulator 不能把「我有 discretion」理解成「我什麼都可以不做」。**

Article 58 本來就有很多工具：

```
warning
reprimand
order to comply
processing restriction
ban
erasure
fine
```

而不是只有 fine。[Gibraltar Laws](https://www.gibraltarlaws.gov.gi/legislations/regulation-eu-2016679-5582?utm_source=chatgpt.com)

==note start==

這一部分真正處理的是：**ICO 對資料保護申訴到底有多大的執法裁量，以及法院能不能迫使它真正處理違法問題。** 英國先前的 Killock、Veale 與 Delo 等案件，整體上給 ICO 相當大的空間：審裁處與法院通常不會把自己當成「第二個 ICO」，重新判斷每一件申訴究竟有沒有違反 GDPR；ICO 也不必把每一件申訴都處理成完整的個別執法案件。問題是，如果監管裁量過大，就可能出現「人民有資料保護權，但監管機關可以選擇不積極執法」的問責缺口。歐盟法院在 Land Hessen 案則提供了一個重要限制：主管機關雖然可以決定採取哪一種矯正措施，也不必每次都罰款，但一旦確認存在違法，原則上仍必須採取適當措施使違法狀態得到補救。換言之，**監管裁量是決定「如何執法」，而不是取得「可以完全不執法」的自由。**

- **Substantive merits｜實體爭議本身／實體判斷**  
    指案件真正核心的法律問題，例如「企業到底有沒有違反 GDPR」。這和只檢查程序有沒有走、機關有沒有回覆申訴，是不同層次。
- **Accountability gap｜問責缺口**  
    指一個機關雖然掌握很大權力，外部卻缺乏足夠有效的制度去要求它說明、改正或承擔責任。這裡的問題就是：如果 ICO 不積極執法，審裁處和法院又不願深入介入，那誰能真正要求 ICO 改變？
- **Duty to handle a complaint｜處理申訴的義務**  
    ICO 收到資料當事人的申訴後，不能完全置之不理，必須進行一定程度的處理。但這項義務不一定等於必須就每件申訴作出完整的違法認定與正式處分。
- **Industry investigation｜產業性調查**  
    指監管機關不只處理單一公司的問題，而是從大量申訴中發現某個產業的共同風險，再進行較廣泛的調查。這有利於策略性監管，但可能犧牲個別申訴人的即時救濟。
- **Strategic enforcement｜策略性執法**  
    指監管機關因資源有限，把人力集中在影響較大、具有示範效果或涉及整個產業的案件，而不是每一件都深入處理。爭議在於，策略性選擇不能變成長期不處理個人權利侵害的理由。
- **Data subject｜資料當事人**  
    指個人資料所指向的那個人，例如你的姓名、位置、醫療或消費資料被處理時，你就是資料當事人。GDPR 賦予資料當事人許多權利，也允許他們向監管機關申訴。
- **CJEU（Court of Justice of the European Union）｜歐盟法院**  
    負責統一解釋歐盟法的最高司法機關之一。Land Hessen 案的重要性在於，它對資料保護監管機關的執法裁量劃出更清楚的界線。
- **Supervisory authority｜監督機關／資料保護主管機關**  
    GDPR 下負責監督資料保護法執行的獨立機關，例如歐盟各國的資料保護主管機關。它們擁有調查、命令改正、限制處理與裁罰等權力。
- **Corrective powers｜矯正權力**  
    指主管機關用來停止或修正違法狀態的法定工具，包括正式譴責、命令改善、限制或禁止資料處理、命令刪除資料及罰款。重點是，矯正不只有「開罰」一種方式。
- **Remedy an infringement｜補救違法狀態**  
    指不是只認定「你違法了」，而是要讓違法真正停止或被修正。例如命令停止處理資料、刪除違法取得的資料，或改正未來的資料處理方式。
- **Order to comply｜命令遵法／命令改善**  
    主管機關要求組織在一定期限內把資料處理方式改到符合法律要求。它的重點不是懲罰，而是直接使違法狀態停止。
- **Processing restriction｜限制資料處理**  
    主管機關限制企業繼續使用、分析、傳輸或處理某些個人資料。對高度依賴資料的企業而言，這有時甚至比罰款更有實際壓力。
- **Ban｜禁止處理**  
    主管機關可以直接禁止某種資料處理活動繼續進行。這是相當強的矯正措施，可能直接影響企業某項服務或商業模式能否繼續運作。
- **Erasure｜刪除資料**  
    主管機關可以要求違法持有或處理的個人資料被刪除。這種措施直接處理違法後果，而不是只處以金錢制裁。
- **Strong enforcement｜強力執法**  
    指 GDPR 的權利與義務不能只停留在紙面上，監管機關必須有能力且願意透過實際措施確保法律被遵守。它不等於「每件案件都重罰」，而是違法不能長期沒有實際後果。
- **Enforcement discretion ≠ enforcement freedom｜執法裁量不等於不執法的自由**  
    主管機關可以選擇「用哪一種方法處理違法」，但不代表在確認違法後仍可任意完全不處理。這正是 Land Hessen 對監管裁量最重要的限制。

==note end==

---

# 七、這產生一個很漂亮的法理問題

可以寫成：

\[ \text{Enforcement discretion} \neq \text{Enforcement freedom} \]

主管機關可以決定：

> **怎麼執法。**

但未必可以決定：

> **要不要執法。**

這就是作者想拿 CJEU case law 去壓縮 ICO discretion 的地方。

而因為英國已 Brexit，後 Brexit 的 CJEU 判決對 UK court 並非簡單地直接具有 EU membership 時期那種拘束效果；所以文章很謹慎地把它稱作具有 **persuasive** 意義的 Court of Justice jurisprudence。[科學直接](https://www.sciencedirect.com/science/article/pii/S2212473X26001367?utm_source=chatgpt.com)

這一塊其實很適合公法研究。

---

# 八、Data (Use and Access) Act 又讓事情更複雜

作者對英國近年的方向並不樂觀。

Data (Use and Access) Act 改造 ICO／Information Commission 的制度環境，同時給 regulator 一些與：

- innovation；
- competition；
- economic growth；

相關的新考量。

作者擔心：

```
data protection
      ↑
      │ tension
      ↓
innovation / growth / competition
```

原本已經採取「pragmatic / proportionate regulation」的 ICO，可能更容易將強制執法往後排。

英國近年的政策方向也確實強調 regulator 對 growth 的支持；相關法制改革同時擴張部分資料使用與 automated decision-making 的空間。[英國立法網](https://www.legislation.gov.uk/ukpga/2018/12/schedules/2026-01-05?view=plain&utm_source=chatgpt.com)

這不是說：

> growth duty 一定導致少執法。

而是作者認為：

> **它增加了一組與 rights enforcement 潛在衝突的 statutory considerations。**

這個論證是合理的，但目前還比較屬於 **institutional prediction**，尚不能說已經被 empirical evidence 證明。

---

# 九、Afghan data breach 是作者抓到的「制度失靈個案」

文章特別提 2022 Afghan data breach。

這件事的重要性不是 breach 本身而已，而是：

> 重大政府資料事件發生之後，ICO 竟沒有進行作者認為應有的調查。

事件曝光後，英國國會 **Science, Innovation and Technology Committee** 承諾加強對 ICO 的 oversight。[科學直接](https://www.sciencedirect.com/science/article/pii/S2212473X26001367?utm_source=chatgpt.com)

所以作者看到一線希望：

```
ordinary complaints
→ accountability mechanisms 沒什麼反應

重大政治事件
→ Parliament 開始注意 regulator 本身
```

這也揭露一個 public-law 問題：

> **監督機關的 accountability 是否必須等 scandal 才啟動？**

==note start==

這一部分真正要處理的是：**監管機關的裁量到底有沒有底線，以及制度是否反而正在增加 ICO 不積極執法的理由。** 作者藉由歐盟法院判例提出一個重要區分：監管機關可以選擇「如何執法」，但不應因此取得「完全不執法」的自由；然而英國脫歐後，歐盟法院的新判決對英國法院主要只具有參考與說服作用，能否真正限制 ICO 仍有不確定性。另一方面，英國新的資料法制又要求監管機關更多考量創新、競爭與經濟成長，可能使資料保護與權利執法在制度上面臨更強的政策拉扯。Afghan data breach 則進一步顯示，當日常監督機制長期沒有有效約束 ICO 時，往往要等到重大事件或政治醜聞出現，國會才開始強化對監管機關本身的監督；這正凸顯一個公法上的核心問題：**監管機關是否受到持續、制度化的問責，還是只有在危機爆發後才被追究責任。**

**真正成熟的監管制度，不只要限制被監管者，也要建立能持續限制監管者本身的機制；否則裁量很容易從「如何執法」滑向「是否執法」。**

- **CJEU case law｜歐盟法院判例**  
    指歐盟法院對歐盟法作出的判決與解釋。這些判例在歐盟成員國具有重要拘束力，但英國脫歐後，新判決對英國法院通常不再像過去那樣直接具有同等拘束效果。
- **Brexit｜英國脫歐**  
    指英國退出歐盟。對這篇而言，最重要的法律效果是：英國法院現在如何使用新的歐盟法院判例，已不同於英國仍是歐盟成員國時期。
- **Persuasive authority｜具有說服力的法律資料**  
    指法院**不一定非照著做不可**，但可以拿來參考。這篇裡是說，英國脫歐後，Land Hessen 雖不一定拘束英國法院，仍可以被拿來支持「ICO 的裁量不是無限的」。
- **Court of Justice jurisprudence｜歐盟法院法理／判例法**  
    指歐盟法院透過很多案件慢慢形成的一套法律原則。這篇主要借用這些判例來說明：**監管機關可以選擇怎麼執法，但不能把裁量變成完全不執法。**
- **Data (Use and Access) Act｜《資料使用與存取法》**  
    英國近年的資料法制改革之一，重新調整資料使用、監管及相關制度。作者關心的是，新制度不只要求保護個資，也更強調創新、競爭與經濟成長。
- **Pragmatic regulation｜務實監管**  
    指監管機關不機械式地對每個違法行為都採取最嚴厲措施，而是考量實際效果、資源與政策目標。優點是彈性，風險則是「務實」可能逐漸變成低度執法的理由。
- **Proportionate regulation｜比例原則式監管**  
    指監管措施的強度應和違法程度、風險與損害相稱，不能為了小問題採取過度嚴厲的手段。問題在於，「比例」如果被解釋得過度寬鬆，也可能被用來合理化不採取正式措施。
- **Rights enforcement｜權利執行／權利保障的實際落實**  
    指個人在法律上享有的資料保護權，不只存在於法條中，還能透過監管與救濟真正被實現。作者擔心其他政策目標可能壓縮這種權利執行。
- **Statutory considerations｜法定應考量事項**  
    指法律明文要求監管機關在作決定時必須納入考慮的因素，例如創新、競爭或成長。這些因素不一定決定結果，但會影響監管機關如何平衡不同公共利益。
- **Institutional prediction｜制度性預測**  
    指根據法律制度與機關誘因推測未來可能發生的監管行為，而不是已經被數據證實的結果。作者對新法可能削弱強力執法的擔心，目前主要屬於這一層次。
- **Empirical evidence｜實證證據**  
    指透過統計、案件資料、調查或其他可觀察資料，證明某種現象實際存在。這裡的重點是，目前還不能直接證明新法已經造成 ICO 減少執法。
- **Scandal-driven accountability｜醜聞驅動式問責**  
    指平常監督機制沒有發揮作用，直到重大事件、媒體曝光或政治爭議爆發後，國會或其他機關才開始追究責任。這反映的是制度性監督不足，而不是健康的日常問責。
- **Public law｜公法**  
    研究政府機關如何行使公權力，以及其權力如何被法律與其他制度限制。這一段之所以具有公法價值，就是因為它開始追問「監管者本身如何被監管」。

==note end==

---

# 十、我認為這篇最值得學的是研究結構

你前面覺得那篇 AI definition 很空，我覺得這篇正好可以對照。

它的研究結構非常清楚：

```
① 法律承諾
GDPR = strong enforcement
effective + proportionate + dissuasive sanctions
          ↓

② 實證現象
43,000+ complaints/year
vs
6.8 fines/year
1.8 enforcement actions/year
          ↓

③ 排除簡單解釋
不是只看 fine
→ 看其他 corrective mechanisms
          ↓

④ 找制度原因
ICO 有很大的 enforcement discretion
          ↓

⑤ 找 accountability architecture
Tribunal
Court
Ombudsman
Parliament
EHRC
          ↓

⑥ 發現 second-order failure
監督 regulator 的制度也不強
          ↓

⑦ 尋找 doctrinal leverage
Land Hessen
Article 58
strong enforcement obligation
          ↓

⑧ 研究未來
能不能用 judicial / parliamentary oversight
把 discretion 拉回來？
```

**這是一篇「empirical observation → institutional diagnosis → doctrinal solution」的文章。**

這個結構非常值得學。

==note start==

這篇最值得學的，不只是它批評 ICO 執法不足，而是它的**研究推理很完整**：先從 GDPR 所要求的強力執法出發，再用實際數據證明英國執法與法律承諾之間存在落差；接著排除「只是罰款少」這種過度簡單的解釋，轉而檢查其他矯正措施、ICO 的執法裁量，以及法院、國會、監察機關等外部監督是否有效。最後，作者再利用 Land Hessen 與 GDPR 第 58 條，尋找能夠限制 ICO 過度裁量的法律依據。也就是說，這篇不是先有結論再找材料，而是沿著「**發現問題 → 找制度原因 → 檢查監督失靈 → 尋找法律解法**」一步一步把論證鎖緊。

- **Strong enforcement｜強力執法**  
    指法律不能只寫得嚴格，主管機關還必須真的調查、要求改善或處分違法者，讓法律產生實際效果。
- **Effective, proportionate and dissuasive sanctions｜有效、合比例且具有嚇阻力的制裁**  
    制裁要真的能讓違法停止，又不能重到不合理，同時還要讓企業知道違法是有代價的。
- **Empirical observation｜實證觀察**  
    不是只靠理論推測，而是先看真實世界的數據和案件。這篇就是用申訴數、罰款數、執法命令數來確認「執法不足」是不是實際存在。
- **Corrective mechanisms｜矯正機制**  
    指主管機關用來修正違法的各種工具，例如正式譴責、命令改善、限制資料處理或罰款。作者因此不是只看「罰了多少錢」。
- **Accountability architecture｜問責架構**  
    指整套「誰來監督監管機關」的制度設計，例如法院、審裁處、國會、監察專員等。作者不是只看單一機關，而是看整個監督網絡有沒有發揮作用。
- **Second-order failure｜第二層監督失靈**  
    第一層是 ICO 沒有有效監督企業；第二層則是本來應該監督 ICO 的機構，也沒有有效糾正它。也就是「監管者失靈，監督監管者的制度也失靈」。
- **Doctrinal leverage｜法律上的突破口／可運用的法理依據**  
    指作者找到可以用來推動法律論證的判例、法條或原則。這篇主要用 Land Hessen 和 GDPR 第 58 條來限制 ICO 把裁量解釋得過度寬鬆。
- **Institutional diagnosis｜制度診斷**  
    不只是說「結果不好」，而是進一步找出是哪個制度設計造成問題，例如裁量太大、司法審查太弱、國會監督不足。
- **Doctrinal solution｜法律上的解決方向**  
    指利用既有法條、判例與法律原則，提出如何限制或修正監管機關的作法。它不一定等於立刻修法，也可能是重新解釋現有法律。
- **Judicial oversight｜司法監督**  
    指法院透過訴訟或司法審查，檢查 ICO 是否依法履行職責、是否濫用裁量。
- **Parliamentary oversight｜國會監督**  
    指國會透過委員會、調查、聽證或報告，要求 ICO 說明它的執法政策與成效。

==note end==

---

# 十一、不過，這篇也不是沒有問題

這裡我要稍微拆作者的論證。

## 1. 「complaints 很多、fines 很少」不能直接推出執法不足

這是最大 methodological issue。

43,000 件 complaint 不等於：

> 43,000 件 proven violation。

裡面可能很多是：

- 不成立；
- 輕微；
- 重複；
- 私人糾紛；
- 已自行改善；
- 不適合 formal enforcement。

因此：

\[ \frac{\text{fines}}{\text{complaints}} \]

**不是一個真正的 enforcement rate。**

這個比例只能當 warning signal。

---

## 2. Fine count 不是最好的 enforcement metric

例如：

```
Regulator A
一年罰 300 個小案件

Regulator B
一年只辦 10 件
但每件都打大型平台
並造成 whole-industry compliance
```

你不能只看數量說 A 比 B 強。

真正理想的研究應該看：

\[ \text{enforcement effectiveness} \]

而不是：

\[ \text{number of enforcement actions} \]

這篇沒有真的建立這個 metric。

---

## 3. 缺乏 counterfactual

作者證明：

> UK enforcement 很少。

但更難的問題是：

> **如果 ICO 多開 100 件 enforcement notice，英國的 data protection compliance 真的會變好多少？**

文章沒有辦法回答。

所以它是：

> **descriptive + doctrinal institutional study**

不是 causal empirical study。

---

## 4. 德國、西班牙比較需要 normalization

作者簡報拿 Germany 約 **351 fines/year**、Spain 約 **266 fines/year** 跟 UK 比，視覺效果非常強。[www.slideshare.net](https://www.slideshare.net/slideshow/public-enforcement-of-uk-data-protection-promise-reality-and-future-f89a/277667696?utm_source=chatgpt.com)

但更嚴格的 comparative study 應該控制：

- population；
- number of controllers；
- complaint volume；
- regulator structure；
- national procedural rules；
- fine-recording practices；
- types of infringement。

否則：

```
351 vs 6.8
```

很 shocking，

但還不是精確的 comparative enforcement index。

==note start==

這篇最大的限制，不在法律分析，而在**實證方法還不足以證明「ICO 執法真的不有效」**。作者用「申訴很多、正式處分很少」來顯示可能存在執法不足，這是一個很有力的警訊，但不能直接等同於執法失敗，因為不是每件申訴都成立，也不能只用罰款件數衡量監管效果；少數大型案件有時可能比大量小額處分更有嚇阻力。更進一步，文章也沒有建立因果關係，無法證明「如果 ICO 多執法，整體資料保護遵法程度就一定會提高」。跨國比較同樣需要控制人口、申訴量、監管制度與統計方式等差異。因此，這篇比較適合被理解成：**它有力地提出了「可能存在結構性低度執法」的證據，但還沒有建立一個能精確衡量執法效果、甚至證明因果關係的實證模型。**

**這篇很成功地證明「英國執法少得值得懷疑」，但還沒有完全證明「英國執法一定無效」，更沒有證明「增加執法就一定能改善守法」。**

- **Methodological issue｜研究方法上的問題**  
    指研究從資料推到結論的方式可能不夠嚴謹。這裡的問題是，「申訴多、罰款少」不一定就能直接證明執法不足。
- **Proven violation｜已確認的違法案件**  
    指經過調查後，真的被認定違反資料保護法的案件。申訴只是有人提出問題，不代表最後一定成立。
- **Enforcement rate｜執法率**  
    指真正發生違法後，有多少比例最後被監管機關正式處理。直接用「罰款件數 ÷ 申訴件數」並不精確，因為申訴不等於已確認違法。
- **Warning signal｜警訊**  
    指某個數據很值得注意，但還不足以單獨證明結論。43,000 件申訴對上極少正式處分，就是一個需要進一步調查的警訊。
- **Enforcement metric｜執法衡量指標**  
    指用什麼數據判斷監管機關執法強不強。只看罰款件數太粗糙，還可能要看案件影響、改善效果、產業嚇阻力等。
- **Enforcement effectiveness｜執法有效性**  
    指執法是否真的讓違法減少、企業改善、權利得到保障。它比單純計算「辦了幾件案件」更重要，也更難測量。
- **Whole-industry compliance｜整個產業的守法改善**  
    指監管機關可能只處理一兩個大型案件，卻讓整個產業都改變做法。這種效果不能只靠案件數量看出來。
- **Counterfactual｜反事實情境**  
    指問「如果事情換一種做法，結果會怎樣？」例如，如果 ICO 多發布 100 件執法命令，企業的守法程度到底會不會真的提高。
- **Causal empirical study｜因果實證研究**  
    不只是描述兩件事同時出現，而是要證明「A 真的造成 B」。這篇能證明 ICO 正式執法很少，但不能證明增加執法一定會提升整體遵法程度。
- **Descriptive study｜描述性研究**  
    主要回答「現在實際上發生了什麼」。例如統計 ICO 每年收到多少申訴、開多少罰單。
- **Doctrinal study｜法釋義／法理研究**  
    主要透過法條、判例與法律原則分析「法律要求什麼」。這篇在 ICO 裁量與監督義務上就是典型的法釋義分析。
- **Institutional study｜制度研究**  
    關注不同機關如何互動，例如 ICO、法院、國會、審裁處之間如何互相監督。重點不是單一法條，而是整體制度是否有效運作。
- **Normalization｜標準化比較**  
    跨國比較時，要先把人口、案件量、制度結構等差異納入考量，才不會只是直接比原始數字。否則「德國 351 件、英國 6.8 件」雖然差很多，也不代表兩國執法強度可以直接相比。
- **Number of controllers｜受監管的資料控制者數量**  
    指一個國家有多少企業或機關實際負責決定個資怎麼被使用。受監管對象越多，理論上可能自然出現更多案件與處分。
- **National procedural rules｜各國程序規則**  
    指不同國家的申訴、調查、處分程序可能不同。這些差異會影響案件數量，因此跨國比較不能只看表面統計。
- **Fine-recording practices｜罰款統計方式**  
    各國可能用不同方式記錄罰款，例如一案多罰算一件還是多件。若統計口徑不同，直接比較數字就可能失真。
- **Comparative enforcement index｜比較性執法指數**  
    指把人口、案件量、監管資源、處分類型與影響等因素整合成較公平的跨國執法比較。這篇沒有真正建立這種指標。

==note end==

---

# 十二、所以我怎麼評價這篇？

如果沿用我們剛才對上一篇的標準，我會給：

|面向|評價|
|---|---|
|問題意識|**9/10**|
|法律分析|**8.5/10**|
|資料使用|**8/10**|
|Institutional analysis|**9/10**|
|Empirical rigor|**6.5/10**|
|Causal inference|**3/10**|
|概念創新|**6/10**|
|Public-law value|**9/10**|
|Technology-law value|**8/10**|
|研究延伸空間|**9/10**|

它不是一篇「技術法」論文。

但是我反而認為它是一篇**很扎實的 technology regulation / public law 論文**。

因為 technology law 不一定要去解 neural network。

有時候最重要的問題就是：

> **法律制定得再漂亮，如果 regulator 不執行，technology regulation 到底還剩什麼？**

---

# 十三、這篇對 AI Act 其實非常有啟發

雖然文章談的是 GDPR，而不是 AI Act，但我覺得這反而是你最值得留下來的部分。

今天大家一直研究：

```
AI Act
high-risk AI
GPAI
systemic risk
conformity assessment
human oversight
transparency
```

可是更基本的問題是：

> **誰來執行？**

未來完全可能出現：

```
AI Act
規則非常漂亮
       ↓
主管機關人力不足
       ↓
不願正式認定 violation
       ↓
大量 soft guidance
       ↓
極少 corrective action
       ↓
企業逐漸知道 enforcement probability 很低
```

此時真正決定法規效果的是：

\[ \text{Expected regulatory cost} = P(\text{detection}) \times P(\text{enforcement}|\text{detection}) \times \text{sanction} \]

大家一直看最後面的：

> **最高可以罰多少？**

但 Erdos 這篇其實提醒：

如果

\[ P(\text{enforcement}) \approx 0 \]

那：

> **4% global turnover 看起來再可怕也沒多大意義。**

這個觀念我認為非常值得帶到 AI governance。

==note star==

Erdos 這篇雖然研究的是 GDPR，但對 AI Act 的啟發很直接：**真正決定科技監管效果的，不只是法律規定多完整、最高罰則多重，而是違法後到底有多大機率會被發現、被正式認定，並受到實際矯正或處分。** AI Act 即使設計了高風險 AI、透明義務、人工監督、符合性評估與高額罰款，如果主管機關缺乏人力、長期依賴非正式指引，或很少真正啟動執法程序，企業最終感受到的違法成本仍可能很低。因此，研究 AI 治理不能只看「規則寫了什麼」，還要追問：**誰負責執行、執法機率多高、監管機關是否有足夠能力與意願使用法律工具。**

**AI Act 真正的威力，不是看最高可以罰多少，而是企業有多相信：違法真的會被發現、被認定，而且會產生實際後果。**

- **GPAI（General-Purpose AI）｜通用型 AI**  
    指可以處理很多不同任務，而不是只為單一用途設計的 AI 模型，例如可用於寫作、程式設計、分析與問答的大型模型。
- **Systemic risk｜系統性風險**  
    指某些強大 AI 模型的影響不只限於單一使用者，而可能擴散到整個市場、資訊環境、安全或社會制度。
- **Conformity assessment｜符合性評估**  
    指 AI 系統上市或使用前，要檢查它是否符合 AI Act 要求。白話就是先做一套「這個系統有沒有依法達標」的檢驗。
- **Human oversight｜人工監督**  
    指重要 AI 決策不能完全交給系統，人仍應有能力理解風險、介入、停止或推翻 AI 的結果。
- **Transparency｜透明義務**  
    指使用者或監管機關至少要知道自己正在與 AI 互動、系統如何被使用，以及必要的風險與限制資訊。
- **Soft guidance｜軟性指引**  
    指主管機關發布的指南、建議或最佳實務，通常不像法律命令那樣具有直接強制力。它可以幫助守法，但不能完全取代正式執法。
- **Corrective action｜矯正措施**  
    指主管機關發現問題後，要求企業停止、修改或補救違法行為，例如限制系統使用、命令改善或課予罰款。
- **Enforcement probability｜執法機率**  
    指企業違法後，真正被監管機關調查並採取措施的可能性。即使法律最高罰很重，如果實際被執法的機率非常低，嚇阻效果仍可能有限。
- **Detection probability｜被發現的機率**  
    指違法行為被主管機關、稽核機制或申訴制度發現的可能性。如果違法很難被察覺，再嚴格的罰則也不容易產生效果。
- **Expected regulatory cost｜預期監管成本**  
    指企業在決定是否違法時，實際會考慮的「預期代價」。可以簡單理解成：**被發現的機率 × 被執法的機率 × 執法後的代價**。
- **Sanction｜制裁／處罰**  
    指違法後可能承受的法律後果，例如罰款、限制使用、停售或其他處分。它只是監管威嚇力的一部分，不是全部。
- **Global turnover｜全球營業額**  
    指企業在全球範圍內的營收。很多歐盟科技法規會用全球營業額的一定比例作為最高罰款上限，讓大型跨國企業也會感受到處罰壓力。

==note end==

---

# 最後濃縮成一段筆記

> **Erdos 的核心主張不是英國 ICO「罰得不夠多」，而是 GDPR 所承諾的 strong enforcement 與英國實際監管之間出現結構性落差：在每年超過四萬件 complaints 的背景下，ICO 長期僅採取極少數 fines 與 formal enforcement notices，而 Tribunal、judicial review、Ombudsman、Parliament 與 EHRC 又未能形成有效的 second-order accountability。文章因此把問題從「資料控制者是否守法」推進到「監管者是否履行執法義務」。CJEU 的 Land Hessen 提供了一個重要法理突破：supervisory authority 對選擇何種 corrective measure 擁有 discretion，但 discretion 並不等於可以完全不作為；一旦確認 infringement，原則上必須採取適當措施確保有效執法。這篇真正重要的啟示是：technology regulation 的效力不只取決於 substantive rules 有多嚴格，更取決於 regulator 的 enforcement probability，以及誰能對 regulator 本身進行有效 accountability。** [科學直接](https://www.sciencedirect.com/science/article/pii/S2212473X26001367?utm_source=chatgpt.com)

如果跟你剛才那篇 **What are AI systems?** 放在一起，我反而會認為**這篇更值得細讀**：上一篇主要是在換一個 conceptual lens；這篇是真的抓到一個制度現象，再用資料、行政法與判例把它拆開。