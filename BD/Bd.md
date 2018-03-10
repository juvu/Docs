# 大数据

## OLAP VS OLTP

### 联机分析处理(OLAP) 

OLTP也称实时系统(Real Time System)，支持事务快速响应和大并发，这类系统典型的有ATM机(Automated Teller Machine)系统、自动售票系统等，但有些银行转账并不是实时到账的。OLTP反映企业当前的运行状态，完成企业管理所包含的日常任务的数据库应用，一般没有复杂的查询和分析处理。

### 联机事务处理(OLTP)

OLAP也称决策支持系统(Decision Support System，DSS)，是数据仓库系统的主要应用形式，使分析人员、管理人员或执行人员能够从多种角度对从原始数据中转化出来的、能够真正为用户所理解的、并真实反映企业维特性的信息进行快速、一致、交互地存取，从而获得对数据的更深入了解的一类软件技术。

* 数据仓库(Data Warehouse)：核心。是一个面向主题的（Subject Oriented）、集成的（Integrated）、相对稳定的（Non-Volatile）、反映历史变化（Time Variant）的海量数据集合(包括大量冗余数据)，用以支持经营管理中的决策制定过程，核心是海量数据存放和海量数据检索。相对于操纵型数据库来说其突出的特点是对海量数据的支持和快速的检索技术。为了实现决策支持型数据处理与事务型数据处理的分离，它按照一定的周期将事务型数据转换导入决策支持数据库中。数据仓库系统是一个信息提供平台，他从业务处理系统获得数据，主要以星型模型和雪花模型进行数据组织，为用户提供各种手段从中获取信息和知识。数据仓库按照数据的覆盖范围可以分为企业级数据仓库和部门级数据仓库（通常称为数据集市）。从功能结构划分，数据仓库系统至少应该包含数据获取（Data Acquisition）、数据存储（Data Storage）、数据访问（Data Access）三个关键部分。
* 联机分析处理
* 数据挖掘

目标是满足决策支持或多维环境特定的查询和报表需求，它的技术核心概念是维(观察数据的特定角度，如时间维)，因此OLAP也可以说是多维数据分析工具的集合。

按照数据存储格式

* Relational OLAP(ROLAP）：基本数据和聚合数据均存放在RDBMS之中
* Multidimensional OLAP(MOLAP）：存放于多维数据库中
* Hybrid OLAP(HOLAP）：本数据存放于RDBMS之中，聚合数据存放于多维数据库中。

E.F.Codd提出12条准则来描述OLAP系统：

* OLAP模型必须提供多维概念视图　　
* 透明性　
* 存取能力推测 　　
* 稳定的报表能力 　　
* 客户/服务器体系结构 　　
* 维的等同性　
* 动态的稀疏矩阵处理　
* 多用户支持能力　
* 非受限的跨维操作 　　
* 直观的数据操纵 　　
* 灵活的报表生成 　　
* 不受限的维与聚集层次

ETL(Extraction-Transformation-Loading)：负责将分布的、异构数据源中的数据如关系数据、平面数据(去除了所有特定应用格式，可以迁移到其他应用上进行处理的一类数据，比如逗号分隔数据)文件等抽取到临时中间层后进行清洗、转换、集成，最后加载到数据仓库或数据集市中，成为联机分析处理、数据挖掘的基础，是BI(Business Intelligence)/DW的核心和灵魂，是数据仓库中的非常重要的一环。数据仓库是一个独立的数据环境，需要通过抽取过程将数据从联机事务处理环境、外部数据源或者脱机的数据存储介质导入到数据仓库中；在技术上，ETL主要涉及到关联、转换、增量、调度和监控等几个方面；数据仓库系统中数据不要求与联机事务处理系统中数据实时同步，所以ETL可以定时进行。在数据仓库建设中最难部分是用户需求分析和模型设计，而ETL规则设计和实施则是工作量最大的，约占整个项目的60%～80%。