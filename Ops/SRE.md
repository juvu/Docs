# SRE 站点可靠性工程(Site Reliability Engineering)：

## 黄金指标

称为“黄金”指标，最重要的原因之一在于其直接衡量的事物会对系统的最终用户及工作生产环节产生影响——换言之，其是在直接衡量最关键的对象。

* 来自谷歌 SRE 书: 延迟、流量、错误以及饱和度；
* USE 方法(来自 Brendan Gregg): 利用率、饱和度与错误；
* RED 方法(来自 Tom Wilkie): 速率、错误与持续时间。

### 指标

* 速率：请求速率，请每秒请求数量。
* 错误： — 错误率，即每秒错误数量。
* 延迟：— 响应时间，包括队列 / 等待时间，以毫秒为单位。
* 饱和度：—即过载程度，这项指标与资源利用率相关，但也可通过队列深度（或者并发水平）等方式进行直接衡量。从队列深度的角度来理解，当系统逐渐趋于饱和时，队列深度将由零变为非零。饱和度指标通常体现为计数器形式。
* 利用率： — 资源或系统的繁忙程度。通常表示为 0% 至 100%，这项指标对预测而言非常重要（同样重要的还有饱和度指标）。请注意，这里我们并没有使用“利用率法则”的定义，即速率 x 服务时间 / 工作程序，而是选择了大家更为熟悉的直接衡量指标。

### 判断标准

* 警报：当问题出现时，及时向我们上报。
    - 警报机制利用静态阈值对我们的 Nagios、Zabbix 以及 DataDog 等系统进行监控。在设定方面难度较大，且往往会产生大量噪声
    - 不要忘记设置下限警报，例如每秒近零请求或延迟等，这些通常都意味着问题的存在（包括凌晨 3 点这类低流量时段）。
    - 中位值通常不会受到过大 / 过小偏离值的影响。
    - 百分位数。举例来说，大家可以对位于第 95 百分位上的延迟值进行追踪，这将更好地衡量用户的实际体验。如果第 95 百分位延迟较为理想，则可认定绝大多数用户都具备良好的使用体验。
    - 异常检测非常适用于捕捉非峰值期间或引发低指标值的问题。此外，异常检测还能够提供更为密集的警报提醒，从而降低问题发现难度（但不要介入太早，否则大家可能遭遇误报困境）。Datadog 以及 SignalFX 等新一代 SaaS/ 云监控解决方案能够实现上述目标，而 Prometheus 以及 InfluxDB 等内部系统也拥有这样的检测能力。
    - Weave Works 就拥有一套包含两个图形列的良好格式，Splunk 则提供出色的视图。界面左侧为请求与错误率堆叠图，右侧则为延迟图。
    - 工程师必须把所有线索连接起来，并对警报信息进行全面挖掘——即使其单纯体现为高 CPU 占用率或低内存余量。然而，黄金指标的抽象程度通常更高，且往往会一次性出现大量此类指标——具体来讲，来自某一低级别服务的单一高延迟问题，亦可能导致系统层面的大范围延迟及错误警报。
* 故障排查：帮助我们发现并解决问题。
* 调整与容量规划：帮助我们让运行状态更加顺畅。

## 负载均衡器

* HAProxy: 人人喜爱的非云负载均衡器，HAProxy 亦拥有强大的日志记录功能以及一套不错的用户界面
    - 注意
        + 大多数（甚至全部）HAProxy 版本只面向单一进程报告统计数据，这种方法对于 99% 的用例都没有问题，但极少数高性能系统会使用多进程模式，这意味着其监控难度将直线提升。因为统计数据将随机提取自某一进程，这可能引发混乱，因此大家必须尽可能避免多进程监控情况。
        + HAProxy 当中的实用统计信息包括 PER 监听程序、后端或者服务器几种，虽然这些指标都很重要，但却会给整体评估带来复杂性挑战。简单的网站或应用程序通常只有一个（www.）监听程序与后端，但更为复杂的系统却通常包含多个监听程序与后端。这意味着我们会很快收集到数百项指标并陷入混乱。
    - 使用
        + 从内置网页当中提取 CSV 数据
        + 使用 CLI 工具
        + 使用 Unix Socket
    - 指标
        + 请求率：即每秒请求数量，我们可以使用以下两种方式：
            - 请求 Count REQ_TOT 不适用于每服务器，而仅计算全部服务器的总体请求率（虽然仅限于最后一秒）。
            - 你也可以使用 REQ_RATE (每秒请求)，但其仅限于最后一秒，因此大家可能会错过数据峰值时段——特别是你的监控时长在一分钟以下时。
        + 错误率：响应错误（简称 ERESP），代表来自后端的错误数量。这是一个计数器，因此你必须对其进行增量计算。不过需要注意的是，相关文档指出其中包含“客户端套接上的写入错误”，所以我们很难搞清这一数值中包含哪些客户端错误类型（特别是在移动设备上）。大家也可以使用这种方法获取每后端服务器信号。关于更多细节信息以及纯 HTTP 错误，大家通常会得到 4xx 及 5xx 错误计数，用户对这类提示也表现得最为敏感。其中 4xx 错误通常不属于客户自身的问题，但如果其突然出现，则通常源自代码错误或者某种形式的攻击。而监控 5xx 错误对于所有系统都非常重要。   你可能还希望查看请求错误（简称 EREQ），但请注意，这类错误中包括可能带来大量噪声的客户端关闭（特别是在低速或移动网络情况下）。仅限于前端。
        + 延迟：即每后端响应时间（简称 RTIME），代表过去 1024 条请求的平均延迟（在繁忙系统上，1024 条请求周期可能太短并导致错失峰值状况 ; 但在新启动的系统上，1024 条请求周期可能太长而混入大量噪声）。此项信号适用于每服务器。
        + 饱和度：队列请求的数量（简称 QCUR）。这一信号适用于后端（代表未被分配至服务器的请求）以及每服务器（代表未被发送至目标服务器的请求）。你可能需要将各项数值相加以计算整体饱和度，或追踪每服务器信号以了解单服务器饱和度（详见 Web 服务器章节）。如果使用每服务器监控方式，则应追踪每套后端服务器的饱和度（但请注意，该服务器自身可能同样在排队，因此负载均衡器中的任何队列都代表着一项严重问题）。
        + 利用率：HAProxy 通常不会耗尽全部容量，除非 CPU 资源已经被全部占用，但你也可以监控实际会话 SCUR/SLIM。
* AWS ELB: 弹性负载均衡服务：各项指标将通过 Cloud Watch 与推送至 S3 的日志共同实现提取。其格式易于处理，但处理 S3 中的日志却稍有难度，因此我们会尽量避免这种处理方法（部分原因在于，我们无法立足 S3 中的日志进行实时处理，也无法借此实现警报机制
    - ELB 指标适用于 ELB 整体，但遗憾的是无法由后端组或服务器处提取。需要注意的是，若每个可用区内只有一套后端服务器，则可使用可用区维度过滤器。
    - 将信号映射至 ELB 后，我们可从 CloudWatch 处获取全部信号。请注意，指标当中的 sum() 部分为 CloudWatch 的统计函数。具体指标包括
        + 请求率：每秒请求数量，即我们从 sum(RequestCount) 得到的指标再除以配置 CloudWatch 采样时间（ 1 或 5 分钟）。其中包含请求错误。
        + 错误率：大家还应添加以下两项指标： sum(HTTPCode_Backend_5XX) 与 sum(HTTPCode_ELB_5XX)，二者分别负责捕捉服务器产生的错误与负载均衡器产生的错误（请务必计量因队列已满造成的后端不可用与拒绝情况）。你可能还需要添加 sum(HTTPCode_Backend_5XX)。
        + 延迟：即平均延迟值。就这么简单。
        + 饱和度—即在后端队列当中获取请求的 max(SurgeQueueLength)。需要注意的是，其仅关注后端饱和度，而非负载均衡器自身在 CPU 上的饱和度（在自动规模伸缩之前）——后者目前似乎尚无法监控。你也可以监控并警报 sum(SpilloverCount)，若其〉0 则代表负载均衡器已经饱和，且由于 Surge Queue 已满而拒绝后续请求。与 5xx 错误一样，这代表已经出现非常严重的问题。
        + 利用率：目前还没有比较好的 ELB 利用率数据获取办法，因为 ELB 会在内部容量不足以进行自动规模伸缩（但可以抢在规模扩展之前进行提取）。
    - 如果大家需要计算上述信号的百分位与统计指标，请务必阅读 CloudWatch 说明文档中“典型负载均衡器指标统计数据”部分提到的警告与问题。
* AWS ALB: 应用负载均衡器
    - 指标适用于 ALB 整体，且可由目标组（通过维度过滤器）提供，大家可以借此获取给定一组后端服务器的数据（而无需直接监控 Web/ 应用服务器）。ALB 不提供每服务器数据（不过你可以根据可用区进行过滤，这样若你在每个可用区内仅拥有一套目标后端服务器，即可实现每服务器监控）。
        + 请求率：每秒请求数量，即我们从 sum(RequestCount) 得到的指标再除以配置 CloudWatch 采样时间（ 1 或 5 分钟）。其中包含请求错误。
        + 错误率：大家还应添加以下两项指标： sum(HTTPCode_Backend_5XX) 与 sum(HTTPCode_ELB_5XX)，二者分别负责捕捉服务器产生的错误与负载均衡器产生的错误（请务必计量因队列已满造成的后端不可用与拒绝情况）。你可能还需要添加 sum(TargetConnectionErrorCount)。
        + 延迟：即平均延迟值。就这么简单。
        + 饱和度— 我们似乎无法从 ALB 处获取任何队列数据，因此这里我们只使用 sum（RejectedConnectionCount），其负责对 ALB 达到最大连接数量后的拒绝连接进行计数。
        + 利用率—目前还没有比较好的 ELB 利用率数据获取办法，因为 ELB 会在内部容量不足以进行自动规模伸缩（但可以抢在规模扩展之前进行提取）。需要注意的是，你可以监控 sum(ActiveConnectionCount) 与最大连接数量——需要以手动方式或者通过 AWS Config 方可获取。

通过两种方式审视负载均衡器信号，立足前端或立足后端。在前端方面，我们需要面向不同域或网站部分（例如 API 以及验证等）关注数种信号。

* 较关注负载均衡器的总体信号（OVERALL），即面向所有前端。当然，如果分别有不同系统对接 Web、应用、API 以及搜索等对象，我们也可能需要分别追踪与之对应的各前端。这意味着我们将独立处理功能各异的单独信号。
* 某些系统对于 HTTP 与 HTTPS 采用不同的监听程序 / 后端，但其支持几乎相同的 URL，因此大家完全可以将合并为统一视图并进行实际监控。

## WEB

* 启用状态监控:对于 Apache 与 Nginx，最佳实践在于为其分配另一端口（例如端口 81）或者将其锁定于你的本地网络以及监控系统等处，从而确保恶意人士无法访问。
    - Apache: 启用 mod_status，包括 ExtendedStatus。详见 DataDog 的良好 Apache 指南。
    - Nginx: 启用 stub_status_module。详见 DataDog 的良好 Nginx 指南。
* 启用日志记录:首先需要进行配置，将其存放在理想位置，且最好能够利用 vhost 进行隔离。还需要在日志当中纳入响应时间指标，遗憾的是标准或者默认格式当中并不包括此项指标。因此你需要编辑 Web 配置以实现此项目标：
    - Apache: 我们需要在日志定义当中添加 “%D” 字段（通常位于末尾），其将以毫秒为单位记录响应时间（在 Apache V1.x 版本中需要使用 %T，但其将以秒为单位）。具体操作与下面要介绍的 Nginx $request_time 基本相同。需要注意的是，其并不支持使用更接近于 Nginx Supstream_time 的 mod-log-firstbyte 模块，但它测量的才是真正的后端响应时间。具体请参阅 Apache Log 说明文档。
    - Nginx: 我们需要为后端响应时间添加 "\$upstream_response_time" 字段，通常位于日志行末尾。利用此“后端时间”将可避免系统向低速客户端发送大量响应。Nginx 同样支持在日志定义当中添加 “\$request_time” 字段。此时间截至面向客户端的最后一个字节发送完成，因此能够捕捉到大量响应与 / 或低速客户端。   这可能会带来更为准确的用户体验反应（并不总能），但如果你的大多数问题出现在系统而非客户端处，这种方法可能引发更多噪声。具体请参阅 Nginx Log 说明文档。
    - 将 X-Forwarded-For 标头添加到日志当中。如果存在这一或者其它字段，你可能需要调整解析工具以获取正确的字段。
* 最重要的各项 Web 服务器信号（特别是延迟）只能通过日志获取，但日志信息往往难于读取、解析以及汇总
    - ELK 堆栈就能实现一部分功能（包括 Splunk、Sumologic、Logs 等等）
    - 大多数 SaaS 监控工具（例如 DataDog）也能够通过其探针或受支持的其它第三方工具提取此类指标。
* 具体指标
    - 请求率：每秒请求数量，你可以选择比较困难的实现方法，即通过读取访问日志并计算其中行数以获取总体请求数量，并对每秒请求数量进行加和。或者，你也可以使用更简单的服务器状态信息，具体方式如下： 
        * Apache: 使用 Total Accesses，此计数器用于统计进程生命周期内的总体请求数量。进行增量处理即可得出每秒请求数量。千万不要使用“Requests per Second”，因为其计算的是整个服务器生命周期内的每秒请求数量，因此并无实际意义。 
        * Nginx: 使用 Requests，此计数器用于统计总体请求数量。进行增量处理即可得出每秒请求数量。
    - 错误率：这一指标必须来自日志，且你的工具应在日志中对每秒 5xx 错误数量进行计数以获取错误率结果。你也可以对 4xx 错误进行计数，但其中可能包含大量噪声，且通常与真正影响到用户的错误无关。大家真正需要关注的只有 5xx 错误，其会直接影响用户且在理论上永远不应出现。
    - 延迟：这项指标应源自日志，你的工具应有能力合并你添加至日志当中的请求或响应时间信息（如上文所述）。一般来讲，我们需要获取整个采样周期（例如 1 或 5 分钟）内的响应时间平均值（中位值更好）。目前市面上也提供多种实用的脚本或工具选项。你通常需要将日志发送至 ELK 类服务（例如 ELK、Sumo 以及 Logs 等）、监控系统（DataDog 等）或者 New Relic、App Dynamics 或 DynaTrace 等 APM 系统当中。
    - 饱和度：这项指标的获取颇具挑战，具体取决于你的 Web 服务器类型：
        + Nginx: Nginx 服务器自身几乎不可能出现饱和问题，只要你的工作程序与最大连接数量设置得够高（默认为 1 x 1K，但一般会设置得更高，我们使用的为 4 x 2048K）。
        + Apache: 大多数用户运行的是预 fork 模式，因此每连接会配备一个 Apache 进程，其实际限制在 500 左右。因此如果后端速度缓慢，Apache 本身很容易出现饱和。所以：
            - BusyWorkers 对最小配置 MaxRequestWorkers/MaxClients/ServerLimit。当 Busy = Max 时，此服务器即达饱和且无法接受新的连接（新连接将被添加至队列当中）。
            - 你也可以在日志当中对 HTTP 503 错误进行计数，其通常发生在后端应用服务器过载的情况下。不过在理想情况下，你应该可以直接从应用服务器处获取此项指标。
            - 对于大多数 Apache 系统而言，最重要的是对内存资源进行衡量，这是因为其是导致 Apache 系统发生崩溃的常见“元凶”——特别是在运行 modPHP 或者其它基于 mod 的应用服务器时，内存不足往往经常出现。
        + 利用率：对于 Nginx 来说，利用率一般不必关注，但 Apache 的利用率则存在与饱和度一样的问题（BusyWorkers 对最小配置 MaxRequestWorkers/MaxClients/ServerLimit）。

## APP

* PHP:
    - PHP 在 Apache 当中作为 mod_php 运行,扩展能力较差;
        - 使用的只有 Apache 状态页以及 Web 服务器部分中提到的日志记录
    - Nginx 之后以独立进程 PHP-FPM 的形式运行:
        + 需要启用该状态页——正如我们需要为 Apache 及 Nginx 启用状态页一样.
        + 启用 PHP-FPM 访问、错误与缓慢记录，并借此进行故障排查
        + 此外，请允许状态页显示缓慢记录事件计数器。该错误记录需要在主 PHP-FPM 配置文件当中进行设置。访问记录的使用频率不高，但其确实存在且有着一定的必要性，因为在将其启用并把格式设置为包含 %d 之后，日志当中将包含请求实现所耗费的时间（这项设置需要在 POOL 配置当中实现，通常为 www.conf，而非主 php-fpm.conf 当中）。
        + php 日志记录进行合理配置。在默认情况下，其通常被记录在 Web 服务器的错误记录当中 ; 但如果其中包含 404 以及其它 Web 错误，则会导致分析或合并难度提升。因此，最好是在您的 PHP-FPM pool 文件（通常为 www.conf ）当中添加一条 error_log 覆盖设置（php_admin_value[error_log]）。
    - PHP-FPM，需要关注的信号
        + 速率：遗憾的是，并不存在直接方式来获取访问记录并将其合并为每秒请求次数。
        + 错误：这在很大程度上取决于您 PHP 的具体记录位置。PHP-FPM 错误记录实用性不高，因为其通常只包含缓慢请求追踪，且其中存在大量噪声信号。因此，大家应启用并监控实际 php 错误记录，并将其合并为每秒错误数量。
        + 延迟：从 PHP-FPM 访问记录当中，我们能够获得以毫秒为单位的响应时间，正如在 Apache/Nginx 当中一样。   与 Apache/Nginx 延迟一样，如果您的监控系统能够从负载均衡器当中直接提取相关信号，那么延迟的获取将更为轻松（不过其中将包含非 FPM 请求，例如静态 JS/CSS 文件延迟）。   如果您无法获取这些记录，亦可监控“Slow Requests”以获取缓慢响应数量，并通过增量计算将其合并为每秒缓慢请求数量。
        + 饱和度：监控“Listen Queue”，若其不为零，则代表着没有多余的 FPM 进程可供使用，而 FPM 系统亦宣告饱和。需要注意的是，导致这种情况的原因可能包括 CPU、内存 / 交换、数据库缓慢以及外部服务缓慢等等。   您也可以监控“Max Children Reached”以了解 FPM 上达到饱和的具体次数，不过我们无法轻松将其转换为具体速率（因为饱和状况可能持续一整天）。但在采样过程当中，该计数器的任何变化都代表着采样期间曾经出现饱和。
        + 利用率：我们希望监控使用中的 FPM 进程（即‘Active Processes’）与已配置最大值，不过后者的获取难度较高（需要解析配置），因此大家可能需要对其进行硬编码。
* JAVA:通常为各类用量繁重的网站、大规模电子商务以及逻辑复杂的网站提供动力，换方之主要面向企业用例。其运行在各类不同的服务器与环境当中，但大多立足于 Tomcat
    - 在 Web 服务器当中也应尽可能立足上游进行黄金信号监控，特别是考虑到每个 Tomcat 实例在同一主机内往往位于一个专用的 Nginx 服务器之后（但这种作法现在正逐渐被淘汰）。直接部署在 Tomcat 之前的负载均衡器（或者是 Nginx）亦可提供速率与延迟指标。
    - 直接监控 Tomcat，那么首先需要对其进行设置，这意味着大家必须确保拥有良好的访问 / 错误日志记录机制，同时启用 JMX 支持。
        + JMX 负责提供数据，因此我们需要在 JVM 层级上将其启用并重启 Tomcat。当启用 JMX 时，请确保其为只读状态且包含指向本地设备、配备一个只读用户的安全限制访问。
    - Tomcat 信号
        + 速率：通过 JMX，大家可以获取 GlobalRequestProcessor 的 requestCount 并对其进行增量处理，从而获取每秒请求数量。其将对全部请求进行计数，其中包括 HTTP，但考虑到 HTTP 有可能在请求当中占大部分比例，我们可以将其中一个 Processor 名称指定为过滤器，具体名称与您配置的 HTTP Processor 名称相同。
        + 错误：通过 JMX，大家可以获取 GlobalRequestProcessor 的 errorCount 并对其进行增量处理，从而获取每秒错误数量。其中会包含非 HTTP 内容，除非您利用 processor 进行过滤。
        + 延迟：通过 JMX，大家可以获取 GlobalRequestProcessor 的 processingTime，但其为自重启以来的总时间。我们可以将其除以 requestCount 以获得长期平均响应时间，但这项指标的实用性不高。   在理想情况下，您的监控系统或者脚本能够在每一次采样时存储上述值，而后发现其中的差异并进行等分——这种作法并不理想，但已经是我们最好的选择。
        + 饱和度：如果 ThreadPool 的 currentThreadsBusy=maxThreads，那么您将面临排队，即意味着已经饱和。因此，请监控这两项指标。
        + 利用率：利用 JMX 以获取 (currentThreadsBusy / maxThreads)，从而查看您当前线程的使用百分比。
* Python:大多数人都需要在代码当中嵌入指标以及 / 或者使用特定工具包 /APM 工具才能实现监控。Django 就拥有一套 Prometheus 模块。
    - 指标
        + 速率：我们很难将其引入代码，因为大多数任务都以每请求为基础实现运行。最简单的方法可能是设置一个全局请求计数器并直接发送，但这无疑会带来共享内存与全局锁定问题。
        + 错误：与获取速率信号的情况类似，可使用全局计数器来实现
        + 延迟：编码难度最低，您可以捕捉请求的开始与结束时间，而后发送持续时间指标
        + 饱和度：很难将其引入代码，否则能够获得来自可用工作程序及队列内调度程序的数据
        + 利用率：与饱和度情况相同。
* Ruby
    - 对于运行在 Passenger 之下的 Ruby，您可以查询定期检查 enger-status 页以获取关键指标。
    - 对于 Passenger 或 Apache mod_rails
        + 速率：获取每应用组“Processed”数量并进行增量处理，从而获取每秒请求数量。
        + 错误：没有简单的方法获取错误数量，除非其已经在日志中明确包含。Passenger 不提供单独的错误日志（只提供混合日志，而且尽管能够将记录级别设定为仅错误，但其中仍存在大量 Web 服务器错误）。
        + 延迟：您需要从 Apache/Nginx 访问日志中获取此项指标。请参阅本文中的 Web 服务器部分。
        + 饱和度：每应用组“Requests in Queue”将显示服务器是否饱和。不要使用“Requests in top-level queue”，因为根据说明文档的阐述，其应该始终为零。
        + 利用率：没有简单的方法获取利用率指标。我们可以获取总体进程数量，但其显然无法表达系统的繁忙程度。另外在配合 mod_PHP 或 PHP-FPM 时，请务必监控内存使用量——因为内存资源经常出现耗尽。

## MySQL

MySQL 监控当中的黄金标准为 Vivid Cortex

* 请求率：每秒查询数量，最简单的办法是对 MySQL 的状态变量 com_select、com_insert、com_update 以及 com_delete 进行加和，而后通过增量处理最终获得每秒查询数量。  不同监控工具在实现方式也有所区别。一般我们可以通过 SQL 进行：
```sql
SHOW GLOBAL STATUS LIKE “com_select”;
SHOW GLOBAL STATUS LIKE “com_insert”;
 # 你也可以一次性将四者进行加和，并获取单一数值：
SELECT sum(variable_value) FROM information_schema.global_status WHERE variable_name IN (“com_select”, “com_update”, “com_delete”,“com_insert”) ;
```
* 错误率：我们可以获取一条全局错误率，其中包含 SQL、语法以及大部分返回自 MySQL 的其它错误。我们从 Performance Schema 当中获取此指标，但这里要使用 User Event 表而非 Global Event 表——这是因为后者主要用于延迟测量，而 User 表则一般体积小巧因此速度更快且更易清除。这是一个计数器，因此你还需要进行增量处理。此查询为：
```sql
SELECT sum(sum_errors) AS query_count FROM events_statements_summary_by_user_by_event_name WHERE event_name IN (‘statement/sql/select’, ‘statement/sql/insert’, ‘statement/sql/
update’, ‘statement/sql/delete’);
```
* 延迟：我们可以从 Performance Schema 中获取延迟。正如前文所述，要获取理想的数据，我们需要解决以下复杂问题：  我们需要使用 events_statements_summary_global_by_event_name 表，因为来自其它潜在实用表的数据（例如 events_statements_history_long）会在线程 / 连接中止时自动被删除（这种情况在 PHP 等非 Java 系统中相当常见）。另外大家所熟知的摘要表在数学层面非常难于使用，因为其中存在复杂的溢出与时钟 / 计时器计算。   不过 events_statements_summary_global_by_event_name 表是一种汇总表，因此其中包含有自服务器启动以来的全部数据。这意味着我们必须随时对该表进行 TRUNCATE 方可实现读取——如果还有其它工具需要使用此表，则可能产生问题。不过这种情况似乎并无避免的方法。具体查询如下，但获取 SELECT 的延迟与获取全部查询的数据一样，都不可避免地涉及更为复杂的数学计算或查询——以下两条语句分别用于获取数据以及截断该表：
```sql
SELECT (avg_timer_wait)/1e9 AS avg_latency_ms FROM performance_schema.events_statements_summary_global_by_event_name WHERE event_name = ‘statement/sql/select’;
TRUNCATE TABLE performance_schema.events_statements_summary_global_by_event_name;
```
* 饱和度：最简单的饱和度查看方法为根据队列深度判断，但深度指标非常难以获取。因此：  最好的办法就是解析 SHOW ENGINE INNODB STATUS 输出结果（或者 Innodb Status 文件）。我们在自己的系统中执行这一操作，但非常麻烦（参见以下并发设置）。如果选择这种方法，那么大家需要的两个变量分别为“Queries Running # queries inside InnoDB”以及“# queries in queue”。如果你只希望获取其中之一，则应选择 queue，其将告知我们 InnoDB 是否饱和。   需要注意的是，以上输出结果取决于 innodb_thread_concurrency 是否大于零。最近的新版本当中，人们往往将其设置为零，这意味着状态输出结果中将禁用这两项状态。在这种情况下，大家必须使用全局 Threads Running，具体如下所示。如果 innodb_thread_concurrency = 0 或者无法读取该状态输出文件，我们则需要着眼于更简单的 THREADS_RUNNING 全局状态变量，其将告知我们存在多少非活动线程——不过我们无法查看其是否正在排队等待 CPU、Locks 或者 IO。这是一种即时测量方法，因此其中可能包含大量噪声。大家可能需要立足多个监控周期求取平均值。以下为两种获取方法：
```sql
 SHOW GLOBAL STATUS LIKE “THREADS_RUNNING”;
 SELECT sum(variable_value) FROM information_schema.global_status WHERE Variable_name = “THREADS_RUNNING” ;
```
    - 请注意，Threads Connected 本身并不是一项理想的饱和度指标，因为其经常源自客户端上的饱和问题——例如 PHP 进程（当然这也有可能源自缓慢数据库）。
    - 你也可以将 Threads Connected 与 Threads Max 进行比较，但这种作法仅适用于系统线程不足的情况——在现代服务器上，由于最大值会被设置为高于任何可能客户端数量（例如 8192 以上），且数据库会自动拒绝连接，因此这种情况应该永远不会出现。
    - 在 AWS RDS 当中，你亦可以从 Cloud Watch 当中获取 DiskQueueDepth，这种作法适用于 IO 限制类工作负载。对于非 RDS，请参阅 Linux 部分以了解更多与 IO 相关的细节信息。

* 利用率：MySQL 能够以多种方式耗尽容量，但立足底层 CPU 及 I/O 容量的方法最为简单，即测量 CPU % 与 IO 利用率 %。二者的获取需要一点技巧，具体请参阅 Linux 部分内容。在这里，我们使用的一项实用利用率指标为“row”读取，在进行增量处理之后，其将告知我们每秒行读取数量—— MySQL 在每 CPU 计算核心上最高可进行约 100 万次每秒行读取操作，因此如果你的数据库 IO（内存内数据）量不高，这种方法将帮助大家更好地把握利用率。不过如果你能够将其与 MySQL 数据加以结合，则可配合 Linux CPU 获得更准确的结论。我们可以通过以下两种方式实现行读取计数：
```sql
 SHOW GLOBAL STATUS LIKE “INNODB_ROWS_READ”;
 SELECT variable_value FROM information_schema.global_status WHERE variable_name = “INNODB_ROWS_READ”;
```
    - 在 AWS RDS 当中，你也可以从 Cloud Watch 当中获取 CPU Utilization，这种作法适用于 CPU 限制型工作负载场景。

## Linux

### CPU

* 延迟：目前尚不清楚其在 Linux 当中意味着什么，可能代表着负载的执行时耗，也可能代表负载在 CPU 队列中的等待时间（不涉及交换或 IO 延迟）。  遗憾的是，我们无法衡量 Linux 在执行各类负载时所耗费的具体时间，也无法衡量全部任务在 CPU 队列当中的等待时长。有些朋友可能认为从 /proc/schedstat 中能够得到这类指标，但经过大量实验，我们发现其实并不可以。
* 饱和度：我们需要 CPU Run Queue 长度，如果其大于零，则代表负载需要在 CPU 上等待处理。不过我们很难准确获取此项指标。
    - 我们当然希望能够直接使用 Load Average，但遗憾的是在 Linux 当中，其中同时包含 CPU 与 IO，这会令数据内容质量下降。不过如果你的服务仅使用 CPU 且 IO 很少 / 为零（例如应用服务器），则此项指标将极易获取——但请注意那些被写入至磁盘的大型日志。若 Load Average 大于 CPU 计数的 1 到 2 倍，则通常意味着饱和。
    - 如果我们需要真正的队列，即排除一切 IO，则难度将直线上升。第一问题在于，所有工具所显示的数据并无法随时间推移计算平均值，这意味着瞬时测量结果将包含大量噪声——这种情况广泛存在于 vmstat、sar-q 以及 /proc/stat 的 procs_running 当中。  下一项挑战在于，Linux 调用的运行队列中实际包含有当前运行的条目，因此我们必须减去 CPU 数量以获得真正的队列指标。任何大于零的结果都意味着 CPU 处于饱和状态。
    - 最后一个问题是如何从内核当中获取这些数据。最好的办法当然是使用 /proc/sat 文件。获取“procs_running”计数并减去 cpus（grep -c processor / proc/cpuinfo）即可获得队列指标。
    - 但这仍然属于瞬时计数，因此同样包含大量噪声。似乎还没有工具选项能够实现随时间推移的汇总能力，因此我们使用 Go 语言编写了 runqstat 工具——感兴趣的朋友可以点击此处在 GitHub 中查看（目前仍处于开发当中）。
    - 窃取：你可以追踪 CPU 窃取百分比，这意味着从虚拟机管理程序的角度来看，CPU 已经处于饱和状态。不过大家也应同时查看 CPU Run Queue 中的即时增长，除非你使用的是 Nginx、HAProxy 或者 Node.js 等单线程负载（在这种情况下，Run Queue 将难以查看）。
    - 需要注意的是，对于单线程负载而言饱和度指标实用性不高（因为由于不存在其它需要运行的负载，因为不会出现队列）。在这种情况下，你应关注利用率，具体如下。
* 利用率：我们需要 CPU%，但其需要配合一些数学计算，最终得出的 CPU% 加和为：用户 + 冗余 + 系统 + 窃取（若有）。不要添加 iowait%，显然也不应添加 idle%。  在虚拟化条件下，对系统上的 CPU 窃取百分比进行追踪将非常重要。请千万不要忘记这一点，并对一切高于 10% 至 25% 的结果保持警惕。
    - 对于 Docker 以及其它容器，我们很难检测到最佳饱和度指标，且具体取决于系统是否设定有上限。大家必须读取每个容器中的每套 /proc 文件系统，并从 cpu.stat 文件中获取结果，提取会在每次 CPU 发生卡顿时增加的 nr_throttled 指标。在对其进行增量处理后，我们即可得到每秒卡顿数量。

### 磁盘

* 请求率：磁盘系统的 IOPS（合并之后），在 iostat 中表示为 r/s 与 w/s。
* 错误率：对磁盘进行测量无法得出任何有意义的错误率——除非你希望对高延迟状况进行计数。大多数真正的错误——例如 ext4 或 NFS——会带来致命的后果，你应尽快发布警报。而且由于需要对内核日志进行解析，因此我们实际上很难发现这些错误。
* 延迟：读取与写入时间，其在 iostat 中表示为平均等待时间（await），其中包含查询时间并会在磁盘饱和时急剧上升。
    - 如果可以，最好分别监控读取与写入响应时间（即 r_await 与 w_await），因为二者在动态表现上往往区别很大，能够帮助你为敏感地把握实际变化，且不受“其它”IO 方向（特别是数据库）噪声的影响。
    - 你也可以测量 iostat 服务时间，并借此查看各磁盘子系统的运行状况，具体包括 AWS EBS 以及 RAID 等等。其上升可能会给负载及 IO 敏感型服务带来实际影响。
* 饱和度：最好利用每设备 IO 队列长度进行测量，iostat 以 aqu-sz 变量的形式提供。需要注意的是，其中包含有服务 IO 请求，因此并不算是真正的队列指标（相关数学计算比较复杂）。需要强调的是，iostat util % 并不适合用于测量真正的饱和度，特别是在使用 SSD 与 RAID 阵列的情况下——二者可以同时执行多个 IO 请求，因此其实际表示的是处于进行过程中的至少一项请求的时间百分比。然而，如果你只需要获取 util %，也可利用其简单查看当前运行状态与基准情况是否存在差别。
* 利用率：大多数人会使用 iostat util %，但如上所述，这项指标在衡量单轴磁盘之外的实际利用率时并不适合。

### 内存

* 饱和度：任何交换行为都意味着内存已经发生饱和，不过目前大多数云虚拟机已经不再使用任何交换文件，因此这项指标的实用性正快速下降。这意味着目前最理想的衡量指标应该是接下来要提到的利用率。
    - 大家应尽可能追踪 OOM（内存不足）错误，其主要由内核本身引发，即出现内存空间用尽或者进程交换。遗憾的是，我们只能在内核日志当中查看这些情况，因此在理想状态下，你可以将这些信息发送至一套集中式 ELK 或者基于 SaaS 的日志分析系统当中，并据此发布警报。
    - 任何内存不足错误都意味着内存资源已经严重饱和，而且也代表着一种紧急事态——在 MySQL 等系统中，这可能导致内存使用量最大的用户被关闭。
    - 需要注意的是，如果存在交换或者内核交换未被设置为 1，那么内存在被用尽之前会发生大量交换操作。这是因为内核不会在开始交换之前尽可能下调文件缓存，如此一来即可保证内存资源被生产进程全部占用之前即进入内存饱和状态。
* 利用率 — 计算内存使用量的公式为（总内存 - 自由缓冲区 - 缓存），这也是最经典的内存用量衡量方法。除以总内存容量，即可得出当前利用率百分比。

## 网络:最关注的是吞吐量，也就是我们在带宽层面所说的“每秒请求数量”

* 速率：读取与写入带宽，以每秒 bit 为单位。大多数监控系统能够轻松获取这项指标。
* 利用率：将读取 / 写入带宽除以网络最大带宽。需要注意的是，在混合虚拟机 / 容器环境下，最大带宽数值可能很难确定，这是因为此类情况下流量可能同时使用主机的本地主机网络与主有线网络。这时，可能最好直接追踪读取与写入带宽。

## 参考

* 《利用 USE 与 RED 方法实现监控与观察》：USE 方法主要着眼于资源内部，RED 方法则关注请求、实际工作以及外部视角（即来自服务消费方的视角）。各种方法既彼此相关，亦相辅相成，共同面向每一项服务实现自身效果所消耗的具体资源。
* 《利用异常检测进行监控》
