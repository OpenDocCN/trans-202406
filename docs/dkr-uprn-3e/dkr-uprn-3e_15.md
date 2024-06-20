# 第十四章：结论

到目前为止，你已经对 Docker 生态系统有了扎实的了解，并且看到了 Docker 和 Linux 容器如何能够使你和你的组织受益的许多示例。我们试图列出一些常见的陷阱，并传授多年来在生产中运行 Linux 容器时积累的一些智慧。我们的经验表明，Docker 的承诺是完全可以实现的，作为结果，我们的组织也看到了显著的好处。像其他强大的技术一样，Docker 也不是没有妥协的，但总体结果对我们、我们的团队和我们的组织都是极大的积极影响。如果你将 Docker 工作流程实施并整合到你组织已有的流程中，有充分的理由相信你也能从中获得显著的益处。

在本章中，我们将花一点时间考虑 Docker 在技术景观中不断演变的位置，然后快速回顾 Docker 设计帮助你解决的问题以及它带来的一些强大功能。

# 未来之路

毫无疑问，容器将长期存在，但有些人长期以来一直预测 Docker 的最终消亡。其中很大一部分原因只是因为 *Docker* 这个词在很多人心目中代表了 [很多不同的东西](https://oreil.ly/pvSEl).¹ 你是在谈论那家在 2019 年被卖给 Mirantis 的公司，两年后重组后报告的每年 5000 万美元的营收吗？还是 `docker` 客户端工具，其源代码可以被 [下载](https://github.com/docker/cli)，任何需要的人都可以修改并构建？这很难说。人们经常喜欢试图预测未来，但现实往往隐藏在中间，埋藏在常常被忽视的细节之中。

2020 年，Kubernetes 宣布[废弃 dockershim](https://kubernetes.io/blog/2022/02/17/dockershim-faq)，并且随着 Kubernetes v1.24 版本的发布完全生效。当时，许多人认为这意味着 Docker 已经死了，但许多人忽略的一点是，Docker 始终主要是开发工具，而不是生产组件。当然，它可以出于各种原因在生产系统上使用，但其真正的力量在于能够将大部分软件打包和测试工作流程整合到一个统一的工具集中。Kubernetes 使用[容器运行时接口（CRI）](https://kubernetes.io/blog/2016/12/container-runtime-interface-cri-in-kubernetes)，而 Docker 并未实现这一接口，因此需要他们维护另一个称为`dockershim`的包装软件来通过 CRI 支持使用 Docker 引擎。此公告并不意味着要表达 Docker 在生态系统中的位置，而只是为了简化维护一个大型志愿驱动的开源项目。Docker 可能无法在您的 Kubernetes 服务器上运行，但在大多数情况下，这对软件的开发和发布周期没有任何影响。除非您是一个使用`docker` CLI 直接查询运行在 Kubernetes 节点上的容器的 Kubernetes 操作员，否则在此过渡期间，您不太可能注意到任何变化。

此外，正如事实证明的那样，Docker 的母公司已经开发并继续支持一个名为[`cri-dockerd`](https://github.com/Mirantis/cri-dockerd)的新的 shim，允许那些需要支持此工作流程的人继续与 Docker 进行接口交互。

有趣的是，Docker 还在进入非容器技术领域多样化，比如[WebAssembly](https://docs.docker.com/desktop/wasm)（Wasm），这可以补充容器技术，同时改善开发者体验。

所以，作为一款开发者友好的工具集，Docker 很可能会长期存在，但这并不意味着生态系统中没有其他工具可以补充甚至取代它，如果这是你想要或需要的。像 OCI 这样存在的各种标准的美妙之处在于它们被广泛采纳，许多工具可以与其他工具生成和管理的相同镜像和容器进行互操作。

# Docker 解决的挑战

在传统的部署工作流程中，通常需要大量步骤，这些步骤显著增加了团队的整体痛苦感。每增加一个应用程序部署过程中的步骤都会增加将其推向生产环境的风险。Docker 将工作流程与简单的工具集结合在一起，直接针对这些问题进行了处理。在这过程中，它直接指向了行业最佳实践，其自主的方法往往会导致更好的沟通和更加健壮的应用程序设计。

Docker 和 Linux 容器可以帮助缓解的一些具体问题包括以下几点：

+   避免在部署环境之间出现显著的分歧。

+   要求应用开发人员在应用程序中重新创建配置和日志记录逻辑。

+   使用过时的构建和发布流程，需要在开发和运维团队之间进行多层次交接。

+   需要复杂且脆弱的构建和部署过程。

+   管理需要共享同一硬件的应用程序所需的不同依赖版本。

+   在同一组织中管理多个 Linux 发行版。

+   为每个投入生产的应用程序构建一次性部署流程。

+   在处理补丁和审计安全漏洞时，需要将每个应用程序视为独特的代码库。

+   和更多。

通过将注册表用作交接点，Docker 简化了操作团队与开发团队之间，或同一项目中的多个开发团队之间的沟通。通过将一个应用程序的所有依赖项捆绑成一个交付物，Docker 消除了开发人员想要在哪个 Linux 发行版上工作、他们需要使用哪些库的版本以及如何编译其资产或捆绑其软件的担忧。它将操作团队与构建过程隔离开来，并让开发人员负责他们的依赖关系。

# Docker 工作流

Docker 的工作流帮助组织解决真正困难的问题——一些与 DevOps 过程旨在解决的相同问题。将 DevOps 成功地整合到公司的流程中的一个主要问题是，许多人不知道从何处开始。工具经常被错误地呈现为解决基本上是过程问题的方案。向环境中添加虚拟化、自动化测试、部署工具或配置管理套件通常只是改变了问题的性质，而没有提供解决方案。

将 Docker 只视为另一个工具，它做出了无法实现的承诺，这样的看法过于狭隘。Docker 的强大之处在于其自然的工作流程允许应用程序在一个生态系统内完成其整个生命周期，从概念到退役。与其他通常只针对 DevOps 管道的单个方面的工具不同，Docker 几乎改进了流程的每一个步骤。该工作流程通常是有主张的，但它简化了采纳 DevOps 核心原则的过程。它鼓励开发团队理解其应用程序的整个生命周期，并允许操作团队在同一运行时环境上支持更多种类的应用程序。这为各方面带来了价值。

# 最小化部署工件

Docker 减轻了常常由庞大的部署产物引发的痛苦。它通过将构建结果定义为单个构件，即 Docker 镜像来实现。镜像包含了您的 Linux 应用程序运行所需的一切，并在受保护的运行时环境中执行。容器可以轻松部署在现代 Linux 发行版上。但由于 Docker 客户端和服务器之间的清晰分离，开发人员可以在非 Linux 系统上构建其应用程序，同时仍能远程参与 Linux 容器环境。

利用 Docker，软件开发人员可以创建 Docker 镜像，从最初的概念验证开始，就可以在本地运行、通过自动化工具进行测试，并部署到集成或生产环境，而无需重新构建。这确保了在生产中启动的应用与经过测试的应用完全一致。在部署工作流程中不需要重新编译或重新打包任何内容，这显著降低了通常在大多数部署过程中固有的风险。同时，单一的构建步骤取代了通常涉及多个复杂组件编译和打包的容易出错的过程。

Docker 镜像还简化了应用程序的安装和配置。每个现代 Linux 内核上运行所需的软件都包含在镜像中，消除了传统环境中可能出现的依赖冲突。这使得在同一台服务器上运行依赖不同版本核心系统软件的多个应用程序变得轻而易举。

# 优化存储和检索

Docker 利用文件系统层次结构允许容器由多个镜像组合而成。通过仅传输重要变更，大大节省了许多部署过程中的时间和精力。它还通过允许多个容器基于相同的底层基础镜像，然后利用写时复制过程将新文件或修改后的文件写入顶层，节省了大量的磁盘空间。这也有助于通过允许在同一台服务器上启动更多应用的副本来扩展应用。

为了支持图像检索，Docker 利用镜像注册表来托管镜像。尽管表面看并不革命性，但注册表有助于根据 DevOps 原则明确划分团队责任。开发人员可以构建他们的应用程序，测试它，将最终镜像发布到注册表，并将镜像部署到生产环境，而运维团队可以专注于构建优秀的部署和集群管理工具，从注册表中提取镜像，确保其可靠运行，并确保环境健康。运维团队可以在构建时提供反馈给开发人员，并查看所有测试运行的结果，而不是等到应用程序被部署到生产环境时才发现问题。这使得两个团队都可以专注于各自擅长的领域，而无需进行多阶段的交接流程。

# 优势

随着团队对 Docker 及其工作流的信心增强，他们往往会意识到容器在所有软件组件和底层操作系统之间创建了一个强大的抽象层。组织可以开始摆脱为大多数应用程序创建定制物理服务器或虚拟机的做法，而是部署一批相同的 Docker 主机作为资源池，动态地将他们的应用程序部署到其中，这是以前无法想象的便捷方式。

当这些流程变化成功时，对软件工程组织的文化影响可能是显著的。开发人员对完整的应用程序堆栈拥有更多的所有权，包括通常由完全不同的团队处理的最小细节。与此同时，运维团队同时摆脱了尝试打包和部署复杂依赖树的负担，而几乎没有关于应用程序的详细知识。

在良好设计的 Docker 工作流中，开发人员编译和打包应用程序，这使得他们能够更轻松地确保应用程序在所有环境中正常运行，而不必担心运维团队引入的重大环境变化。与此同时，运维团队不再花费大部分时间支持应用程序，可以专注于为应用程序创建一个强大稳定的平台。这种动态创建了一个非常健康的环境，团队在应用交付过程中拥有更清晰的所有权和责任，团队之间的摩擦显著减少。

正确处理流程对公司和客户都有巨大的好处。通过消除组织内部的摩擦，提高了软件质量，优化了流程，使代码更快地交付到生产环境。所有这些都有助于组织更多地投入提供令人满意的客户体验和直接交付更广泛的业务目标。一个良好实施的基于 Docker 的工作流程可以极大地帮助组织实现这些目标。

# 最后的话

现在，你应该已经掌握了帮助你过渡到现代基于容器的构建和部署流程的知识。我们鼓励你在笔记本电脑或虚拟机上小规模地尝试使用 Docker，以进一步理解所有组件如何配合，然后考虑如何开始为你的组织实施。每家公司或个人开发者都会根据自己的需求和能力选择不同的路径。如果你想要关于如何开始的指导，我们发现先用简单的工具解决部署问题，然后再转向诸如服务发现和分布式调度等任务，通常会取得成功。Docker 可以变得非常复杂，但像任何事物一样，从简单开始通常会更有回报。

我们希望你现在可以运用这些新获得的知识，实现 Docker 和 Linux 容器为你自己带来的种种承诺。

¹ 完整网址：[*https://www.tutorialworks.com/difference-docker-containerd-runc-crio-oci*](https://www.tutorialworks.com/difference-docker-containerd-runc-crio-oci)