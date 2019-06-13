# CODEX Economic Model Draft Beta 1.0

## 1. Foreword

CODEX introduces a sustainable heterogeneous cross-chain architecture by creating a cross-chain service based on public chains. The architecture provides high-performance heterogeneous cross-chain relay solutions and convenient development tools for parallel chains. The final form is a decentralized multi-chain ecological platform.
CODEX will continuously connect to heterogeneous chains such as BTC/ETH, isomorphic chains such EOSIO and parallel chains such as FORCEIO, forming a completely decentralized multi-chain cross-chain system.

The economic model is the core of the sustainable development of the public chains. Most of the traditional POS token distribution models adopt ICO, that is, about 90% of the tokens are sold through ICO. After the mainnet launches, the nodes can be rewarded with low inflation. The extreme centralization of token distribution leads to a rapid convergence of the system into a centralized organization. The development team got enough money and tokens before the mainnet launched, which led to the lack of enthusiasm of the development team. And it is difficult to upgrade continuously with users' requirements after the mainnet launched. There is a big difference between POS and POW. The holder of POW token is equivalent to obtaining the system's dividend rights. And the decision-making right of the system belongs to the miners and developers who continue to contribute to the system, while the holder of POS token is equivalent to obtaining the system's dividend rights and decision-making rights at the same time. Therefore, how to motivate system contributors fairly and continuously becomes more important under the POS consensus.

CODEX relay chain will be issued with 100% mining, and the development team has no reservation and no fixed block rewards. CODEX distributes tokens to the entire network through a continuous inflation issuance. On this basis, participation in the community at any point in time will not affect fairness. As CODEX connects to more public chain protocols, more heterogeneous chain users can freely participate in CODEX governance and fairly obtain CODEX's system tokens. The development team is also designing a decentralized budget system, a referendum system and a proposal system to ensure that the system is decentralized and sustainable.

The development team built the Codex cross-chain testnet and designed a new Codex economic model draft Beta1.0, hoping to work with the community to explore a more sustainable blockchain.

## 2. Token issuing

Concept:

- CDX: CODEX system token
- Cycle: CODEX will generate one block every 1 second, each BP will generate 3 blocks in succession each time. 21 BPs will generate 63 blocks in turn for one round. One cycle of CODEX is 5 rounds. 315 blocks are generated without missing blocks. If there is a change of term, the current cycle will automatically end.

### 2.1 Inflation

One block will be generated every 1 second, and 630 CDXs will be generated per cycle. The genesis block rewards are 2 CDXs per block. After every 275 cycles, the total number of blocks produced will increase by 1.001 times that of the previous cycle.

### 2.2 Fully fair mining method

#### Holding non-system tokens to obtain the proof of computing power by mortgage

Holding non-system tokens (early support EOS, EOSC, ENU, Telos and other EOSIO tokens, and later will support BTC，ETH and other heterogeneous chain tokens) and mortgaging to the designated contract account of the chain can participate in mining, the account permissions of the corresponding chain are controlled by multi-sign of CODEX block producers. The mortgaged tokens can be withdrawn at any time.
After the non-system token is mortgaged, the CODEX will be obtained as proof of computing power according to the market price (the CODEX exchange ratio on the chain) multiplied by 0.05.

#### Use system token CDX to participate in voting to obtain the computing power

Mining power can also be obtained by participating in the voting mining elections using the system token CDX. Users can start voting by mortgaging the system tokens, and the tokens in the state of mortgage can switch voting nodes at any time. If a node is punished by 2/3 multi-sign of the block-generating BPs because of missing blocks, the voting on the node cannot get dividends during the penalty period.

Every 275 cycles, the system will allocate mining rewards according to the weight of mining computing power on the whole chain. The number of mining rewards obtained by each user = user computing power value / total computing power value.

The specific formula is as follows:

Suppose:

The incentive for block $k$ is $R_k$, and the settlement period $C_k=[Block_k,Block_{k+172800})$ starting from the block $k$.

Let:

- User sets $U=[U_1,U_2, ... , U_n]$
- Token $T=[T_1,T_2, ... , T_n]$
- Mortgage Token Value $ST=[ST_1,ST_2, ... , ST_k]$
- Voting Token Value $VT=[VT_1,VT_2, ... , VT_k]$

In the $C_j$ period, suppose:
Each Token has an average price of $P=[P_1,P_2..., P_k]$ during the $C_j$ period
User $\mathbf{u}$ mortgage $T_k$ Token’s value is ${ST}_k^u={ST}_kP_k$, mortgage block height interval is ${SN}_k^{\mathbf{u}}$,
User $\mathbf{u}$ vote $T_k$ Token’s value is ${VT}_k^u={VT}_kP_k$, voting block height interval is ${VN}_k^{\mathbf{u}}$,

For users' mortgage and voting behaviors within the same period, the system should allocate incentives based on value and behavior duration.

Then for user $\mathbf{u}$ in $C_j$ period, the mortgage reward is:
$$StakeReward_{\mathbf{u},C_j} = ∑_{k∈C_j}R_k × \frac {∑_{i∈T}{ST}_i^\mathbf{u} {SN}_i^\mathbf{ u}} { ∑_{j∈U}∑_{i∈S}{ST}_i^j{SN}_i^j + ∑_{j∈U}∑_{i∈S}{VT}_i^ j{VN}_i^j }$$

The voting rewards are:
$$VoteReward_{\mathbf{u},C_j} = ∑_{k∈C_j}R_k × \frac {∑_{i∈T}{VT}_i^\mathbf{u} {VN}_i^\mathbf{ u}} { ∑_{j∈U}∑_{i∈S}{ST}_i^j{SN}_i^j + ∑_{j∈U}∑_{i∈S}{VT}_i^ j{VN}_i^j }$$

Therefore:
$$∑_{k∈C_j}R_k = ∑_{j∈U}(StakeReward_{\mathbf{u},C_j} + VoteReward_{\mathbf{u},C_j})$$

In the implementation, the system assigns the overall incentive according to the type of Token, and set the incentive of Token $T_m$ in the block $k$ as $R_k^{T_m}$

Considering that the system should make incentive allocation based on value, it should be:
$$∑_{k∈C_j}{SR}_k^{T_m} = ∑_{k∈C_j}R_k × \frac {∑_{\mathscr{u}∈U} {∑_{}{ST}_m^\mathscr{u} {SN}_m^\mathscr{u}} } { ∑_{j∈U}∑_{i∈S}{ST}_i^j{SN}_i^j + ∑_{j∈U}∑_{i∈S}{VT}_i^j{VN}_i^j }$$

$$∑_{k∈C_j}{VR}_k^{T_m} = ∑_{k∈C_j}R_k × \frac {∑_{\mathscr{u}∈U} {∑_{}{VT}_m^\mathscr{u} {VN}_m^\mathscr{u}} } { ∑_{j∈U}∑_{i∈S}{ST}_i^j{SN}_i^j + ∑_{j∈U}∑_{i∈S}{VT}_i^j{VN}_i^j }$$

Hence:
$$∑{k∈C_j}R_k = ∑{T_m∈T}∑{k∈C_j}{SR}k^{T_m} + ∑{T_m∈T}∑{k∈C_j}{VR}_k^{T_m}$$

So for user $\mathbf{u}$, we conclude that:
$$StakeReward_{\mathbf{u},C_j} = ∑_{T_m∈T}∑_{k∈C_j}{SR}_k^{T_m} \frac {{ST}_m^\mathbf{u}{SN}_m^\mathbf{u}} { ∑_{\mathscr{u}∈U} ∑_{}{ST}_m^\mathscr{u} {SN}_m^\mathscr{u} }$$

$$VoteReward_{\mathbf{u},C_j} = ∑_{T_m∈T}∑_{k∈C_j}{VR}_k^{T_m} \frac {{VT}_m^\mathbf{u}{VN}_m^\mathbf{u}} { ∑_{\mathscr{u}∈U} ∑_{}{VT}_m^\mathscr{u} {VN}_m^\mathscr{u} }$$

### 2.3 Mintage cycle

To make the issue of tokens fairer, we designed the mintage cycle. The mintage cycle is the period required to unlock the newly issued tokens. The mintage cycle is one cycle per 2592000 blocks in the slow launch period, and it has an attenuation process in the early stage of the chain. The logic of attenuation is that the mintage cycle decays one block per 2.5 blocks produced, and finally stabilizes at one cycle per 604800 blocks. Tokens in the mintage process cannot be used for transfer or voting.

### 2.4 Slow launch process

In order to allow miners to get a fairer treatment in the early token distribution period, the first 60 days after the mainnet ’s launch was the slow start period.

Dividend parameters during the slow launch period:

- Dividend of initial period: 143
- Dividend increase parameter of every 275 cycles :1.025

Dividend parameters after the slow launch period:

- Dividend of initial period: 630
- Dividend increase parameter of every 275 cycles: 1.001

## 3. Nodes

The nodes are divided into two types: block-generating nodes and profit nodes. The block-generating nodes consist of the first 21 nodes with the highest number of votes. And those after 22nd are profit nodes. If the number of votes is greater than 0.5% of the total mortgage amount of CDX, it can become a profit node. Users can get dividends by voting for block-generating nodes and profit nodes.

### Block-generating rewards and voting fee rewards

Block-generating reward means the reward of the block production, which is set to 3% of the block reward, and it is allocated according to the node's block-generating coinage (continuous block-generating time) as the weight. If there are blocks missing in a cycle, the weight is reset. When the block-generating node is changed, the block weights of all nodes are reset.
The voting reward is the handling fee of user's voting income. The default rate of fee is 10%, and the node can freely design the rate (0%-100%).

The block-generating node needs to mortgage a part of the system tokens as a deposit. If the deposit is lower than the minimum value (currently the amount of coins per block *8228), there will be no block reward and the node cannot run for BP. The deposit will be deducted from the block missing, and twice the deposit of the block reward will be deducted for each less produced block (for example, if 1 token is issued in one block at the time, 2 tokens will be deducted).

The penalty is split equally between users who propose to penalize BP with multi-sign and the top 14 nodes who agree to multi-sign (50% for initiator, 50% for multi-sign). After being kicked out, the node enters a two-day observation period, during which users vote with no mining rewards. After the two-day observation period, BP can apply to return to normal and enter the normal election, obtaining the voting mining income.
If the deposit is insufficient in the process of block producing, the node will be kicked out for 1 hour.

### Genesis Bootstrap Nodes

26 genesis bootstrap nodes will be launched by the development team. The number of votes for the genesis bootstrap nodes are in the form of  ladder votes to facilitate the community BPs to campaign for block-producing BP (the ladder is four 400000 tickets, four 600000 tickets, three 1300000 tickets, three 2100000 tickets, eight 10000000 tickets and five 100000 alternative nodes). The system contract will automatically destroy the lent tokens after one year, during which no account has the right to use these tokens. The genesis bootstrap node has no voting dividends and cannot be voted by the users. The block reward tokens obtained by the genesis bootstrap node will be locked in a foundation account and cannot be misappropriated by anyone. In the future, it will be transferred to the community multi-sign account and be handled by the community.

## 4. Resource usage on the chain

The use of any resource on the CODEX system requires a mortgage system token CODEX. The first 63072000 blocks are in the account minimum-guarantee phase, during which each newly registered user will receive a certain amount of system resources required for daily transfer by default. The following system resources all need to be obtained by mortgaging system tokens. It takes 3 days to withdraw the mortgage status after the token is mortgaged

- Governance voting on the chain
- Node deposit
- Relay verifier deposit
- Register an exchange account and open a transaction pair (only the exchange account can open a transaction pair)
- Use system integration contracts such as transaction/options/loan on the chain

## 5. The decentralized budget system on the chain

Sustainable development is the most important part of a blockchain project. Currently, the budget system of most blockchain projects is divided into two extremes. The POW network that rewards 100% miners cannot guarantee sustainable development by relying on developers to provide volunteer services. Some codebases of POW blockchains have not been updated for a long time. However, although the ICO provided sufficient funds for various projects in the early stage, these funds failed to effectively stimulate the development of the project. Once the ICO was completed, most developers would gradually abandon the project after gaining profits, and only a few developers would continue to work.

We were pleasantly surprised to find that DASH's decentralized budget system is expected to solve the above problems. Based on DASH's budget system, we have made some improvements:
The basic principle of the Codex budget funding system is to determine the allocation of budget funds through node voting.

Anyone can go to the CODEX budget system to apply for a budget proposal (need to pay N CDX as a proposal fee to prevent someone from making a garbage proposal). It is necessary to specify the objectives of the proposal, how it will be implemented, how long it will take and how much it will cost, how much CDX will be required, and what the expected return on the CODEX ecosystem will be.
All current block-generating nodes will periodically review the content of these proposals and vote at their own discretion (agree, disagree, or abstain). If the proposal receives more than two-thirds of the votes, the proposal is approved.

Including the development team's salary budget, promotion budgets of various regions, cooperative budget of wallet and exchange, and other cooperation budgets, etc., each passed proposal will become a DAO (decentralized autonomous organization) that operates spontaneously.

After the proposal is approved, the proposer will receive the amount of CDX applied for in the next block reward to execute the proposal.

## 6. Instructions for adjustment of relevant parameters during Codex beta testing

### 6.1 Cycle

During the test, each BP produces 3 blocks in succession, and 21 BPs produce 63 blocks for one cycle.

### 6.2 Mining rewards payment cycle

During the test, mining rewards will be awarded every 5 cycles.

### 6.3 Slow launch cycle

The slow launch period during the test is 10 mining rewards payment cycles.

### 6.4 Inflation issuance

The initial inflation of the slow launch cycle is 58.6 coins per cycle, that is 0.9301 coins per block, and the growth rate is 1.025. The initial inflation after the slow launch cycle is 126 coins, that is 2 coins per block and the growth rate is 1.001.
