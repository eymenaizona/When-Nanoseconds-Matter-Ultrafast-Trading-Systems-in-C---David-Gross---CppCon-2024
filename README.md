# When-Nanoseconds-Matter-Ultrafast-Trading-Systems-in-C---David-Gross---CppCon-2024


## Low Latency Trading Systems in C++

Welcome, dear readers, to an exploration into the engineering of low latency trading systems, specifically using C++. This article distills insights shared during a presentation on creating these high-performance financial market engines. We will traverse through historical, technical, and practical aspects, emphasizing engineering principles and profiling techniques that promise to aid any developer venturing into the realm of low latency trading systems.

### A Historical Lens: The Roman Empire and Early Derivative Trading

To understand the significance and evolution of trading systems, we must time travel back about two millennia to the Roman Empire. The Romans demonstrated remarkable planning and organizational skills, often credited for their long-lasting empire and vast territories.

One of their ingenious inventions was the Roman Future Contracts, an early form of derivative trading. To manage the uncertainties involved in organizing massively attended events like festivals, where food supply and other essentials had to be guaranteed well in advance, the Romans asked farmers and other suppliers to commit to future prices. This practice reduced world uncertainty but also introduced liquidity challenges, paving the way for modern market mechanisms.

### Market Making and the Need for Low Latency

In today's financial markets, market makers serve a crucial role by maintaining liquidity. Engaging in what is referred to as a "loser's game," market makers must consistently perform well in various aspects of the trade, often making small but steady profits while avoiding large losses. The necessity to react swiftly to market changes and maintain accurate prices drives the need for low latency systems.
Combination of:
-being fast: you need to react quickly enough 
-being smart: you need the correct prices ; which is related to being fast

### Why Low Latency in Market Making?
FPGA expensive not card itself but engineering, software is more flexible. Finding the right solution to right problem.
FPGAs blazing fast however need to be functionally simple (not in engineering).
Strategy require low latency.

![Screenshot from 2024-10-18 13-09-51](https://github.com/user-attachments/assets/b2b0599e-273e-4b3c-8b95-7f43bebebeab)

Two primary reasons drive the demand for low latency:


1. **Rapid Reaction to Events:** Market makers must promptly update their prices or cancel orders in response to market news or events.
2. **Accuracy and Model Sophistication:** High-frequency trading requires precision. Accurate pricing is directly tied to how quickly market data can be processed and utilized.
### Order Book Challenges
* Latency constraint for algo trading, Its not just about being fast to send and order, but being fast to be accurate
* Latency constraint to not drop packets on the NIC, Limited number of buffers, Not reading fast enough will cause the application to drop packet, causing an outage,
### The Backbone of Trading Systems: The Order Book Data Structure

An essential component of trading systems is the order book, which tracks the buy and sell interest in financial instruments. Representing prices where transactions can occur, the order book must handle high-frequency data efficiently. Two crucial requirements are:

1. **Maintaining Ordered Sequences:** Prices need to be ordered for quick access to the best bid and ask.
2. **Fast Retrieval and Update:** Real-time trading demands swift updates to the order book.

#### Implementing the Order Book: Data Structures 

The natural choice for managing an ordered sequence of prices is the `std::map` in C++, offering logarithmic complexity for insertions and lookups. However, `std::map` has poor cache locality due to its node-based structure.

On the other hand, `std::vector` offers better cache locality and is thus faster for certain use cases. Yet, `std::vector` has linear complexity for insert operations since all following elements need to be shifted when a new price level is added. The innovative solution was to reverse the `std::vector`, minimizing element shifts by leveraging the fact that most updates occur at the top of the book.
![Screenshot from 2024-10-18 13-32-06](https://github.com/user-attachments/assets/73aa681d-71a9-41c9-a01a-d553a4a635d5)

### Principles for Low Latency Systems

Several guiding principles are invaluable in engineering low latency systems:

#### Principle 1: Avoid Node-Based Containers

Node-based containers like `std::map` or `std::unordered_map` are often slower due to poor cache locality. Instead, prefer contiguous memory containers like `std::vector` but in reverse .
![Screenshot from 2024-10-18 13-38-54](https://github.com/user-attachments/assets/28a12055-9f9d-4136-a864-53f419497661)

#### Principle 2: Leverage Specific Problem Properties

Understanding the specific nuances of the problem allows you to tailor your data structures and algorithms for optimal performance. For example, reversing the `std::vector` took advantage of the distribution of updates within the order book.

#### Principle 3: Embrace Simplicity

Simple solutions are often the fastest. The linear search within a `std::vector` proved to be the most efficient, leveraging caching benefits and simplicity.

#### Principle 4: Employ Mechanical Sympathy

Develop algorithms that appreciate and exploit hardware properties—cache lines, CPU pipelines, and branch prediction—to ensure high performance. 

### Profiling Techniques

Effective profiling is essential to identify and mitigate performance bottlenecks. Tools and methods discussed include:


- **Perf and perf-stat:** Useful for measuring various performance metrics like CPU instructions per cycle.
- **Top-Down Microarchitecture Analysis Method (TMA):** Classifies CPU cycles into categories that help identify bottlenecks.
- **Branchless Algorithms:** Reducing branch mispredictions can significantly improve performance in high-frequency trading scenarios.(Branchless binary search)
![Screenshot from 2024-10-18 13-44-23](https://github.com/user-attachments/assets/ed9010c5-1287-47f8-9125-71b7ca46b601)

### Networking and Concurrency

Networking in low latency systems bypasses the kernel to reduce delays and jitter. 
Userspace networking:
* Solarflare - industry standar low-latency NIC

* OpenOnload
  -Easy to use, compatible with BSD sockets

* TCPDirect
  -Custom TCP/UDP stack
  -Reduced set of features

* EF_VI
  -Layer 2 api (similar to DPDK)
  -lowest latency

#### Principle 5: Bypass the Kernel

User-space networking ensures the fast and efficient transmission of market data.

For concurrency, shared memory offers the fastest inter-process communication. The preferred design is a single-writer, multiple-reader setup to avoid the complexity and added latencies of more intricate designs.

Why Shared memory:
* If you dont need sockets, no need to pat for their complexity
* as fast as it gets
* Kernel is not involved in  any operations
* Multi processes requires it- which is good for minimizing operational risk
- Shared memory work well with
  * contigiouse blocks of data; arrays!
  * one writer, one or multiple readers -> avoid multiple writers!!

### Maintaining and Monitoring Performance

Profiling goes beyond initial implementation. Continuous monitoring ensures sustained performance, setting up alerts and audits to catch deviations early.

#### Principle 6: Continuous Profiling

Implement metrics and alarms to alert on performance degradations to maintain the system's efficiency.

### Final Thoughts

In conclusion, engineering low latency trading systems is an intricate balance of simplicity, mechanical sympathy, and continuous performance monitoring. Drawing inspiration from ancient practices like the Roman future contracts and employing modern hardware-aware algorithms pave the way for efficient and accurate trading systems.

Remaining disciplined in adhering to these principles ensures that not only are our systems fast today, but they remain robust and performant well into the future.

Thank you for joining this deep dive into the fascinating world of low latency trading systems with C++. Your journey to creating efficient, high-speed market engines is paved with the disciplined application of these engineering insights.## Low Latency Trading Systems in C++

Welcome, dear readers, to an exploration into the engineering of low latency trading systems, specifically using C++. This article distills insights shared during a presentation on creating these high-performance financial market engines. We will traverse through historical, technical, and practical aspects, emphasizing engineering principles and profiling techniques that promise to aid any developer venturing into the realm of low latency trading systems.

### A Historical Lens: The Roman Empire and Early Derivative Trading

To understand the significance and evolution of trading systems, we must time travel back about two millennia to the Roman Empire. The Romans demonstrated remarkable planning and organizational skills, often credited for their long-lasting empire and vast territories.

One of their ingenious inventions was the Roman Future Contracts, an early form of derivative trading. To manage the uncertainties involved in organizing massively attended events like festivals, where food supply and other essentials had to be guaranteed well in advance, the Romans asked farmers and other suppliers to commit to future prices. This practice reduced world uncertainty but also introduced liquidity challenges, paving the way for modern market mechanisms.

### Market Making and the Need for Low Latency

In today's financial markets, market makers serve a crucial role by maintaining liquidity. Engaging in what is referred to as a "loser's game," market makers must consistently perform well in various aspects of the trade, often making small but steady profits while avoiding large losses. The necessity to react swiftly to market changes and maintain accurate prices drives the need for low latency systems.
Combination of:
-being fast: you need to react quickly enough 
-being smart: you need the correct prices ; which is related to being fast

### Why Low Latency in Market Making?

Two primary reasons drive the demand for low latency:

1. **Rapid Reaction to Events:** Market makers must promptly update their prices or cancel orders in response to market news or events.
2. **Accuracy and Model Sophistication:** High-frequency trading requires precision. Accurate pricing is directly tied to how quickly market data can be processed and utilized.

### The Backbone of Trading Systems: The Order Book Data Structure

An essential component of trading systems is the order book, which tracks the buy and sell interest in financial instruments. Representing prices where transactions can occur, the order book must handle high-frequency data efficiently. Two crucial requirements are:

1. **Maintaining Ordered Sequences:** Prices need to be ordered for quick access to the best bid and ask.
2. **Fast Retrieval and Update:** Real-time trading demands swift updates to the order book.

#### Implementing the Order Book: Data Structures 

The natural choice for managing an ordered sequence of prices is the `std::map` in C++, offering logarithmic complexity for insertions and lookups. However, `std::map` has poor cache locality due to its node-based structure.

On the other hand, `std::vector` offers better cache locality and is thus faster for certain use cases. Yet, `std::vector` has linear complexity for insert operations since all following elements need to be shifted when a new price level is added. The innovative solution was to reverse the `std::vector`, minimizing element shifts by leveraging the fact that most updates occur at the top of the book.

### Principles for Low Latency Systems

Several guiding principles are invaluable in engineering low latency systems:

#### Principle 1: Avoid Node-Based Containers

Node-based containers like `std::map` or `std::unordered_map` are often slower due to poor cache locality. Instead, prefer contiguous memory containers like `std::vector`.

#### Principle 2: Leverage Specific Problem Properties

Understanding the specific nuances of the problem allows you to tailor your data structures and algorithms for optimal performance. For example, reversing the `std::vector` took advantage of the distribution of updates within the order book.

#### Principle 3: Embrace Simplicity

Simple solutions are often the fastest. The linear search within a `std::vector` proved to be the most efficient, leveraging caching benefits and simplicity.

#### Principle 4: Employ Mechanical Sympathy

Develop algorithms that appreciate and exploit hardware properties—cache lines, CPU pipelines, and branch prediction—to ensure high performance. 

### Profiling Techniques

Effective profiling is essential to identify and mitigate performance bottlenecks. Tools and methods discussed include:

- **Perf and perf-stat:** Useful for measuring various performance metrics like CPU instructions per cycle.
- **Top-Down Microarchitecture Analysis Method (TMA):** Classifies CPU cycles into categories that help identify bottlenecks.
- **Branchless Algorithms:** Reducing branch mispredictions can significantly improve performance in high-frequency trading scenarios.

### Networking and Concurrency

Networking in low latency systems bypasses the kernel to reduce delays and jitter. Technologies like Solarflare and libraries like `DPDK` from Intel are instrumental for low latency networking.

#### Principle 5: Bypass the Kernel

User-space networking ensures the fast and efficient transmission of market data.

For concurrency, shared memory offers the fastest inter-process communication. The preferred design is a single-writer, multiple-reader setup to avoid the complexity and added latencies of more intricate designs.

### Maintaining and Monitoring Performance

Profiling goes beyond initial implementation. Continuous monitoring ensures sustained performance, setting up alerts and audits to catch deviations early.

#### Principle 6: Continuous Profiling

Implement metrics and alarms to alert on performance degradations to maintain the system's efficiency.

### Final Thoughts

In conclusion, engineering low latency trading systems is an intricate balance of simplicity, mechanical sympathy, and continuous performance monitoring. Drawing inspiration from ancient practices like the Roman future contracts and employing modern hardware-aware algorithms pave the way for efficient and accurate trading systems.

Remaining disciplined in adhering to these principles ensures that not only are our systems fast today, but they remain robust and performant well into the future.

Thank you for joining this deep dive into the fascinating world of low latency trading systems with C++. Your journey to creating efficient, high-speed market engines is paved with the disciplined application of these engineering insights.
