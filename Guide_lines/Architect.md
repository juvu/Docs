# 架构

## 架构部操作手册

公司的部门分工、流程、机制由其文化、历史、人员背景等决定，无一定之规，仅供参考。

架构部属于技术部中的公共部门，面向技术部整体，在架构层面负责，包括三个小组：架构与规范、应用架构研发、性能测试，并组织技术委员会。

* 职责
    - 架构与规范：负责系统架构蓝图设计，系统设计的合规，架构的持续改善与治理，研发流程的规范制定与审查，提升整体技术架构水平。研发公共技术组件，推进技术体系规范化，架构合理化，实现平台化、功能通用化，提高技术能力，使系统具备更强的灵活性，满足公司业务战略发展需要，实现业务技术一体化。
    - 应用架构研发（基础平台）：负责技术基础应用平台体系的搭建，包括项目管理、自动化编译部署、应用监控告警、报单处理、数据库元数据管理等通用系统的设计开发。提升技术部的自动化、工具化水平，提高工作效率，强化运维能力和信息化管理能力。
    - 性能测试：负责对性能要求较高的核心系统、技术组件进行线下和线上性能测试，提前发现性能问题、验证系统性能指标达成情况，确保系统更为稳定。
    - 技术委员会：根据公司业务发展战略需要，关注电商业务场景需要的大流量、高并发、高可靠性、实时性、平台化、服务化的底层技术。针对不同领域组织专家小组、课题小组，实现跨团队技术共享与协作。定期组织技术沙龙，分享外部技术热点，挖掘内部技术成果，收集技术资料供技术部学习。与外部协作举办线下技术沙龙，邀请外部专家，与其他公司进行技术交流，向业界技术会议推荐专家讲师（进行报备及主题审核），进行主题分享，发表技术文章，活跃团队技术气氛，强化公司技术品牌。
* 产品线
    - 技术框架、组件
* 基础平台
       项目管理系统PDLC

* 自动化运维部署平台
    - 应用监控平台
    - 服务监控系统
    - 报单跟踪系统
    - 资源管理平台
    - 代码构建仓库
    - 代码检查工具

### 日常工作

####  架构

    + 业务架构
    + 系统列表
收集整理系统列表：参见Twiki
因采用PDLC进行技术团队项目管理，PDLC中包含此功能，应以PDLC为准。

* 系统架构总图：最新版参见：架构部工作文档\01.规划\系统清单
* 系统分级：根据系统重要程度分级，参见文档：架构部工作文档\01.规划\系统分级
* 系统分层：复杂系统需要有清晰的层次划分，一般自上而下调用，避免逆向依赖而导致循环依赖。一般分为前台、中台、后台。
    - 前台包括面向用户的页面、我的订单、营销活动等系统。
    - 中台包括通用服务，搜索、推荐、数据、购物车、交易、客户信息、商品信息、价格、库存、促销、礼券、礼品卡、商家、积分、支付、全站配置等系统。
    - 后台包括订单、退换货、客服、TMS、WMS、财务、结算、ERP等系统。
* 架构规划：对未来系统架构进行规划，明确边界划分，指导产品线设计，避免重复建设、长期缺位等问题。
* 系统架构
    - 系统成熟度：根据不同的系统类型，定义成熟度标准，梳理各系统当前实际水平，指导系统演进。参见Twiki。
    - 项目支持：根据需要对具体项目进行支持，包括：
        + 前期调研，设计方案，如：O+O、拍卖、直播。
        + 根据项目需要，参与项目需求分析、技术方案设计、上线部署准备。
        + 主导项目架构设计、核心代码实现、技术难点公关，如：WMS、TMS
* 架构评审：技术设计方案评审，在PDLC上登记，每周三下午定期举行，特殊情况可临时评审。
    * 要求尽可能早、同一个项目各系统一起进行架构评审，这样可能根据建议进行调整。
    * 架构评审重点在于把关，提高研发设计能力，考虑全面，达到基本要求。
    * 有争议的设计方案可以打回，无法达成一致则升级决策。
    * 技术部自发的重点重构项目往往忽视架构设计评审，导致后续开发、测试、上线、运行出现问题。

* 技术架构
    - 技术调研：对互联网、电商领域新兴技术进行调研，跟踪趋势，分析优劣，根据公司实际情况提出实行建议。比如日志中心、Redis集群、容器化、微服务等。
    - 技术组件、框架、平台
        + 公司技术架构体系并不完整和统一，需要逐步完善，并重在解决实际痛点和获得技术部广泛认可。技术架构作为基础，逐步完善意味着整体技术水平提升，并以技术提升效率，降低成本和风险，创造价值。
        + 路线是组件到框架到平台，由易入难，由轻落重。
        + 重点： 稳定性、易用性、分布式场景可靠性、持续升级。
    - 技术支持：响应各技术团队技术咨询、协助解决技术问题，重点在于架构部推广的框架、组件、平台。
    - 技术债
        + 架构部应关注技术维度问题，立足当下、面向未来、对过去心中有数，知其然亦知其所以然。
        - 总结解决方案、建议

#### 规范

汇总技术规范并发布，规范可来自其他部门。

* 设计规范
    - 技术方案设计规范
    - 数据库设计规范
    - 项目架构设计文档规范
* 开发规范
    - CodeReview规范
    - 代码质量检测
    - SQLReview规范
    - Maven使用规范
    - PHP/JAVA开发规范
* 单元测试规范
* 流程规范
* PDLC流程
* 域名申请流程
* 开源申请流程

#### 应用架构研发

* 管理机制
    - 各系统有主要负责人，相当于内部PO。
    - 采用PDLC敏捷方式进行任务管理，跨职能，交叉测试。
    - 架构部自身管理也应结合在PDLC之中。

# 关注重点
    - 待完成任务登记列表：
    - 系统稳定性、易用性、监控全面性。

技术部各团队潜在需求。

通过PDLC、Pangu、监控系统数据统计分析使用情况及发现问题。

关注线上系统运行情况。

监控系统的监控方案。

Nagios年底下线。

跨机房部署方案。

Tracker告警短信接口超时问题。

告警是否可以扩展到钉钉、微信等通知。

* 发展方向
    - 完善基础平台，各系统打通，发挥协同作用。
    - 作为自研技术组件和新技术的试点应用平台。


2.3性能测试
2.3.1流程
测试任务由架构部内部发起，或其他部门通过项目经理提出，任务需登记在PDLC中。

性能测试的发起来源：

1.研发部门根据项目改动发起，包括页面性能测试。

2.架构部参与项目设计时明确。

3.架构部评审项目设计时提出。

2.3.2重点
测试项目文档、结果、报告、脚本做好备份。

尽可能了解业务场景，有针对性制定性能测试方案，最大程度发现问题。

对测试结果负责，了解问题原因，有问题务必通知到项目经理，避免影响上线决策。

测试环境尽可能与线上一致，模拟请求符合业务场景，线下测试主要目标发现性能瓶颈及隐患。

2.3.3发展方向
开发自动化测试工具，实现自动化、平台化、可复用，节省人力，提高效率。

对线上系统实现常态化、自动化性能测试，以性能指标、趋势衡量系统。

将性能测试工程师升级为测试开发工程师及性能测试专家。



2.4技术委员会
2.4.1内部沙龙
定期举行技术沙龙，邀请外出参会人员、内部有成果成员、新加入架构师进行分享。

2.4.2专家小组
根据实际需要，组成针对专门领域的跨部门技术专家小组，加强协作，以强化对专门技术的掌握，提高整体技术能力。

2.4.3外部交流
与其他公司进行技术交流。

邀请外部专家到公司分享交流，可从HR培训部申请经费。

2.4.4外部沙龙、大会
分享外部沙龙、大会信息，与HR培训部沟通，申请参加。

推荐技术骨干作为嘉宾，到外部技术沙龙、大会进行分享。

在业界技术社区、网站、公众号发表文章。 

2.4.5开源社群运营
基于自研开源产品，通过GitHub、QQ群、微信群维系社群，响应问题，发掘贡献者，进行技术支持。

2.4.6内部社群运营
通过技术部组织跨部门技术QQ群、微信群，增加部门沟通，便于咨询交流。



三．部门长管理职责
3.1部门管理
3.1.1部门周会
每周五上午10点-11点，结合部门周报，总结工作成果及问题，明确后续工作，传达公司、技术部信息，记录会议纪要，安排内部分享。

注意：会议室预约可能会被定期清理。

3.1.2技术部周会
每周一下午2点12-2，会前会收集其他议题。

架构部周报经整理后写入sharepoint。

会上各部门汇报上周工作情况，架构部应了解各部门情况，并参与相关问题讨论。

3.1.3部门绩效
目前技术部实行季度考核，每季度各部门与HR指定KPI，季度结束后进行考核，其中一定比例为CTO打分。

每季度会发放绩效奖金，部门绩效会影响部门总奖金数及人员考核结果分布比例。

3.1.4月总结
每月会安排各部门反馈月总结及下月计划。

月总结通过周报汇总即可获得。

一般更关注各产品线情况。

3.1.5与CTO1V1
每两周周四下午3点-4点，汇报架构部各项工作进展及问题，反馈一些想法，与CTO进行深入讨论。

听取CTO的指导和建议，保持思路同步。

3.1.6招聘
与HR招聘组对口负责人协作，通过邮件筛选简历，安排面试时间及人员，架构师也需要笔试。

架构师需要CTO终面，终面时间可另行安排。

其他职位负责终面，介绍架构部情况，明确具体职责及加班要求，询问待遇要求及当前offer情况，将结果反馈给HR。

对新员工指定导师，确定试用期目标，结束前评估达成情况，打印签字后交给HR，以便转正。

发布招聘信息，尽快招聘到位，避免长期空缺造成人员浮动。

新入职架构师需安排在技术部范围内进行分享。

招聘JD参见：架构部工作文档\08.招聘\架构部JD.txt

3.1.7培训交流
负责对技术部新员工进行系统架构培训、技术方案设计培训，包括应届生，并针对应届生安排Java基础培训。

负责对有需要的业务部门和外部公司讲解公司系统架构。

3.1.8资产管理：管理部门成员使用的办公电脑、开发测试环境服务器、线上生产环境服务器。

* 开发敏捷管理：项目经理负责，迭代任务计划制定、任务分配（注意交叉测试）、站立会、迭代总结。
    * 代码备份、SQL备份
    * 代码规范、检查
    * 测试要求、报告
    * 登记待完成任务。

#### 团队管理

* 绩效考核
    * 每季度对部门成员进行绩效考核，根据HR要求分配考核结果。
    * 考核结果会影响未来的晋升和加薪资格。
    * 架构部因职责、能力差异较大，主要以集中答辩评分、部门长主观评价及参考工时确定。
    * 需关注、强调成员平均工时情况，进行提醒，此为公司要求，HR会进行统计。
* 晋升及加薪
    - 每半年技术部有一次晋升答辩，高级水平以上需要公开答辩，初中级各部门自行评审。
    - 晋升一般要连续两次考核B以上，以HR提供规则为准。
    - 晋升会获得较大加薪，每半年也会有普调，两者资金隔离。
    - 晋升加薪及普调加薪，需部门长根据情况分配。
* 1V1：定期（如每两周）与团队成员1V1，半小时，沟通近期工作、问题、后续工作方向，个人成长目标，对公司、团队的想法。
* 内部分享
    - 每周安排一名同事进行内部分享，分享内容尽量以技术为主，30分钟以内。
    - 强化内部交流氛围，提高分享技能。
* 资金管理：每季度每人150元左右活动经费，由部门福利专员管理。
* 团队建设：可根据需要定期举行团建活动，可安排部门福利专员组织。
* 申请奖项
    - 每月HR会征集总裁认同奖及技术部部门认同奖，包括个人奖及团队奖，可根据情况提报。
    - 申请到的认同奖，可留一部分作为团队经费，一部分聚餐，剩余大部分根据贡献分配发放到个人。

#### 记录

* 架构部Twiki主页：
* 架构部相关历史Twiki:
* 架构部Sharepoint主页：
* 外部技术大会资料（上传需在ITSupport申请开通权限）
