6.5840 2025 Lecture 1: Introduction MIT Distributed Systems Engineering 
MIT 6.824 分散式系統課程：講義 1 概述

What I mean by "distributed system":　a group of computers cooperating to provide a service
所謂的「分散式系統」是什麼意思：一組電腦合作提供服務

Examples
  we all use distributed systems, popular apps' back-ends, e.g. for messaging、big web sites、 phone system
    我們都使用分佈式系統，流行應用程序的後端，例如用於消息傳遞、大型網站、電話系統
  focus here is distributed infrastructure: 這裡的重點是分佈式基礎設施
    storage 存儲
    transaction systems 交易系統
    "big data" processing frameworks “大數據”、處理框架
    authentication services 身份驗證服務

It's not easy to build systems this way: 這樣建系統並不容易
  concurrency 並發性
  complex interactions 複雜的交互作用
  performance bottlenecks 效能瓶頸
  partial failure 部分故障

So why do people build distributed systems? 那麼為什麼要建立分散式系統呢？
  to increase capacity via parallel processing 通過並行處理增加處理量
  to tolerate faults via replication　通過複製容許故障(容錯)
  to match distribution of physical devices e.g. sensors 匹配實體設備（例如感測器）的分佈
  to increase security via isolation 通過隔離提高安全性

Why study this topic? 為什麼要研讀這個主題？
  interesting -- hard problems, powerful solutions 有趣——難題，強大的解決方案
  widely used -- driven by the rise of big Web sites 廣泛使用——由大型網站的興起推動
  active research area -- important unsolved problems 活躍的研究領域——未解決的重要問題
  challenging to build -- you'll do it in the labs 構建具有挑戰性——您將在實驗室中完成

COURSE STRUCTURE  課程結構
http://pdos.csail.mit.edu/6.5840
Course staff: 課程工作人員
  Frans Kaashoek and Robert Morris, lecturers 講師
  Kenneth Choi, TA
  Yun-Sheng Chang, TA
  Ivy Wu, TA
  Aryan Kumar, TA
Course components : lectures, papers, two exams, labs, final project (optional)
課程組成部分：講座、試卷、兩次考試、實驗室、期末專案（可選）

Lectures : big ideas, paper discussion, lab guidance
講座：大創意、論文討論、實驗室指導

Papers: 論文
  one per lecture 每堂課一篇論文
  research papers, some classic, some new 每場講座對應一篇研究論文，包含經典和新近論文
  problems, ideas, implementation details, evaluation 內容涵蓋：論文討論問題、想法、實現細節和評估
  please read papers before class! 請在課前閱讀指定論文
  web site has a short question for you to answer about each paper 網站上針對每篇論文提供一個簡短問題，需回答
  and we ask you to send us a question you have about the paper　請提交一個你對該論文的問題
  submit answer and question before start of lecture 在講座開始前提交答案和問題

Exams:
  Mid-term exam in class 課堂期中考
  Final exam during finals week 在最後一周期末考試
  Mostly about papers and labs 主要關於論文和實驗室
  You must attend the exams! 你必須參加考試！

Labs:
  goal: use and implement some important techniques 使用和實施一些重要技術
  goal: experience with distributed programming 分散式程式設計經驗
  first lab is due a week from Friday 第一個實驗室從星期五開始一周到期
  one per week after that for a while 之後每週一次，持續一段時間

Lab 1: distributed big-data framework (like MapReduce)　實驗室 1：分散式大數據框架（如 MapReduce）
Lab 2: client/server vs unreliable network　實驗室 2：客戶端/伺服器 vs. 不可靠網路
Lab 3: fault tolerance using replication (Raft)　實驗室 3：使用複製實現容錯（Raft 算法）
Lab 4: a fault-tolerant database 實驗室 4：容錯資料庫
Lab 5: scalable database performance via sharding 實驗室 5：透過分片提升資料庫可擴展性效能
 
We grade the labs using a set of tests, we give you all the tests; none are secret
評分方式：我們使用一組測試來評分實驗，所有測試均公開，沒有隱藏測試

Optional final project at the end, in groups of 2 or 3. 期末專案（選修）：以 2 或 3 人為一組進行
  The final project substitutes for Lab 5. 期末專案可替代實驗 5
  You think of a project and clear it with us. 需自行構思專案並與我們確認
  Code, short write-up, demo on last day. 需提交程式碼、簡短報告，並在最後一天進行展示

Warning: 注意事項：
  debugging the labs can be time-consuming 實驗室作業除錯可能耗時
  start early 提早開始
  ask questions on Piazza 在 Piazza 上提問
  use the TA office hours 善用助教（TA Teaching Assistant）辦公時間

MAIN TOPICS  主要主題
This is a course about infrastructure for applications.本課程聚焦於應用程式的基礎設施
  * Storage.儲存
  * Communication.通訊
  * Computation.運算

A big goal: hide the complexity of distribution from applications.
核心目標：對應用程式隱藏分散式系統的複雜性

Topic: fault tolerance　主題：容錯
  1000s of servers, big network -> always something broken 在數千台伺服器和大型網絡中，總有部分元件會故障
    We'd like to hide these failures from the application 目標：對應用程式隱藏這些故障
    "High availability": service continues despite failures 高可用性：即使發生故障，服務仍能繼續運行
  Big idea: replicated servers. 核心概念：複製伺服器
    If one server crashes, can proceed using the other(s).若一台伺服器當機，可使用其他伺服器繼續運作

Topic: consistency　主題：一致性
  General-purpose infrastructure needs well-defined behavior.　
    E.g. "read(x) yields the value from the most recent write(x)."
  通用基礎設施需要明確定義的行為：例如：「read(x) 應返回最近一次 write(x) 的值。」
  
  Achieving good behavior is hard!
    e.g. "replica" servers are hard to keep identical.
實現良好行為的挑戰：例如：保持「複製」伺服器完全一致非常困難

Topic: performance　主題：效能
  The goal: scalable throughput　Nx servers -> Nx total throughput via parallel CPU, RAM, disk, net.
  目標：可擴展的吞吐量：N 台伺服器應實現 N 倍總吞吐量，透過並行使用 CPU、記憶體、磁碟和網路
  Scaling gets harder as N grows:　隨著 N 增加，擴展難度加大：
    Load imbalance.負載不平衡
    Slowest-of-N latency.　N 台伺服器中最慢的延遲（slowest-of-N latency）

Topic: tradeoffs
  Fault-tolerance, consistency, and performance are enemies.
  Fault tolerance and consistency require communication
    e.g., send data to backup server
    e.g., check if cached data is up-to-date
    communication is often slow and non-scalable
  Many designs sacrifice consistency to gain speed.
    e.g. read(x) might *not* yield the latest write(x)!
    Painful for application programmers (or users).
  We'll see many design points in the consistency/performance spectrum.
主題：權衡
容錯、一致性和效能相互衝突：
  容錯和一致性需要通訊：
    例如：將數據傳送到備份伺服器。
    例如：檢查快取數據是否為最新。
    通訊通常緩慢且不具可擴展性。
許多設計犧牲一致性以換取速度：
  例如：read(x) 可能無法返回最新的 write(x)！
  這對應用程式開發者（或用戶）來說很麻煩。
我們將探討一致性與效能光譜中的多種設計點。

Topic: implementation 主題：一致性
  RPC, threads, concurrency control, configuration.
  The labs...
主題：實作 RPC、執行緒、並發控制、配置。實驗室......

This material comes up a lot in the real world.
  All big web sites and cloud providers are expert at distributed systems.
  Many open source projects are built around these ideas.
  A hot topic in both academia and industry.
這種材料在現實世界中經常出現。所有大型網站和雲提供商都是分佈式系統的專家。許多開源專案都是圍繞這些想法建立的。學術界和工業界的熱門話題。

CASE STUDY: MapReduce  案例研究：MapReduce

Let's talk about MapReduce (MR)
  a good illustration of 6.5840's main topics
  hugely influential
  the focus of Lab 1
讓我們來談談 MapReduce（MR），它很好地說明了 6.5840 的主要主題，對實驗室 1 的重點影響很大

MapReduce overview
  context: multi-hour computations on multi-terabyte data-sets
    e.g. build search index, or sort, or analyze structure of web
    only practical with 1000s of computers
  big goal: easy for non-specialist programmers
    programmer just defines Map and Reduce functions
    often fairly simple sequential code
  MR manages, and hides, all aspects of distribution!
MapReduce 概述上下文：對數 TB 數據集進行數小時計算，例如構建搜索索引、排序或分析 Web 結構，僅適用於 1000 台計算機 大目標：對於非專業程序員來說很容易，程序員只需定義 Map 和 Reduce 函數，通常相當簡單，順序代碼 MR 管理和隱藏分發的各個方面！

Abstract view of a MapReduce job -- word count
  Input1 -> Map -> a,1 b,1
  Input2 -> Map ->     b,1
  Input3 -> Map -> a,1     c,1
                    |   |   |
                    |   |   -> Reduce -> c,1
                    |   -----> Reduce -> b,2
                    ---------> Reduce -> a,2
  1) input is (already) split into M pieces
  2) MR calls Map() for each input split, produces list of k,v pairs
     "intermediate" data
     each Map() call is a "task"
  3) when Maps are done,
     MR gathers all intermediate v's for each k,
     and passes each key + values to a Reduce call
  4) final output is set of <k,v> pairs from Reduce()s
MapReduce 工作的抽象視圖 -- 字數 Input1 -> Map -> a，1 b，1 Input2 -> Map -> b，1 Input3 -> Map -> a，1 c，1 | | | | | |-> 減少 -> c，1 |-----> 減少 -> b，2 ---------> 減少 -> a，2 1） 輸入（已經）分成 M 塊 2） MR 為每個輸入分割呼叫 Map（），產生 k，v 對「中間」資料列表 每個 Map（） 呼叫都是一個「任務」 3） 當 Maps 完成後，MR 收集每個 k 的所有中間 v，並將每個鍵 + 值傳遞給 Reduce 呼叫 4） 最終輸出是來自 Reduce（） 的 <k，v> 對的集合

Word-count code
  Map(d)
    chop d into words
    for each word w
      emit(w, "1")
  Reduce(k, v[])
    emit(len(v[]))
字數統計代碼 Map（d） 將 d 切碎為每個單詞的單詞 w emit（w， “1”） Reduce（k， v[]） emit（len（v[]））

MapReduce scales well:
  N "worker" computers (might) get you Nx throughput.
    Maps()s can run in parallel, since they don't interact.
    Same for Reduce()s.
  Thus more computers -> more throughput -- very nice!
MapReduce 擴展性很好：N 台“工作”計算機（可能）為您提供 Nx 吞吐量。Maps（） 可以並行運行，因為它們不交互。對於 Reduce（） 也是如此。因此，更多的計算機 - >更高的吞吐量 - 非常好！

MapReduce hides much complexity:
  sending map+reduce code to servers
  tracking which tasks have finished
  "shuffling" intermediate data from Maps to Reduces
  balancing load over servers
  recovering from crashed servers
MapReduce 隱藏了很多複雜性：向伺服器發送 map+reduce 程式碼，追蹤哪些任務已完成從 Maps 的「洗牌」中間資料，以減少從崩潰伺服器恢復的伺服器的平衡負載

To get these benefits, MapReduce restricts applications:
  No interaction or state (other than via intermediate output).
  Just the one Map/Reduce pattern for data flow.
  No real-time or streaming processing.
為了獲得這些好處，MapReduce 限制了應用程式：沒有互動或狀態（透過中間輸出除外）。只有一個資料流的 Map/Reduce 模式。沒有實時或流處理。

Some details (paper's Figure 1)
一些細節（論文的圖 1）

Input and output are stored on the GFS cluster file system
  MR needs huge parallel input and output throughput.
  GFS splits files over many servers, many disks, in 64 MB chunks
    Maps read in parallel
    Reduces write in parallel
  GFS replicates all data on 2 or 3 servers, for fault tolerance
  GFS is a big win for MapReduce
輸入和輸出存儲在 GFS 集群文件系統上，MR 需要巨大的並行輸入和輸出吞吐量。GFS 將檔案分割成 64 MB 區塊，在許多伺服器、許多磁碟上 並行讀取 減少並行寫入 GFS 將所有資料複製到 2 或 3 台伺服器上，用於容錯 GFS 是 MapReduce 的一大勝利

The "Coordinator" manages all the steps in a job.
  1. coordinator gives Map tasks to workers until all Maps complete
     Maps write output (intermediate data) to local disk
     Maps split output, by hash(key) mod R, into one file per Reduce task
  2. after all Maps have finished, coordinator hands out Reduce tasks
     each Reduce task corresponds to one hash bucket of intermediate output
     each Reduce task fetches its bucket from every Map worker
     sort by key, call Reduce() for each key
     each Reduce task writes a separate output file on GFS
「協調員」管理工作中的所有步驟。1. 協調器將 Map 任務交給 workers，直到所有 Maps 完成 Maps 將輸出（中間資料）寫入本機磁碟 Maps 將輸出，透過雜湊（鍵）mod R，拆分為每個 Reduce 任務 2 的一個檔案。在所有 Map 完成後，協調器分發 Reduce 任務，每個 Reduce 任務對應一個中間輸出的雜湊桶，每個 Reduce 任務從每個 Map 工作器中獲取其桶，按鍵排序，為每個鍵調用 Reduce（） 每個 Reduce 任務在 GFS 上寫入一個單獨的輸出文件

What will likely limit the performance?
  We care since that limit is the thing to optimize.
  CPU? memory? disk? network?
  In 2004 authors were limited by network speed.
    What does MR send over the network?
      Maps read input from GFS.
      Reduces fetch Map intermediate output -- the shuffle.
        Often as large as input, e.g. for sorting.
      Reduces write output files to GFS.
    [diagram: servers, tree of network switches]
    In MR's all-to-all shuffle, half of traffic goes through root switch.
    Paper's root switch: 100 to 200 gigabits/second, total
      1800 machines, so 55 megabits/second/machine.
      55 is small: less than disk or RAM speed.
什麼可能會限制性能？我們關心，因為這個限制是需要優化的事情。中央處理器？記性？磁碟？網？2004 年，作者受到網絡速度的限制。MR 通過網絡發送什麼？地圖會讀取 GFS 的輸入。減少獲取地圖中間輸出 -- 隨機播放。通常與輸入一樣大，例如用於排序。減少對 GFS 的寫入輸出檔案。[圖：伺服器、網路交換器樹]在 MR 的全對全部洗牌中，一半的流量通過根交換機。紙張的根開關：100 到 200 Gb/秒，總共 1800 台機器，所以 55 MB/秒/機器。55 很小：低於磁盤或 RAM 速度。

How does MR minimize network use?
  Coordinator tries to run each Map task on GFS server that stores its input.
    All computers run both GFS and MR workers
    So Map input is usually read from GFS data on local disk, not over network.
  Intermediate data goes over network just once.
    Map worker writes to local disk.
    Reduce workers read from Map worker disks over the network.
    (Storing it in GFS would require at least two trips over the network.)
  Intermediate data hash-partitioned into files holding many keys.
    Reduce task unit is a hash bucket (rather than just one key).
    Big network transfers are more efficient.
MR 如何最大限度地減少網路使用？協調器嘗試在儲存其輸入的 GFS 伺服器上執行每個地圖工作。所有電腦都同時執行 GFS 和 MR Worker 因此，Map 輸入通常是從本機磁碟上的 GFS 資料讀取的，而不是透過網路讀取。中間資料僅通過網路一次。對應背景工作角色寫入本機磁碟。減少透過網路從 Map Worker 磁碟讀取的背景工作角色。（將其存儲在 GFS 中至少需要通過網絡進行兩次旅行。中間資料雜湊分割成包含許多索引鍵的檔案。Reduce 任務單元是一個雜湊儲存桶（而不僅僅是一個鍵）。大型網路傳輸效率更高。

How does MR get good load balance?
  Why do we care about load balance?
    If one server has more work than others, or is slower,
    then other servers will lie idle (wasted) at the end, waiting.
  But tasks vary in size, and computers vary in speed.
  Solution: many more tasks than worker machines.
    Coordinator hands out new tasks to workers who finish previous tasks.
    So faster servers do more tasks than slower ones.
    And slow servers are given less work, reducing impact on total time.
MR 如何獲得良好的負載平衡？為什麼我們關心負載平衡？如果一台伺服器的工作量比其他伺服器多，或者速度較慢，那麼其他伺服器將在最後閒置（浪費）等待。但任務的大小各不相同，電腦的速度也各不相同。解決方案：比工作機器多得多的任務。協調員將新任務分發給完成先前任務的工作人員。因此，速度較快的伺服器比較慢的伺服器執行更多任務。速度較慢的伺服器的工作量更少，從而減少對總時間的影響。

What about fault tolerance?
  What if a worker computer crashes?
  We want to hide failures from the application programmer!
  Does MR have to re-run the whole job from the beginning?
    Why not?
  MR re-runs just the failed Map()s and Reduce()s.
容錯呢？如果員工電腦當機怎麼辦？我們想向應用程式設計師隱藏失敗！MR 是否必須從頭開始重新執行整個工作？為什麼不呢？MR 只會重新執行失敗的 Map（） 和 Reduce（）。

Suppose MR runs a Map task twice, one Reduce sees first run's output,
    but another Reduce sees the second run's output?
  The two Map executions had better produce identical intermediate output!
  Map and Reduce should be pure deterministic functions:
    they are only allowed to look at their arguments/input.
    no state, no file I/O, no interaction, no external communication,
      no random numbers.
  Programmer is responsible for ensuring this determinism.
假設 MR 執行 Map 工作兩次，一個 Reduce 看到第一次執行的輸出，但另一個 Reduce 看到第二次執行的輸出？兩個地圖執行最好產生相同的中間輸出！Map 和 Reduce 應該是純粹的確定性函數：它們只允許查看它們的參數/輸入。沒有狀態，沒有文件 I/O，沒有交互，沒有外部通信，沒有隨機數。程式設計師負責確保這種確定性。

Details of worker crash recovery:
  * a worker crashes while running a Map task:
    coordinator notices worker is not responding
    coordinator knows which Map tasks ran on that worker
      those tasks' intermediate output is now lost, must be re-created
      coordinator tells other workers to run those tasks
    can omit re-running if all Reduces have fetched the intermediate data
  * a worker crashes while running a Reduce task:
    finished tasks are OK -- stored in GFS, with replicas.
    coordinator re-starts worker's unfinished tasks on other workers.
Worker 當機復原的詳細資料： * Worker 在執行 Map 工作時當機：協調器注意到 Worker 沒有回應 協調器知道在該 Worker 上執行了哪些 Map 工作 這些工作的中間輸出現在已遺失，必須重新建立 協調器告訴其他 Worker 執行這些工作，如果所有 Reduce 都擷取了中間資料，則可以省略重新執行 * Worker 在執行 Reduce 工作時崩潰： 已完成的任務沒問題 -- 儲存在 GFS 中，並有副本。協調員在其他工作人員上重新啟動工作人員未完成的任務。

Other failures/problems:
  * What if the coordinator gives two workers the same Map() task?
    perhaps the coordinator incorrectly thinks one worker died.
    it will tell Reduce workers about only one of them.
  * What if the coordinator gives two workers the same Reduce() task?
    they will both try to write the same output file on GFS!
    atomic GFS rename prevents mixing; one complete file will be visible.
  * What if a single worker is very slow -- a "straggler"?
    perhaps due to flakey hardware.
    coordinator starts a second copy of last few tasks.
  * What if a worker computes incorrect output, due to broken h/w or s/w?
    too bad! MR assumes "fail-stop" CPUs and software.
  * What if the coordinator crashes?
其他失敗/問題： * 如果協調器給兩個工作者相同的 Map（） 任務怎麼辦？也許協調員錯誤地認為一名工人死亡。它只會告訴 Reduce 員工其中一個。* 如果協調器給兩個工作者相同的 Reduce（） 任務怎麼辦？它們都會嘗試在 GFS 上寫入相同的輸出檔案！原子 GFS 重新命名可防止混合;將看到一個完整的文件。* 如果一個工人非常慢——一個“散兵”怎麼辦？也許是由於硬件不穩定。協調器會啟動最後幾個任務的第二個副本。* 如果工作人員由於硬件或軟件損壞而計算出不正確的輸出怎麼辦？太糟糕了！MR 假設「故障停止」CPU 和軟體。* 如果協調員崩潰怎麼辦？

Performance?
  Figure 2
  X-Axis is time
  Y-Axis is total rate at which a "grep"-style job reads its input
  A terabyte (1000 GB) of input
  1764 workers
  30,000 MB/s (30 GB/s) is huge!
  Why 30,000 MB/s?
    17 MB/s per worker machine -- 140 megabits/second
    more than our guess (55 mbit/s) of net bandwidth
    input probably read direct from local GFS disk
    so disks probably could read at about 17 MB/second
  Why is the main period of activity about 30 seconds?
  Why does it take 50 seconds for throughput to reach maximum?
演奏？圖 2 X 軸是時間 Y 軸是「grep」式作業讀取其輸入的總速率 輸入 1000 TB （1764 個工作程序） 30,000 MB/s （30 GB/s） 是巨大的！為什麼是 30,000 MB/s？每台工作機器 17 MB/s -- 比我們猜測的淨頻寬輸入 （55 mbit/s） 多 140 MB/秒 可能直接從本機 GFS 磁碟讀取，因此磁碟可能以大約 17 MB/秒的速度讀取 為什麼主要活動週期約為 30 秒？為什麼輸送量需要 50 秒才能達到最大值？

Current status?
  Hugely influential (Hadoop, Spark, &c).
  Probably no longer in use at Google.
    Replaced by Flume / FlumeJava (see paper by Chambers et al).
    GFS replaced by Colossus (no good description), and BigTable.
目前狀態？影響力巨大（Hadoop、Spark 等）。谷歌可能不再使用。被 Flume / FlumeJava 取代（參見 Chambers 等人的論文）。GFS 被 Colossus（沒有好的描述）和 BigTable 取代。

Conclusion
  MapReduce made big cluster computation popular.
  - Not the most efficient or flexible.
  + Scales well.
  + Easy to program -- MR hides failures and data movement.
  These were good trade-offs in practice.
  We'll see some more advanced successors later in the course.
  Have fun with Lab 1!

結論 MapReduce 使大集群計算流行起來。- 不是最有效或最靈活的。+ 擴展性好。+ 易於編程 - MR 隱藏故障和數據移動。這些在實踐中都是很好的權衡。我們將在課程後面看到一些更高級的繼任者。玩得開心 實驗室 1！
