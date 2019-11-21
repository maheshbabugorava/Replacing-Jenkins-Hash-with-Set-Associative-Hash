# Replacing-Jenkins-Hash-with-Set-Associative-Hash
## Course Code : CO300
## Assignment : #23
# Overview
Flow Queue Controlled Delay (FQ-CoDel) is a popular queue discipline to address the problem of bufferbloat. FQ part in FQ-CoDel uses a Jenkins hash function to classify the incoming packets into multiple different queues. However, it suffers from the classic birthday problem. The main aim of this project is to replace Jenkins hash in the Linux implementation of FQ-CoDel, and instead, use set-associative hash. 
# Introduction
Bufferbloat is the one of the major issue in the networking field now a days.Bufferbloat is a cause of high latency in packet-switched networks caused by excess buffering of packets.To overcome this problem we use Flow Queue Controlled Delay (FQ-CoDel) which is a popular queue discipline.

The concept of set associativity we use here is similar to the caches that we use in computer architecture, where the idea is used to minimize cache misses and increase overall performance. In the context of managing network flows in queues, this technique is claimed to reduce collisions, as compared to direct hashing. A flow is determined uniquely by the five-tuple (source IP address, destination IP address, source port number, destination port number, protocol number).In linux implementation of FQ-Codel it uses jenkins hash which classifies each packet based on the hash value(called 'skb_get_hash'which uses 5-tuple value of the flow).Now this hash value is scaled down to 1024(flows_count) by using 'reciprocal_scale()' function which is actual queue number to which the packet should be sent.
This above procedure works well for a moderate number of flows. As the number of flows increase, the number of hash collisions also increase. The birthday problem provides a more concrete view of why this occurs. There is a 50% chance of collision occurring as the number of flows reaches sqrt(number of buckets). The main aim of this project is to avoid this and reduce the chances of hash collisions. As mentioned in RFC 8290, with 1024 buckets and perfect hashing, the probability of no collision occurring with 100 flows is 90.78% - this probability can be further improved by using set-associative hashing. According to the RFC's analytical equations, the probability of no collision with an 8-way set associative hash is around 100%, which is a significant improvement over the normal hashing technique.

# working
  As mentioned above, a queue in which the flow has to be added is determined uniquely by the jenkins hash using the five-tuple, we get a 10-bit value which is returned by fq_codel_hash() function which means the value ranges from 0 to 1023. Now as we are using 8-way set-associative hash in which we use divide and rule policy, we divide 1024 queues into 128 sets with 8 queues each. Now we take 10-bit value returned by fq_codel_hash() fuction and store it in a variable named 'idx' and we take modulo 128 of the idx. Now we will get a value ranging from 0 to 127, here we have 128 sets with indices 0 to 127, and we will go to a particular set using this value. Now we will run while loop from 0 to 7 for each flow to determine in which queue they are added. Firstly if the header of the queue is empty then that queue is empty and we can directly add that flow to that queue. If the header of the queue is not empty then there is a flow already added to that queue, now we have to compare the values returned by skb_hash() function of both the flows so as to determine that both of them are of the same flow. If they are same then they are from the same flow and we will append the flow to this queue. If these values are not same then this is a collision and the flow goes to the next queue and this process continues until it finds a particular queue to be added.

## References

* [The Flow Queue CoDel Packet Scheduler and Active Queue Management Algorithm (RFC 8290)](https://tools.ietf.org/html/rfc8290)

* [LINUX Code of FQ_CODEL](https://github.com/torvalds/linux/blob/master/net/sched/sch_fq_codel.c)
