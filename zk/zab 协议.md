### ZAB 协议

1. ZAB 协议全称：Zookeeper Atomic Broadcast（Zookeeper 原子广播协议）。
2. Zookeeper 是一个为分布式应用提供高效且可靠的分布式协调服务。在解决分布式一致性方面，Zookeeper 并没有使用 Paxos ，而是采用了 ZAB 协议。
3. ZAB 协议定义：**ZAB 协议是为分布式协调服务 Zookeeper 专门设计的一种支持 `崩溃恢复` 和 `原子广播` 协议**。下面我们会重点讲这两个东西。


