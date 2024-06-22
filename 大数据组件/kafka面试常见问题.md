<!--
 * @Author: kekexili1230 457738564@qq.com
 * @Date: 2024-01-19 16:48:14
 * @LastEditors: kekexili1230 457738564@qq.com
 * @LastEditTime: 2024-01-19 17:05:50
 * @FilePath: /mynote/面试常见问题/kafka面试常见问题.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE 
-->
# kafka常见面试题

## kafka数据一致性原理

- 分区副本：Kafka将每个主题分成多个分区，并将每个分区的数据副本保存在多个服务器上。这样，当某个服务器故障时，仍然可以从其他服务器获取数据，从而保证数据的可靠性。

- Leader选举：kafka会在ISR集合中选举一个副本作为Leader，leader负责处理所有的读写请求，follower负责从leader同步数据

- ISR机制：所有的follower从leader同步数据，同步数据相关leader有一定的延迟；如果延迟在一定范围内，则属于ISR；Leader从ISR中选举。所有ISR集合中分区副本的最小LEO称为HW，消费者可以拉取HW之前的消息。




