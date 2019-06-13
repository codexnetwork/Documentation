# CODEX : open and inclusive high performance multi-chain platform

## 1. Backgroud

In the future, block chain technology will be the same as the current mature database technology.

A variety of different architecture chains coexist, multi-chains coexist. At the same time, there are general chains serving for general needs, special chains serving special needs, public chains facing the public, and private chains facing specific users.

On the other hand, with the increasing application of block chain technology, the interaction requirements between chains will become increasingly close, and cross-chain requirements under different validation mechanisms will be extremely common.

Future block chain architects will choose the most suitable public or private chains to deal with different needs according to the needs they face. At the same time, they will choose the appropriate cross-chain mode for each chain to integrate the whole system. Similar to the current database technology, in today's common software architecture, they often choose different data store according to the needs of data use. And then through different technologies, such as message queue, RPC interface, to transfer and read and write data between various databases and users.

At present, many voices think that there will be a few block chain technologies that can solve all the problems in the future, and many projects use them as selling points and targets. But compared with the existing mature technologies, in fact, because of the different needs, considering the cost and efficiency, no technology can find a universal solution, and so does block chain technology.


## 2. Vision

` CODEX is committed to building an open and inclusive high-performance multi-chain ecosystem to support the actual landing of block chains.


## 3. Technology Acchitecture

Codex is a multi-chain technology ecological construction program, which mainly includes three aspects:

- `Codex.IO Framework` : High Performance Modular Block Chain Bottom Framework
- `Codex.Relay` : High Performance Cross-Chain Service
- `Codex.SDP` : Multi-Chain Development Toolset

### Codex.IO Framework

As mentioned above, when discussing the architecture of multi-chain ecosystem, the idea of solving problems should be to use specific implementation chains to efficiently address specific needs.

This requires developers to start a chain quickly, while only focusing on specific parts of the implementation. If they want to achieve this, they need a series of highly customizable block chain development frameworks.

To this end, we conceived and designed the Codex.IO project, which aims to build a highly customizable and high-performance block chain development framework.

In order to ensure the extensibility and performance of block chains, Codex.IO chooses POS-like consensus algorithm as its main consensus direction.
As the most available POS-like development framework, EOSIO will be adopted as the underlying development framework at the current stage.
In the future, we will seek better solutions in the direction of more decentralization and scalability.

EOS technology is a potential block chain development technology.
On the one hand, based on the DPOS consensus, EOS chains can meet the needs of more than 4000 + TPS at present.
On the other hand, EOS provides an smart contract development platform that can develop medium and large-scale DAPP.

Based on the above two points, EOS technology provides an effective block chain technology which can land quickly in the medium and long term.
Although other technologies also have great potential, they need to be improved for a long time. For any application of technology, from zero to one is always the most difficult and crucial.
Therefore, we choose to develop Codex.IO project based on EOS technology.

But EOS technology is a specific public chain project after all, which means that in EOS, designers will not consider too much chain extensibility.
Therefore, we need a thorough deconstruction of EOS technology, which is also a major work of Codex.IO.

#### Codex.IO Hierarchical Architecture

In order to meet different needs, Codex.IO needs to do three things: consensus is irrelevant, virtual machine is irrelevant, interface is irrelevant.
In this way, developers based on Codex.IO can choose different consensus algorithms and different virtual machines according to their needs, and can easily add or modify the interface of the original chain.
In order to achieve this goal, we have designed a new layered block chain infrastructure development framework, stripping the dependence between the original layers.

Codex.IO Architecture：

![Codex.IO Architecture：](../assert/pic1.png)

#### Topological Layer

Topology layer is represented by block topology structure. The most common block topology structure is block chain. Of course, different consensus algorithms will determine the difference of block topology structure.

Peeling out the topology layer can make Codex.IO easy to adapt to different consensus algorithms, from the original DPOS implementation to POS, POW and other implementations, and also can adopt different block topology(such as DAG) consensus algorithms.

#### Iterative Layer

In order to divide the execution layer and the topology layer, we introduce the block iteration layer to hide the details of the block layer from the execution layer.

#### Execution Layer

In EOS and its derivatives, behavior is defined by action, and the system is responsible for interpreting action and updating the chain state machine accordingly.

In the process of interpreting action, virtual machine is a very important part. Except for a few actions implemented by C++ layer, other actions are executed in virtual machine.
In Codex.IO, we will implement a modular virtual machine implementation by separating the execution layer from other modules.
Codex.IO will be adapted to different virtual machine systems, from specific implementations such as BTC scripts to general-purpose virtual machines such as WASM/JVM/V8 and even embedded language runtimes such as Lua/Python.
This will greatly reduce the technological threshold for traditional enterprises to embrace block chains, so that the introduction of block chains technology will not affect the original technological system of enterprises.
On the other hand, some simple and easy-to-use language platforms can attract more developers to realize their creativity.

#### State Layer

In most block chain technologies, nodes maintain a state machine system with quasi-real-time read-write capability.
In EOS, this system is embodied as the memory database of the EOS node.
At present, these data are stored in an imperfect durable memory database implemented by EOS.
Rich data structures can help developers achieve their needs better and faster.
So in Codex.IO, we separate the implementation of state machines in the execution layer to form a connecting layer.
On the one hand, it allows developers to expand more types of memory data structures and help developers better implement DAPP.
On the other hand, the introduction of such a layer can hide the implementation details of the execution layer from the interface layer.

#### Interface Layer

Interface layer is mainly embodied in API and P2P protocol between nodes.



Chains for different purposes are destined to need to provide different API interfaces. Codex.IO needs to allow secondary developers to simply add and modify APIs for chains.

In various mainstream technology architectures, different API implementations are often used according to different requirements, such as general HTTP REST API, various RPC implementations such as gRPC, SOAP, and various message queues.
As a brand-new value transmission network, block chains must maintain good compatibility and reduce the threshold of value exchange in the interaction with various businesses in the world.
At present, developers need to implement their own interfaces to the original system, such as various EOS plug-ins. Developers have to spend a lot of time rewriting the development of existing business interface logic.
In order to make developers focus on their own business development, we design an independent interface layer, which is decoupled from other layers through the state layer.

On the other hand, as a de-centralized block chain system, P2P protocol is the basic protocol for inter-node networking. In EOS, the designer implements a set of incomplete protocols by himself. Although it can be replaced to some extent, it needs a lot of development work.
In Codex.IO, we will adapt to the mainstream P2P protocol, such as 'libp2p'.

To sum up, Codex.IO is committed to the implementation of a hierarchical framework for high customizable block chains. Through the hierarchical architecture, we achieve low coupling among all levels. Therefore, Codex.IO is more inclusive and can meet the basic development needs of most chains.

### Codex.Relay Relay Chain

The communication mechanism between chains in multi-chain architecture will be the most important part of it,

High Performance Multi-Chain Systems Need High Performance Cross-Chain Mechanisms,

TPS can not be infinitely superimposed by increasing the number of chains. In the preview of the actual multi-chain architecture, the maximum number of transactions per second (TPS) processed by a single service is often limited by the number of transactions per second (TPS) processed across chains. TPS will become a bottleneck restricting the landing ability of the whole multi-chain ecosystem, so cross-chain performance must be improved in Codex ecosystem.

So Codex. Relay will be based on Codex.IO. By customizing functions, we can achieve high-performance communication between chains based on Codex.Relay.

At the same time, Codex.Relay is a service rather than a closed ecosystem.


![Codex.IO Architecture](../assert/pic4.png)

We call the chain using Codex.Relay relay service the side chain of Codex.Relay, where the side chain does not mean that it has primary and secondary relationship with Codex.Relay.
Of course, side chains can also enhance their own security based on the Codex.Relay consensus, or even completely based on the Codex.Relay consensus to reach the consensus of side chains.

At the data level, side chains submit block data to Codex.Relay and state transformations that need to be relayed. The role of performing these operations becomes trunk, which can be the core node of side chains or a set of guarantee nodes composed of stakeholders.

Among the submitters, we divided them into the following types of trunk:

committer: Responsible for the transfer of actions needed in the side chain block to Codex.Relay.
watcher: Responsible for the transfer of actions needed in the Codex.Relay block to side chain.
validator: Responsible for checking whether transferer and watcher's renewal and mortgage are synchronized and whether there is collective evildoing.

Committer is oriented to Codex.Relay, and its behavior is embodied in the action submitted to Codex.Relay. Committer submits the block of side chain to Codex.Relay by multi-signature, which ensures the security of the channel when most committers are honest.

Similar to committer, watcher's behavior is embodied in the Codex.Relay behavior submitted to the side chain, where committer and watcher supervise each other. When one party commits a collective evil, the other party will find the problem and suspend the cross-chain channel.

Validator consists of a group of nodes with core interests in Codex.Relay. It is responsible for verifying whether committers and watchers commit collective evils. When evils occur, the validator will punish the evildoers and restore cross-chain channels by multi-signature.

These roles may be different sets of nodes or the same set of nodes depending on the chain.

In practice, committer should have a core interest in the side chain. For a particular chain, BP of the side chain can act as the transferer. In the cross-chain system, committer will not reduce the degree of decentralization of the whole system.
Validator, as the supervisor of the whole cross-chain process, is very important in the whole system. For the cross-chain channel between Codex.Relay and the main chain, validator will be composed of the core BPs of Codex.Relay.
For example, when Codex.Relay is based on DPOS, the validator is composed of super nodes of Codex.Relay, and when Codex.Relay is based on POS, the validator is composed of selected block producers at each stage.

For Codex.Relay, the block of the side chain is determined by the consensus mechanism of the side chain. Codex.Relay is designed as a service, so it is consensus-independent and chain-independent. The block of the side chain is submitted to Codex.Relay by committer through multi-signatures. On Codex.Relay, specific state transitions are performed according to the block to complete the state synchronization from side chain to relay chain.

![Codex.IO Architecture](../assert/pic2.png)

For the side chain, the block of Codex.Relay is determined by the consensus mechanism of Codex.Relay. The side chain responds to the block of Codex.Relay through the watcher node set through multiple signatures, thus completing the state synchronization from the relay chain to the side chain.

![Codex.IO Architecture](../assert/pic3.png)

In Codex multi-chain ecosystem, there is no direct communication between side chains. First, it is difficult to adapt any chain because of the different structure between chains. Second, there are many chains in multi-chain architecture. If any two chains are interconnected, a lot of confirmation and waiting time will be caused, which will seriously reduce the efficiency of the chain.

It should be noted that in Codex multi-chain ecosystem, Codex.Relay is not exclusive. In multi-chain ecosystem, there will be multiple Codex.Relay chains coexisting. One of them can be used as a general relay to relay most chains, while in some special chains, there will be a series of specific Codex.Relay as a special relay.

Based on above block-based communication, we can achieve cross-chain state synchronization. On this basis, we can achieve many basic cross-chain functions:

#### 1. Inter-chain Value Channel

For most chains, Token is an important indicator of user rights, and the Token transmission channel between chains will be the main cross-chain requirement.

Through the inter-chain state synchronization mechanism, we can easily achieve cross-chain synchronization of Token information:

![Codex.IO Architecture](../assert/pic6.png)

![Codex.IO Architecture](../assert/pic5.png)

For multi-chain ecosystem Token, there will be two main forms in the future. First, some tokens, such as BTC, ETH, EOS, which were widely accepted, will be mapped directly to many other chains and used in them.
Even many chains will run in a coinless mode, and such tokens are often used directly by them.

Secondly, other tokens are only used in its own chains, and will not and need not be used by other chains. Obviously, most DAPP can not handle arbitrary token use. these tokens need to be converted by other tokens in a specific way, which is often achieved by users through transactions, in order to facilitate different tokens transfer and use in multi-chain ecosystem, we pre-set Token exchange contract in Codex.Relay to facilitate users to convert tokens.

![Codex.IO Architecture](../assert/pic8.png)

#### 2. Multi-chain contract

In multi-chain ecosystem, there will be a large number of DAPP contracts deployed in different chains at the same time. These contracts jointly fulfill the functions of DAPP. Based on the inter-chain state synchronization mechanism, it is easier to realize the mechanism of inter-chain contract invocation. Through this mechanism, the prior DAPP architecture will be greatly changed, and many common logic and functions will be deployed as service contracts on a specific chain. Such as predictor service, cryptographic secure random number generation service, IPFS storage service, etc.

![Codex.IO Architecture](../assert/pic7.png)

The developers of DAPP only need to use these services through multi-chain contract communication mechanism, which will greatly reduce the development cost and difficulty of DAPP.

#### 3. Cross-Chain Synchronization of Real-Time State Information

Many times DAPP developers need to synchronize real-time status information between two chains. A typical application is to share accounts between two chains based on Codex.IO.

`Codex.Relay`is based on `Codex.IO', which is represented as a DAPP, providing relay and cross-chain services for various chains in a multi-chain ecosystem. Codex.Relay is also the best example of a one chain one DAPP.

### Codex.SDP Super DAPP Development Platform

Multi-chain ecosystem will also bring new challenges to DAPP development,
Traditional DAPP is based on contracts on a single chain and some data caching services,
In a multi-chain ecosystem, a DAPP consists of multiple contracts on multiple different chains.

In order to cope with the complexity of multi-chain development, a new development form of DAPP is needed,
Therefore, we propose Codex.SDP, which will bring developers a new DAPP development experience, and redefine the block chain application development model.

![Codex.SDP features](../assert/pic9.png)

First, with the diversification of chains in multi-chain ecosystem, developers will face the problem of fragmentation of chain contract versions,
There will always be some differences between different chains. Simply depending on the development tools provided by each chain itself will bring great trouble to DAPP developers. To solve this problem, Codex. SDP will add a chain implementation layer to hide the differences of each chain, so that developers can deploy on multiple chains only with one encoding.

Secondly, in multi-chain ecosystem, developers need to deploy contracts in multiple chains. The update and maintenance of these contracts will bring great problems to DAPP developers. Codex. SDP will provide a set of basic tools to help users update and maintain contracts on multiple chains.

Thirdly, Codex. SDP will integrate a new DAPP development toolkit, improve the support of various development languages, and reduce the pressure of the development team technology stack.

## 4. Design Thought

### why Multi-Chain

Supporting multi-chains is an important direction of block chain development. Through multi-chains, we can provide abundant functions for users and DApp developers while taking into account the current chain security. On the other hand, through cross-chains, we can allow communities to start different public or private chains and provide the most suitable running environment for DApp.

At present, almost all block chain projects are facing the problem of high operating costs of DApp. EOS uses DPOS consensus to make the whole chain have high TPS. However, for super nodes, many resources are still limited in a certain period of time. For example, RAM, although according to Moore's law, with the passage of time, the whole chain will get more resources. But compared with vigorous and rapid growth of DAPP requirements, in the short run, the relative scarcity of resources, coupled with the promotion of some hype, will seriously increase the operating costs of developers, which is a great obstacle to the development of EOS as a whole.

Based on the above considerations, an obvious solution is to use multiple different or identical chains to carry DApp, and at the same time, through cross-chain technology, multiple independent chains can communicate and synchronize with each other to form a multi-chain ecosystem.
There will be chains of different uses in this system:

- Public Chain : Such as BTC、ETH、EOS etc.
- DAPP Chain : Some or some DAPP-specific chains are often partially specialized based on EOSIO or Codex.IO.
- Alliance Chain : Specific-purpose alliance chains integrated into multi-chain systems and communicate with other chain
- Relay Chain : Providing relay services between chains

In multi-chain ecosystem, developers can choose the most suitable chain for DApp, which not only provides rich functions for developers and users, but also guarantees the security of users' assets. At the same time, we can ensure that the whole network resources can grow, so that the scarcity of resources can be alleviated.
DApp developers are free to choose multiple chains that can be afforded to keep the whole system alive.

Based on the excellent consensus mechanism brought by EOS, we design a heterogeneous multi-chain system. We hope that this multi-chain system will form an ecosystem, which will bring us the following benefits：

**Stable and inexpensive resources on the chain** Developers can freely choose running chains according to their own needs and allow multiple chains to integrate into the whole ecosystem, which will enable the supply of resources to fully meet the needs of the market.
This can stabilize the cost of resources on the chain. On the other hand, many applications would like the chain to provide stable resources and TPS. By deploying dedicated chains, DApp can be fully guaranteed not to be interfered by other applications.

**Allow to extended chain functionality** Sometimes developers and users need to extend the chain functionality to meet their specific needs, and then deploy specialized alliance chains or private chains.

**Multi-chain Value Exchange** In multi-chain systems, assets on multiple chains can be easily exchanged through the relay layer. Further, decentralized exchanges can be deployed on the relay chain.

**Support seamless migration of applications based on other chains** We can introduce other underlying chain technology and smart contract technology to provide developers and users in the form of chains, so that developers can easily transplant existing applications into the ecosystem.

### Why DPOS

As a distributed system, the consensus mechanism of block chains can be said to be one of the most important components,
In <<Bitcoin: Point-to-Point Electronic Cash System>>, Nakamoto proposed Proof Of Work (POW) algorithm to implement distributed timestamp server,
Proof Of Work (POW) algorithm has been recognized as an effective de-centralized consensus algorithm in the operation of bitcoin,
However, Proof Of Work (POW) algorithm is also considered to be an inefficient and costly consensus algorithm, so the community has been looking for a better consensus mechanism.

A basic assumption that POW algorithm works well is that organizations or individuals with the most processor capabilities benefit more from compliance than from other dishonest gains,
In this regard, the community proposes a Proof Of Stake (POS) algorithm, which considers that since POW makes the organization or individual with the most processor capabilities the most profitable.
So long as consensus algorithm can make the most profitable individual or organization decide the operation of consensus mechanism, the consensus algorithm will be as effective as POW even without a lot of workload calculation.



On the other hand, the result of consensus algorithm can be regarded as selecting a block producer at a block height, the producer can be a node or a series of nodes,
Thus, the core process of POS is often an algorithm that allows the most profitable individuals or organizations in the system to determine the production of blocks,
Generally speaking, block production determined by a single node tends to be too centralized, and it is better to produce blocks by consensus among a series of nodes,
Therefore, POS algorithm often shows that in a certain period of time, a series of nodes are selected to co-produce blocks. These nodes can represent the most profitable individuals or organizations in the system.

As you can see, the key to POS algorithm is how to select the individuals or organizations that represent the most profitable in the system, which is also the difference of most POS algorithms.

The early POS algorithm encountered a lot of problems in practice. The typical problem is "Nothing-at-Stake" attack, which is essentially a consensus algorithm and fails to ensure that the producer represents the interests of the system,
Because stakes are inherently very complex, the common solution is to add various incentives and punishment mechanisms, which are embodied in the so-called "governance mechanism",
Since POS algorithm relies on "governance mechanism" and election is a typical social governance mechanism, a natural way of thinking is to establish governance mechanism based on election,
And elections can also be used to determine the block producers, which is similar to representative democracy, based on which is a typical DPOS consensus algorithm.

CODEX chooses EOSIO-based DPOS consensus mechanism with the following two considerations:

First of all, the POS-based consensus mechanism, as mentioned above, relies heavily on the governance mechanism. Although the election-based governance mechanism is not the best one, it is a viable one.
Secondly, CODEX needs to maintain a high TPS while providing a complete Turing smart contract, which means that full nodes in the network must have very strong computing resources, which determines that the number of full nodes in the network will not be large, at least not every user will maintain a full node,
In this case, regardless of the consensus mechanism, the block producer node set tends to be a fixed small set. If this is acknowledged, then a "representative" block producer node set will not reduce the decentralization of the whole network,

Based on the above considerations, CODEX chooses EOSIO-based DPOS consensus mechanism.

### Codex.IO's Design

From the point of view of a so-called traditional software architecture, the intuitive attitude often regards the block chain as a database. For example, EOSIO is very similar to PostgreSQL in many aspects from the point of view of the interface used. Of course, the block chain technology provides new features over the traditional database technology and solves the problems that have not been solved before.

If we want to realize a card game based on block chain technology, in order to realize a card game, first of all, the structure must meet a relatively high performance index, which can be expanded horizontally, that is, to meet certain TPS requirements, At present, the chain based on EOSIO can be satisfied from the point of view of the underlying technology, but if it is only based on EOS public chain, the scalability will be greatly reduced. The congestion of EOS public chain in the past period also confirms this point. On the performance issue, a relatively simple idea is to start multiple chains, which means that we need to start a single or multiple chains for this card game.

On the other hand game definitely needs its special logic rules. We can achieve this well based on smart contracts, but we also have to note that the functions provided by current smart contracts are still very limited. For example, as one of the common functions of games, user ranking is difficult to achieve based on current contracts. The difficulty of ranking is that ranking needs frequent updates, the scale of the list is very large, often based on all users, if this aspect is not considered carefully, it will cause the whole game can not run smoothly even for the traditional centralized server. If it is based on EOSIO, it is necessary to implement a new data structure from the bottom perspective. Even for nodes, it is necessary for each node to start a separate ranking node to decentralize the calculation pressure, which means that developers need to expand EOSIO functionality like traditional servers.

In summary, if we want to do some applications based on block chain technology at present, what we need is to start one or more chains with a lot of modifications at the bottom, on which we can complete common logic based on smart contracts.

In this way, it seems that there is no hindrance to block chain landing, but if we look at the current block chain technology from the developer's point of view, it is actually difficult to achieve the above process, because for an application developer, it is impossible to spend a lot of time and energy to start development from the bottom of the chain, and EOSIO is not designed to adapt to the situation of a large number of changes in the bottom, after all, as an application developer. Public chain technology, rather than being able to adapt to the underlying modifications, can not be changed at all after the start of the chain. This is why many people want to add some small functions, but the EOS public chain has not been implemented, even the simplest function, often means that the whole chain needs to be hard-forked.

After summarizing the practice of EOSForce and the experience of ELMG EOS in the past year, the Force team believes that a highly customizable public chain framework based on EOSIO technology is needed. High customizability enables developers to simply add the functions they need. EOSIO technology can guarantee high TPS and keep up with the most recent progress in the area of block chain. "framework" rather than "library" emphasizes that what we want to build is an out-of-the-box product that users do not need to know the underlying details.

### Codex.IO Resource Model

In order to allocate computing resources fairly, reasonably and efficiently, a set of resource settlement rules, called resource model, is needed on the chain.

In EOSIO-based chains, there are generally three kinds of resources: CPU、NET and RAM,
Among these three resources, CPU has strong timeliness, while NET has timeliness, it also affects the fixed storage space of nodes. Unlike CPU and NET, RAM has strong spatial characteristics,
Generally, there are three ways to obtain resources: handling fee, mortgage and lease. In order to meet different needs, Codex.IO supports a variety of resource models.

For CODEX relay chain, CPU and NET are acquired by mortgage core Token, RAM is acquired by lease.
CODEX relay chain is a kind of special chain. CPU and NET do not have problems like EOSIO through mortgage, but mortgage is more friendly to transaction. So CODEX uses mortgage to obtain CPU and NET.
For RAM, leasing is based on Force practice, which can make RAM more effective.

In CODEX relay chain, like EOSForce, users use voting dividends to lease memory, which is user-friendly and does not require frequent operations.

CODEX Relay Chain introduces user low-guarantee system, which gives each user a part of the basic available resources, so that most users do not need additional operations to obtain resources.,
Considering that in an ecosystem, the increase of users usually makes the ecosystem more sound, so the low-guarantee standard will not have a great impact on the system.

### Codex Cross-Chain Design

Cross-chain is a basic requirement in multi-chain ecosystem. The essence of cross-chain is to identify the blocks of another chain by one chain.
In a chain, blocks are validated based on consensus algorithms, such as Codex. IO, where a block is validated by more than two-thirds of BP for two rounds and becomes irreversible, that is, by network validation,
The most natural way of thinking is to use the process of source chain consistency to identify blocks. Suppose there are two chains started by Codex.IO, chain A and chain B, respectively. Chain A wants to identify blocks of chain B,
Then, only the super nodes on chain A need to confirm the signature of the super nodes on each chain B. Based on the irreversible mechanism of chain B, chain A can easily confirm the block of chain B,
Conversely, chain B can also confirm blocks of chain A based on the same process.

Through the method mentioned above, the cross-chain communication and state synchronization between chain A and chain B are realized.
A cross-chain logic that expects no reduction in the degree of system decentralization is necessarily similar to the process described above,
The consensus algorithm of any chain defines this chain to a great extent. Block validation bypassing its consensus algorithm is difficult to ensure that there are no potential problems in the validation process.

But there are also some problems that can not be ignored in this process,

First of all, the consensus algorithm for a chain to complete the source chain synchronously often requires all the nodes that produce blocks to run the full node of the source chain simultaneously,  For example, in order to confirm the signature of the super node, chain A actually needs the information provided by the whole node of chain B to confirm it, For a chain with low TPS, maintaining the whole node of the chain costs little, but for a chain with high TPS, the requirement of maintaining a full node will be very high. Under such a cost, it is difficult to establish a corresponding cross-chain economic system.

Second, following the above approach means that chains trying to synchronize the states of other chains need to change their logic to identify blocks of other chains, which means that this approach is intrusive,
If you want to cross chains between existing chains, it means that each chain has to modify its own code.

Finally, even though chains can be modified to some extent, or cross-chain support is added to the chain development stage, in a multi-chain system, a chain often needs to synchronize the states of multiple chains, if each chain is adapted.
So the mechanism that this chain needs to carry is to become abnormally overstaffed.

The problems mentioned above mean that if we want to build a multi-chain interworking system as open and free as possible, its cross-chain mechanism must satisfy the following principles,

- **principle 1** Cross-Chain Implementation Mechanisms Need to Maintain Efficiency and Cheapness
- **principle 2** Cross-chain adaptation needs to be non-intrusive
- **principle 3** Cross-chain topology requires low coupling

A good way to deal with the explosion of cross-chain complexity caused by the number of chains is to use a dedicated chain synchronized with the chain state as a relay between chains, such as Cosmos's Hub Zone.

Two other issues are the intrusive modification and performance impact of cross-chain mechanisms on chains,

Chains of different technology architectures often have different application scenarios, which determines that chains of different technology architectures have different design and performance requirements,

Most of the cross-chain mechanisms originate from the consensus mechanism of the chain, and different consensus mechanisms determine the community system of different chains.
For BTC, it is difficult for the community to accept a semi-centralized cross-chain mechanism based on a fixed number of nodes, and often does not care about a high degree of decentralization but slow performance cross-chain scheme.
For fabrics-based alliance chains, high-performance cross-chain services provided by a centralized node of source chain operation and maintenance are often chosen.

Performance considerations for different chains are also inconsistent. For BTC, the cross-chain TPS of tens of transfer transactions per second can meet the needs of most application scenarios, but for a chain initiated by Codex.IO, it is likely to require thousands of times of synchronization of complex states per second.

To sum up, the cross-chain mechanism in a multi-chain ecosystem needs to take into account the consistency and versatility, which is also the design direction of CODEX relay chain.

CODEX relay chain provides relay services between chains. In CODEX multi-chain system, chains do not interact with each other, but cross-chain through CODEX relay chain. In the process of cross-chain, we add a `relay layer'to hide the differences between chains. The mechanism of cross-chain is completed by relay layer. In order to adapt to different ecological systems and performance requirements between chains, relay layer only defines relay nodes and their roles. In the relay layer, there are three types of nodes: Committer, Watcher and Checker.

Committer is responsible for state synchronization from source chain to relay chain, Watcher is responsible for state synchronization from relay chain to source chain, Checker is responsible for monitoring whether Committer and Watcher are honest and credible as a whole. These three types of roles are not clearly specified. According to the different chains and the actual situation, the most suitable and feasible nodes will be selected,

For example, for a DAPP application chain based on Codex.IO, Committer and Watcher can be served by a super-node in this chain. Checker is not needed at this time, because in the DPOS consensus, super-nodes are assumed to be honest as a whole. Such a cross-chain process will not have any impact on the centralization of this application chain and its related applications.

Again, for ELMG EOS or some other major EOSIO-based public chains, if super-nodes can not participate in cross-chain, then Committer can be maintained by some ELMG EOS community nodes, while Watcher can be served by CODEX relay chain super-nodes concurrently, and the corresponding Checker can be composed of Committer and Watcher. Here for the behavior of CODEX relay chain, CODEX relay chain super Nodes are honest on the whole, so Watcher by CODEX relay chain super-node will not reduce the credibility of relay, on the other hand, Checker's supervision can prevent Committer from doing evil, in this system, such a cross-chain process can also be effective.

Another example is a fabric-based alliance chain or a private chain based on Codex.IO. It has been shown that a highly available centralized node can serve as Committer and Watcher, and some third-party nodes can be introduced as Checkers for monitoring.

Finally, for POW chains such as BTC and ETH, the situation will be more complex. For Committer, it can be based on // specific hash locking technology - let alone hash locking, which means that light node relay // forward transfers information to the relay chain. Watcher can be served by super-nodes of CODEX relay chain, and Checker can be composed of a part of "fisher" nodes to ensure the reliability of the whole system. However, it also needs to establish a set of economic models to match it.

In addition, this general design allows CODEX relay chain to be compatible with other cross-chain technologies, such as Cosmos, polkadot, etc.

## 5. Application scenarios

### Multi-chain Practice

On the multi-chain ecosystem, through the free supply and trade of resources, many applications that were previously difficult to operate on the chain can be put into the chain operation, so that these applications can not only obtain the consensus of users, but also control the operation cost by selecting specific resources while ensuring TPS.

#### On-chain online game platform

Let's consider a on-chain online game platform. Traditional Internet game operation modes include game players, game developers (CP), game operators and game channel providers.

At present, these subjects are full of contradictions：

- Players do not trust game developers, one of the manifestations is the questioning of reward probability. For this reason, the government requires developers to disclose all kinds of lottery probability, but it is impossible for players to verify it.
- Game players do not trust game players, players can not carry out reliable transactions, cheating plug-ins are common in the game.
- Game developers distrust operators and channel providers. Game developers are at a disadvantage in revenue sharing and ranking, which makes game developers have no motivation to develop better games.

etc， The final result is total transmission. In fact, these contradictions can be well solved through block chain technology and consensus mechanism can be formed，
However, game operation requires high TPS and resources, and many game play logic is not suitable and unnecessary to run on the block chain，
Previous block-chain games faced the problem of blocking the entire network and operating costs too high.

These problems can be solved well through multi-chain ecology, as shown in the figure：

![side](../assert/multi_chain_pic5.png)

Consider a typical RPG mobile game, where a lot of logic is still available to players through the traditional game server client architecture.，
The key logic, such as output, rankings and player assets, is handled by the platform on the chain, and players can verify their honesty，
Platform provides SDK to help game developers access the chain, which will also reduce the cost of developers, and further encourage developers to migrate existing games to the chain，

Platform uses dedicated application chains to allow specific games, which ensures TPS requirements when the game is online, and on the other hand, reduces operating costs.

It is expected that there will be a special application chain cloud service providers in the future. Through several service providers alliances, the platform can provide chain resources efficiently and flexibly, while ensuring the integrity of the chain.

On the other hand, players can exchange platform coin for the game coin to consume in the game, and they can also realize free assets exchange between players through the exchange on the relay layer.

## 6. Roadmap

- **Dante**   2019 Q2 Codex 1.0, Lanch Codex.Relay, supporting EOSIO isomorphic chain state Channel
- **Vinci**   2020 Q1 Codex 2.0, Codex.Relay switches to the PoS consensus mechanism, the state channel supports heterogeneous chains and realizes the free addition of arbitrary chains to the multi-chain ecosystem.
- **Bruno**   2020 Q4 Codex 3.0, Codex.IO implements pluggable consensus mechanism, support multiple virtual machine systems, greatly improve the performance of Codex.Relay
- **Galileo** 2021 Q3 Codex 4.0, Multi-chain ecology joins basic services such as IPFS storage, inter-chain message queue, etc.
