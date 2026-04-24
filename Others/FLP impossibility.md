The **FLP impossibility result** states that in an **asynchronous distributed system** with even a single faulty process, it is **impossible** to design a deterministic consensus algorithm that guarantees both **safety** and **liveness**. If messages can be delayed arbitrarily and processes can fail, no protocol can ensure that all non-faulty nodes always decide on the same value within a bounded time. 

The  limitation of purely asynchronous systems forces the use of other solutions like **randomized algorithms**, **partial synchrony**, or **timeouts with view changes**.
