# 容器

从过去以物理机和虚拟机为主体的开发运维环境，向以容器为核心的基础设施的转变过程，并不是一次温和的改革，而是涵盖了对网络、存储、调度、操作系统、分布式原理等各个方面的容器化理解和改造。

就在这场因“容器”而起的技术变革中，Kubernetes 项目已然成为容器技术的事实标准，重新定义了基础设施领域对应用编排与管理的种种可能。

2013年，虚拟机和云计算已经是比较普遍的技术和服务了，那时主流用户的普遍用法，就是租一批 AWS 或者 OpenStack 的虚拟机，然后像以前管理物理服务器那样，用脚本或者手工的方式在这些机器上部署应用。

当然，这个部署过程难免会碰到云端虚拟机和本地环境不一致的问题，所以当时的云计算服务，比的就是谁能更好地模拟本地服务器环境，能带来更好的“上云”体验。以 Cloud Foundry 为主的 PaaS 开源项目的出现，就是当时解决这个问题的一个最佳方案。

PaaS 之所以能够帮助用户大规模部署应用到集群里，是因为它提供了一套应用打包的功能。可偏偏就是这个打包功能，却成了 PaaS 日后不断遭到用户诟病的一个“软肋”。出现这个问题的根本原因是，一旦用上了 PaaS，用户就必须为每种语言、每种框架，甚至每个版本的应用维护一个打好的包。这个打包过程，没有任何章法可循，更麻烦的是，明明在本地运行得好好的应用，却需要做很多修改和配置工作才能在 PaaS 里运行起来。而这些修改和配置，并没有什么经验可以借鉴，基本上得靠不断试错，直到你摸清楚了本地应用和远端 PaaS 匹配的“脾气”才能够搞定。最后结局就是，“cf push”确实是能一键部署了，但是为了实现这个一键部署，用户为每个应用打包的工作可谓一波三折，费尽心机。

而就在这时，一个并不引人瞩目的 PaaS 创业公司 dotCloud，却选择了开源自家的一个容器项目 Docker。更出人意料的是，就是这样一个普通到不能再普通的技术，却开启了一个名为“Docker”的全新时代。

Docker 项目确实与 Cloud Foundry 的容器在大部分功能和实现原理上都是一样的，可偏偏就是这剩下的一小部分不一样的功能，成了 Docker 项目接下来“呼风唤雨”的不二法宝。这个功能，就是 Docker 镜像。而 Docker 镜像解决的，恰恰就是打包这个根本性的问题。

Docker 项目给 PaaS 世界带来的“降维打击”，其实是提供了一种非常便利的打包机制。这种机制直接打包了应用运行所需要的整个操作系统，从而保证了本地环境和云端环境的高度一致，避免了用户通过“试错”来匹配两种不同运行环境之间差异的痛苦过程。

Docker 项目固然解决了应用打包的难题，但正如前面所介绍的那样，它并不能代替 PaaS 完成大规模部署应用的职责。

一些机敏的创业公司，纷纷在第一时间推出了 Docker 容器集群管理的开源项目（比如 Deis 和 Flynn），它们一般称自己为 CaaS，即 Container-as-a-Service，用来跟“过时”的 PaaS 们划清界限。而在 2014 年底的 DockerCon 上，Docker 公司雄心勃勃地对外发布了自家研发的“Docker 原生”容器集群管理项目 Swarm，不仅将这波“CaaS”热推向了一个前所未有的高潮，更是寄托了整个 Docker 公司重新定义 PaaS 的宏伟愿望。

虽然通过“容器”这个概念完成了对经典 PaaS 项目的“降维打击”，但是 Docker 项目和 Docker 公司，兜兜转转了一年多，却还是回到了 PaaS 项目原本深耕了多年的那个战场：如何让开发者把应用部署在我的项目上。

虽然 Docker 项目备受追捧，但用户们最终要部署的，还是他们的网站、服务、数据库，甚至是云计算业务。这就意味着，只有那些能够为用户提供平台层能力的工具，才会真正成为开发者们关心和愿意付费的产品。而 Docker 项目这样一个只能用来创建和启停容器的小工具，最终只能充当这些平台项目的“幕后英雄”。

而 Swarm 项目，正是接下来承接 Docker 公司所有这些努力的关键所在