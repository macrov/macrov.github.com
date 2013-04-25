---
layout: post
title: "Bitcoin论文中文版"
date: 2013-04-04 01:56
comments: true
categories: 
---



#Bitcoin:A Peer-to-Peer Electronic Cash System
#比特币:一个基于点对点技术的电子货币系统
 
 Satoshi Nakamoto

 satoshin@gmx.com

 www.bitcoin.org
 

 翻译:Macrov
 
**Abstract.**A purely peer-to-peer version of electronic cash would allow online payments to be sent directly from one party to another without going through a financial institution. Digital signatures provide part of the solution, but the main benefits are lost if a trusted third party is still required to prevent double-spending.We propose a solution to the double-spending problem using a peer-to-peer network.The network timestamps transactions by hashing them into an ongoing chain of hash-based proof-of-work, forming a record that cannot be changed without redoing the proof-of-work. The longest chain not only serves as proof of the sequence of events witnessed, but proof that it came from the largest pool of CPU power. As long as a majority of CPU power is controlled by nodes that are not cooperating to attack the network, they'll generate the longest chain and outpace attackers. The network itself requires minimal structure. Messages are broadcast on a best effort basis, and nodes can leave and rejoin the network at will, accepting the longest proof-of-work chain as proof of what happened while they were gone.

**摘要。**一个完全基于P2P技术的电子货币可以实现不依赖第三方金融机构进行货币交易。数字签名技术也可以实现直接交易，但是缺点是需要一个可信的第三方金融机构，用来保证每笔货币不会被重复消费。对于重复消费问题，我们提供了一种基于P2P网络的解决方案。这个货币系统通过把每一笔交易记录加入到一个不断演变的哈希工作量证明链中，来实现标记每一笔交易。这样生成的记录只有重新完成之前所有的工作量证明运算，才有可能被篡改。最长的证明链不仅表示它代表了对所有交易基于时序的记录，更证明了这个证明链是基于最大CPU计算能力池产生的。只要这个系统中大部分计算能力是被守法用户掌握，守法用户就会生成更长的证明链而保证不会被恶意用户攻击。这个系统的正常运作必须要有一定量的计算节点来保证。交易信息会被尽可能的广播出去，网络中的节点可以随时退出或者再次加入，只需要从新从网络中接受一份最长的证明链就可以保证与系统的同步。

<!-- more -->
##1. Introduction
##1.简介
  Commerce on the Internet has come to rely almost exclusively on financial institutions serving as trusted third parties to process electronic payments. While the system works well enough for most transactions, it still suffers from the inherent weaknesses of the trust based model. Completely non-reversible transactions are not really possible, since financial institutions cannot avoid mediating disputes. The cost of mediation increases transaction costs, limiting the minimum practical transaction size and cutting off the possibility for small casual transactions,and there is a broader cost in the loss of ability to make non-reversible payments for non-reversible services.With the possibility of reversal, the need for trust spreads. Merchants must be wary of their customers, hassling them for more information than they would otherwise need. A certain percentage of fraud is accepted as unavoidable. These costs and payment uncertainties can be avoided in person by using physical currency, but no mechanism exists to make payments over a communications channel without a trusted party.

  网络贸易系统几乎都是基于于一个可信的第三方金融体系来保证交易的进行。大部分情况，这样的系统能够满足交易需求，但是基于信用模型的交易系统都存在本身固有的缺点。因为所有的金融体系都不无法避免的要去解决纠纷，所以交易模型不能做成完全不可逆的。金融仲裁增加了交易成本，限制了特定交易的最小数额，阻碍了使用小额交易。并且不可逆的交易也会带来更多服务费用。由于交易可逆，依赖信用，商人必须提防他的顾客，不得不要求顾客提供更多信息来证明他自己，因为恶意用户是无法避免的。当然这些问题可以由使用实体货币来解决(实际上我们仍然要面对假币，但这就是另外的问题了)，但是我们仍然没有一个可以不依赖第三方，并只通过通信渠道来付款的交易系统。

  What is needed is an electronic payment system based on cryptographic proof instead of trust, allowing any two willing parties to transact directly with each other without the need for a trusted third party. Transactions that are computationally impractical to reverse would protect sellers from fraud, and routine escrow mechanisms could easily be implemented to protect buyers. In this paper, we propose a solution to the double-spending problem using a peer-to-peer distributed timestamp server to generate computational proof of the chronological order of transactions. The system is secure as long as honest nodes collectively control more CPU power than any cooperating group of attacker nodes.

  解决办法是构建一个基于加密算法的支付系统，而不是基于信赖。这个系统允许任意交易双方不通过第三方直接交易。交易通过算法保证不能通过计算来逆操作，实现保护卖方，然后通过托管机制来保证买方（？全部交易的记录所有节点人手一份，有大量的见证人）。这篇文章中，我们提供了一个基于P2P分布式时间戳服务器的重复消费解决方案，每个服务器通过计算产生基于时序的交易验证。只要系统中合法用户的计算力大于攻击者时，系统就是安全可靠的。
  
##2. Transactions
##2. 交易
We define an electronic coin as a chain of digital signatures. Each owner transfers the coin to the next by digitally signing a hash of the previous transaction and the public key of the next owner and adding these to the end of the coin. A payee can verify the signatures to verify the chain of ownership.

我们定义了一种电子货币，这种货币就是一个数字签名的链表。每一个所有者可以把现有的链表通过下一个收款方公钥通过哈希算法签名得到一个新块，然后把这个块添加到现有链表的尾部，最后传递给收款方，收款方可以通过这个签名来验证合法性。
![Image Title](/images/myimage/bitcoin-papper/16-09-49.jpg)  
The problem of course is the payee can't verify that one of the owners did not double-spend the coin. A common solution is to introduce a trusted central authority, or mint, that checks every transaction for double spending. After each transaction, the coin must be returned to the mint to issue a new coin, and only coins issued directly from the mint are trusted not to be double-spent. The problem with this solution is that the fate of the entire money system depends on the company running the mint, with every transaction having to go through them, just like a bank.

问题很明显，收款方仍然不能确定付款方有没有重复消费这笔钱，通常的做法是引入一个可信的中心验证机构。这个机构负责检查所有的交易是否有重复消费。每一次交易后，货币必须回到这个中心验证机构确认没有问题后发行新的货币，只有直接来自这个中心验证机构的货币是可信的。这样的解决方案存在一个问题，就是整个货币系统都是依赖于经营这个中心验证机构的公司，每笔交易都需要通过这个机构，类似一个银行。

  We need a way for the payee to know that the previous owners did not sign any earlier transactions. For our purposes, the earliest transaction is the one that counts, so we don't care about later attempts to double-spend. The only way to confirm the absence of a transaction is to be aware of all transactions. In the mint based model, the mint was aware of all transactions and decided which arrived first. To accomplish this without a trusted party, transactions must be publicly announced [1], and we need a system for participants to agree on a single history of the order in which they were received. The payee needs proof that at the time of each transaction, the majority of nodes agreed it was the first received.
 
  我们需要一个方法让收款方能知道付款方在这次付款之前，没有再给这个链表签名然后传给别的人。为了这个目的，我们只关心第一次签名，其他的签名的都是非法的。确认一个交易是否有重复消费的唯一方式就是检索所有的交易，在之前的中心验证机构模型中，中心验证机构会知道所有的交易，并决定每一笔交易的先后顺序。为了能知道所有的交易并且不需要一个中心验证机构的目的，每一笔交易都要对整个系统进行广播，然后需要一个基于共识的、每个参与者都可以验证每笔交易先后顺序的系统。收款人需要网络中大部分节点都认为他的这次消费是第一次签名才表示这次交易有效。
##3. Timestamp Server
##3. 时间戳服务器
  The solution we propose begins with a timestamp server. A timestamp server works by taking a hash of a block of items to be timestamped and widely publishing the hash, such as in a newspaper or Usenet post [2-5]. The timestamp proves that the data must have existed at the time, obviously, in order to get into the hash. Each timestamp includes the previous timestamp in its hash, forming a chain, with each additional timestamp reinforcing the ones before it.
  
  我们需要一个时间戳服务器，用来把一个包含很多数据的block用hash算法计算出hash值后广泛的广播出去，类似在报纸或者新闻组上贴你软件的MD5.显而易见，时间戳可以证明某次交易的存在，因为要得到这个hash值就必须要这次交易去做hash运算。每一个时间戳都包含了之前一个时间戳，这样就形成了一个链表，每当新加入一个时间戳就会加强这个链表。

![Image Title](/images/myimage/bitcoin-papper/16-43-18.jpg)  
##4. Proof-of-Work
##4. 工作量证明
  To implement a distributed timestamp server on a peer-to-peer basis, we will need to use a proof-of-work system similar to Adam Back's Hashcash [6], rather than newspaper or Usenet posts. The proof-of-work involves scanning for a value that when hashed, such as with SHA-256, the hash begins with a number of zero bits. The average work required is exponential in the number of zero bits required and can be verified by executing a single hash.

  为了实现这个分布式的时间戳系统，我们使用了一个类似Adam Back哈希碰撞的工作量证明系统，而不是去报纸或者新闻组上刊登。工作量证明包括检测hash结果中的特定值，比如检测SHA_256的值，这个hash后的值要从一定数量的0比特位开始。平均工作量会随着0比特的数量呈指数增长，并且可以通过一个简单的hash算法来验证。

  For our timestamp network, we implement the proof-of-work by incrementing a nonce in theblock until a value is found that gives the block's hash the required zero bits. Once the CPU effort has been expended to make it satisfy the proof-of-work, the block cannot be changed without redoing the work. As later blocks are chained after it, the work to change the block would include redoing all the blocks after it.

  对于这个时间戳系统的工作量证明，我们通过寻找一个可以让Block哈希值满足要求数量0比特的随机数来实现，这个随机数是可以增长的。一旦这个值被CPU通过计算尝试出来后，之后只有重做这个链表中所有Block的工作量证明后才可能被修改。

![Image Title](/images/myimage/bitcoin-papper/17-10-41.jpg)  

  The proof-of-work also solves the problem of determining representation in majority decision making. If the majority were based on one-IP-address-one-vote, it could be subverted by anyone able to allocate many IPs. Proof-of-work is essentially one-CPU-one-vote. The majority decision is represented by the longest chain, which has the greatest proof-of-work effort invested in it. If a majority of CPU power is controlled by honest nodes, the honest chain will grow the fastest and outpace any competing chains. To modify a past block, an attacker would have to redo the proof-of-work of the block and all blocks after it and then catch up with and surpass the work of the honest nodes. We will show later that the probability of a slower attacker catching up diminishes exponentially as subsequent blocks are added.

  工作量证明的方式也解决了如何确定多数决策的问题。如果多数派的定义由IP地址数量决定的话，系统可能被拥有大量IP地址的人颠覆。而工作量证明实质上决定了是由CPU数量来决定多数派。多数派决策由链表的长度表现，最长的链表代表了消耗了最多的工作量。只要多数的CPU计算力由合法用户掌握，合法的链表就会以最快的速度增长，并超过所有竞争者。如果想篡改这个链表，攻击者就要从新计算这个链表的所有Block然后追上现在合法的链表。后面我们会展示攻击成功的几率会随着Block数量的增加成指数性的减小。

  To compensate for increasing hardware speed and varying interest in running nodes over time,the proof-of-work difficulty is determined by a moving average targeting an average number of blocks per hour. If they're generated too fast, the difficulty increases.
为了配合不断增长的硬件速度与变化的节点运营利润(interest?)，工作量证明的难度会不断的调整。系统限制了每小时可以制作的Block平均数量，生产的太快难度就会上升，反之就会下降。

##5. Network
##5. 系统网络
The steps to run the network are as follows:

1) New transactions are broadcast to all nodes.

2) Each node collects new transactions into a block. 

3) Each node works on finding a difficult proof-of-work for its block.

4) When a node finds a proof-of-work, it broadcasts the block to all nodes.

5) Nodes accept the block only if all transactions in it are valid and not already spent.

6) Nodes express their acceptance of the block by working on creating the next block in the
chain, using the hash of the accepted block as the previous hash.

运营这个货币网络的步骤：

1)新交易广播到所有节点。

2)每一个节点收集新的交易。

3)每一个节点寻找特定难度的随机数去满足工作量证明。

4)当一个节点找到满足的随机数，完成工作量证明后，把生成的新Block发送回网络到所有节点。

5)节点如果已经有包含所有交易的Block后会接受这个新生产的Block。

6)节点表示接受这个Block，通过使用这个Block去计算新的Block。

  Nodes always consider the longest chain to be the correct one and will keep working on extending it. If two nodes broadcast different versions of the next block simultaneously, some nodes may receive one or the other first. In that case, they work on the first one they received, but save the other branch in case it becomes longer. The tie will be broken when the next proof-of-work is found and one branch becomes longer; the nodes that were working on the other branch will then switch to the longer one.

  节点永远认为最长的链表是正确并尝试去计算出下一个Block。如果两个节点同时发现并广播了不同版本的新Block，一部分节点会先收到其中的一个，这种情况下节点会同时保留两个版本的Block，并开始基于第一个收到的Block计算新的Block，当下一个节点确认后就会放弃中间一个不符合的。

  New transaction broadcasts do not necessarily need to reach all nodes. As long as they reach many nodes, they will get into a block before long. Block broadcasts are also tolerant of dropped messages. If a node does not receive a block, it will request it when it receives the next block and realizes it missed one.

  新的交易不需要到达所有的节点。当新交易到达很多的节点后，它们会很快被包含进一个新的Block，Block的广播机制也对信息丢失有一定的容忍度。当一个节点收到一个新Block并发现它丢失了一个后会向其他节点请求补上这个节点信息。

##6. Incentive
##6. 奖励机制
  By convention, the first transaction in a block is a special transaction that starts a new coin owned by the creator of the block. This adds an incentive for nodes to support the network, and provides a way to initially distribute coins into circulation, since there is no central authority to issue them. The steady addition of a constant of amount of new coins is analogous to gold miners expending resources to add gold to circulation. In our case, it is CPU time and electricity that is expended.

  我们约定，第一个发现计算出新Block的节点可以生产一定数量的新货币作为支持这个系统的奖励。并提供一个向系统注入新货币的机制，用以代替我们取消掉的中心验证机构的职能。稳定数量的新货币被注入到我们的网络货币系统中，就像现实中淘金者消耗资源去发现新金矿并注入到流通领域一样，只不过我们消耗的是CPU时间和电能。

  The incentive can also be funded with transaction fees. If the output value of a transaction is less than its input value, the difference is a transaction fee that is added to the incentive value of the block containing the transaction. Once a predetermined number of coins have entered circulation, the incentive can transition entirely to transaction fees and be completely inflation free.

  另一个奖励机制是交易费。一次交易的输入和输出差值会被当做交易费奖励给记录这次交易的节点。当整个环境中已经生产出所有货币后，交易费会变成唯一的奖励机制。

  The incentive may help encourage nodes to stay honest. If a greedy attacker is able to assemble more CPU power than all the honest nodes, he would have to choose between using it to defraud people by stealing back his payments, or using it to generate new coins. He ought to find it more profitable to play by the rules, such rules that favour him with more new coins than everyone else combined, than to undermine the system and the validity of his own wealth.

  奖励机制可以促进节点保持守法。如果一个贪婪的攻击者有能力阻止比所有守法节点更多的计算力的话，这个攻击者就能通过伪造历史来从其他用户手里偷取货币，或者去制造新的货币。他也能找到更多的邪恶玩法，但是规则会使他更倾向去制造新货币而不是破坏这个系统。

##7. Reclaiming Disk Space
##7. 磁盘空间释放

  Once the latest transaction in a coin is buried under enough blocks, the spent transactions before it can be discarded to save disk space. To facilitate this without breaking the block's hash, transactions are hashed in a Merkle Tree [7][2][5], with only the root included in the block's hash. Old blocks can then be compacted by stubbing off branches of the tree. The interior hashes do not need to be stored.

  一旦最新的交易被包含进Block后，这个交易的记录就可以被释放掉，以节省硬盘空间。为了不影响Block的哈希运算，交易记录被保存在一个哈希树中[7][2][5]，只有根记录保存在Block的header中。旧的Block可以通过删掉哈希树的左树来压缩。内部哈希结果也不需要再保存。

![Image Title](/images/myimage/bitcoin-papper/21-46-37.jpg)  

  A block header with no transactions would be about 80 bytes. If we suppose blocks are generated every 10 minutes, 80 bytes * 6 * 24 * 365 = 4.2MB per year. With computer systems typically selling with 2GB of RAM as of 2008, and Moore's Law predicting current growth of 1.2GB per year, storage should not be a problem even if the block headers must be kept in memory.

  一个Block的header如果没有交易记录的话大概是80字节大小。假设每十分钟产生一个新的Block，80字节 * 6(每小时) * 24(每天)
* 365(每年) = 4.2兆字节。 按照2008年主流配置是2GB内存和考虑到摩尔定律大概内存会每年增长1.2GB，所以就算所有的Block Header都保存在内存应该也不是什么问题。

##8. Simplified Payment Verification
##8. 交易确认的简化
  It is possible to verify payments without running a full network node. A user only needs to keep a copy of the block headers of the longest proof-of-work chain, which he can get by querying network nodes until he's convinced he has the longest chain, and obtain the Merkle branch linking the transaction to the block it's timestamped in. He can't check the transaction for himself, but by linking it to a place in the chain, he can see that a network node has accepted it, and blocks added after it further confirm the network has accepted it.
  
  一笔交易可以不传递到所有节点就得到确认。一个用户在他确定他手上的是最长链，并且得到一个包含这笔交易的哈希树之前，只需要保留一份最长链Block的header。这个用户无法自己验证交易的有效性，只能是通过把交易加入到交易链，并且看到包含这个交易链的Block被网络中的节点接受后才能验证。

![Image Title](/images/myimage/bitcoin-papper/22-11-28.jpg)  

  As such, the verification is reliable as long as honest nodes control the network, but is more vulnerable if the network is overpowered by an attacker. While network nodes can verify transactions for themselves, the simplified method can be fooled by an attacker's fabricated transactions for as long as the attacker can continue to overpower the network. One strategy to protect against this would be to accept alerts from network nodes when they detect an invalid block, prompting the user's software to download the full block and alerted transactions to confirm the inconsistency. Businesses that receive frequent payments will probably still want to run their own nodes for more independent security and quicker verification.

  这样来看，只要货币网络被守法节点控制，我们的验证方式就是可靠的。如果非法攻击者有比守法节点更强大的计算力，攻击者就可以一直伪造交易了。一种防范的策略是允许节点接受网络中其他节点发送的警报信息，警报信息会在节点发现非法Block时发出，用来提醒用户软件去下载完整的Block信息去验证这个异常。有频繁交易话也可以运行自己的节点达到更独立更准确的交易确认。

##9. Combining and Splitting Value
##9. 货币的合并于分割
  Although it would be possible to handle coins individually, it would be unwieldy to make a separate transaction for every cent in a transfer. To allow value to be split and combined, transactions contain multiple inputs and outputs. Normally there will be either a single input from a larger previous transaction or multiple inputs combining smaller amounts, and at most two outputs: one for the payment, and one returning the change, if any, back to the sender.

  尽管对每一分钱都可以独立操作，但是真的一分钱一分钱的消费实在没必要。为了允许货币进行分割和合并，交易被设计成可有多个输入和输出组成。一般来讲输入可以是单独一个大额交易，或者是几个小的交易。而输出则最多有两个：一个是支付，一个是找零---会返还给支付者。
![Image Title](/images/myimage/bitcoin-papper/21-59-01.jpg)  

  It should be noted that fan-out, where a transaction depends on several transactions, and those transactions depend on many more, is not a problem here. There is never the need to extract a complete standalone copy of a transaction's history. 

  对于一笔依赖于其它交易，而其它交易又依赖更多其它交易的现象，因为我们永远没有必要去提取出一笔交易的完整依赖关系，所以也就没有什么问题了。
##10. Privacy
##10. 隐私
  The traditional banking model achieves a level of privacy by limiting access to information to the parties involved and the trusted third party. The necessity to announce all transactions publicly precludes this method, but privacy can still be maintained by breaking the flow of information in another place: by keeping public keys anonymous. The public can see that someone is sending an amount to someone else, but without information linking the transaction to anyone. This is similar to the level of information released by stock exchanges, where the time and size of individual trades, the "tape", is made public, but without telling who the parties were.

  传统银行体系保管的隐私仍然会向政府和一些可信的第三方组织泄露。任何交易显然都不想被其他人监控，比特币通过匿名公钥的方式来实现这种隐私保障。每个人可以看到货币系统中有人给了另一个人一笔钱，但是具体的交易信息是只有交易双方知道的。方式有点类似股票交易，所有人知道股票交易的时间和数额，这些信息是公开的，但是，没有人会知道交易双方是谁。

![Image Title](/images/myimage/bitcoin-papper/22-17-33.jpg)  

  As an additional firewall, a new key pair should be used for each transaction to keep them from being linked to a common owner. Some linking is still unavoidable with multi-input transactions, which necessarily reveal that their inputs were owned by the same owner. The risk is that if the owner of a key is revealed, linking could reveal other transactions that belonged to the same owner.

  还需要一对密钥来保证每笔交易不会被泄露给一个共有账户？（common owner）。当多来源的交易发生时，还有一个风险就是很容易判断，这多个来源都属于同一个人。

##11. Calculations
##11. 计算
  We consider the scenario of an attacker trying to generate an alternate chain faster than the honest chain. Even if this is accomplished, it does not throw the system open to arbitrary changes, such as creating value out of thin air or taking money that never belonged to the attacker. Nodes are not going to accept an invalid transaction as payment, and honest nodes will never accept a block containing them. An attacker can only try to change one of his own transactions to take back money he recently spent.

  我们有考虑有攻击者比守法节点更快的生成新链的情况，事实上就算攻击者成功超过守法节点的生成速度了也不会对货币造成什么严重的改变，例如凭空造钱或者拿走别人的钱。守法节点永远不会接受非法的交易，也不会接受包含它们的Block。攻击者只能试图把他近期花的钱拿回来。

  The race between the honest chain and an attacker chain can be characterized as a Binomial Random Walk. The success event is the honest chain being extended by one block, increasing its lead by +1, and the failure event is the attacker's chain being extended by one block, reducing the gap by -1.

  守法节点和非法攻击者之间的竞争关系可以用二叉树随机漫步（Binomial Random Walk）来描述，成功事件是合法的链被延长一个Block，失败事件是攻击者的非法链被延长了一个Block。

  The probability of an attacker catching up from a given deficit is analogous to a Gambler's Ruin problem. Suppose a gambler with unlimited credit starts at a deficit and plays potentially an infinite number of trials to try to reach breakeven. We can calculate the probability he ever reaches breakeven, or that an attacker ever catches up with the honest chain, as follows [8]:

  攻击者追上合法链的可能性类似于赌徒破产问题。假设赌徒有无限的钱可以去尝试赌博并偿还他自己的亏空。我们可以计算赌徒补偿上亏空，既追上合法链的概率：

![Image Title](/images/myimage/bitcoin-papper/22-03-56.jpg)  

  Given our assumption that p > q, the probability drops exponentially as the number of blocks the attacker has to catch up with increases. With the odds against him, if he doesn't make a lucky lunge forward early on, his chances become vanishingly small as he falls further behind.

  假设p>p，赌徒补上亏空的概率会随着之间相差Block数量的增长成指数的下降。所以对于攻击者的成功几率，除非他开始走大运迅速追上合法链，否则随着合法链的不断延长，攻击者成功的几率就越来越小了。

  We now consider how long the recipient of a new transaction needs to wait before being sufficiently certain the sender can't change the transaction. We assume the sender is an attacker who wants to make the recipient believe he paid him for a while, then switch it to pay back to himself after some time has passed. The receiver will be alerted when that happens, but the sender hopes it will be too late.

  现在我们来讨论下收款人要多久才能肯定得认为付款是有效的。假设付款人是一个攻击者，试图让收款人认为他成功付款后再让钱回到自己钱包。收款人发现这个付款人这样做后，会马上发出非法交易的警报，但是攻击者希望这时他已经成功拿回了他付出去的钱。

  The receiver generates a new key pair and gives the public key to the sender shortly before signing. This prevents the sender from preparing a chain of blocks ahead of time by working on it continuously until he is lucky enough to get far enough ahead, then executing the transaction at that moment. Once the transaction is sent, the dishonest sender starts working in secret on a parallel chain containing an alternate version of his transaction.

  收款人会生成一对新密钥，然后把公钥交给付款人用来签名，这样可以防止攻击者有预谋的提前计算出很多Block使非法链长度在一定程度上领先于守法用户，当交易发生后，攻击者把篡改后的交易加到自己提前算好的Block链中使其他节点认为他的链是正确的。

  The recipient waits until the transaction has been added to a block and z blocks have been linked after it. He doesn't know the exact amount of progress the attacker has made, but assuming the honest blocks took the average expected time per block, the attacker's potential progress will be a Poisson distribution with expected value:
  
  收款人在交易被加入到一个Block并且之后有z个Block前一直等待，收款人不知道攻击者已经计算出了多少Block，只能认为守法节点按照平均速度去计算每一个Block，攻击者计算Block的进度可能性否和泊松分布：

![Image Title](/images/myimage/bitcoin-papper/22-06-13.jpg)  

  To get the probability the attacker could still catch up now, we multiply the Poisson density for each amount of progress he could have made by the probability he could catch up from that point:

  为了得出攻击者赶上的概率，我们给攻击者可能的计算概率都乘上泊松密度：
![Image Title](/images/myimage/bitcoin-papper/22-06-38.jpg)  

  Rearranging to avoid summing the infinite tail of the distribution...
    
  然后重排下公式，消除无穷大。
![Image Title](/images/myimage/bitcoin-papper/22-06-54.jpg)  

  Converting to C code...

  C代码下的实现...
```
#include <math.h>
double AttackerSuccessProbability(double q, int z)
{
double p = 1.0 - q;
double lambda = z * (q / p);
double sum = 1.0;
int i, k;
for (k = 0; k <= z; k++)
{
double poisson = exp(-lambda);
for (i = 1; i <= k; i++)
poisson *= lambda / i;
sum -= poisson * (1 - pow(q / p, z - k));
}
return sum;
}
```
  Running some results, we can see the probability drop off exponentially with z.

  通过计算，可以看出攻击者追上的概率会随着Z的增长呈指数下降。
![Image Title](/images/myimage/bitcoin-papper/22-08-37.jpg)  
  Solving for P less than 0.1%...

  然后看下概率小于0.1%要满足的条件...
![Image Title](/images/myimage/bitcoin-papper/22-08-52.jpg)  
##12. Conclusion
##12. 结论
  We have proposed a system for electronic transactions without relying on trust. We started with the usual framework of coins made from digital signatures, which provides strong control of ownership, but is incomplete without a way to prevent double-spending. To solve this, we proposed a peer-to-peer network using proof-of-work to recor
