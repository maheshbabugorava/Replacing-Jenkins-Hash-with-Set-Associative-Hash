# Replacing-Jenkins-Hash-with-Set-Associative-Hash
## Course Code : CO300
## Assignment : #23
# Overview
Flow Queue Controlled Delay (FQ-CoDel) is a popular queue discipline to address the problem of bufferbloat. FQ part in FQ-CoDel uses a Jenkins hash function to classify the incoming packets into multiple different queues. However, it suffers from the classic birthday problem. The main aim of this project is to replace Jenkins hash in the Linux implementation of FQ-CoDel, and instead, use set-associative hash. 
# Introduction
Bufferbloat is the one of the major issue in the networking field now a days.Bufferbloat is a cause of high latency in packet-switched networks caused by excess buffering of packets.To overcome this problem we use Flow Queue Controlled Delay (FQ-CoDel) which is a popular queue discipline.

The concept of set associativity we use here is similar to the caches that we use in computer architecture, where the idea is used to minimize cache misses and increase overall performance. In the context of managing network flows in queues, this technique is claimed to reduce collisions, as compared to direct hashing. A flow is determined uniquely by the five-tuple (source IP address, destination IP address, source port number, destination port number, protocol number).In linux implementation of FQ-Codel it uses jenkins hash which classifies each packet based on the hash value(called 'skb_get_hash'which uses 5-tuple value of the flow).Now this hash value is scaled down to 1024(flows_count) by using 'reciprocal_scale()' function which is actual queue number to which the packet should be sent.
This above procedure works well for a moderate number of flows. As the number of flows increase, the number of hash collisions also increase. The birthday problem provides a more concrete view of why this occurs. There is a 50% chance of collision occurring as the number of flows reaches sqrt(number of buckets). The main aim of this project is to avoid this and reduce the chances of hash collisions. As mentioned in RFC 8290, with 1024 buckets and perfect hashing, the probability of no collision occurring with 100 flows is 90.78% - this probability can be further improved by using set-associative hashing. According to the RFC's analytical equations, the probability of no collision with an 8-way set associative hash is around 100%, which is a significant improvement over the normal hashing technique.

# Refernces 
   [The Flow Queue CoDel Packet Scheduler and Active Queue Management Algorithm (RFC 8290)](https://tools.ietf.org/html/rfc8290)
   
      This memo presents the FQ-CoDel hybrid packet scheduler and Active Queue Management (AQM) algorithm, a powerful tool for fighting      bufferbloat and reducing latency.
   
   [LINUX Code of FQ_CODEL](https://github.com/torvalds/linux/blob/master/net/sched/sch_fq_codel.c)
   
       The Linux implementation of FQ_CODEL
   
   
   
