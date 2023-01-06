突发缓存文件系统（Burst Buffer File System/Ad Hoc File System）

开源的突发缓存文件系统如下所示：

|      文件系统      |     国家     | 发布机构                                               | 年份 | 编程语言 | 开源链接                                  |
| :----------------: | :----------: | :----------------------------------------------------- | ---- | :------: | :---------------------------------------- |
|  BeeOND \[1]\[2]   |     德国     | ThinkParQ公司                                          | 2015 |   C++    | https://git.beegfs.io/pub                 |
|  BurstFS \[3]\[4]  |     美国     | 佛罗里达州立大学；<br>劳伦斯利弗莫尔国家实验室（LLNL） | 2015 |    C     | https://github.com/LLNL/burstfs           |
|    UnifyFS \[5]    |     美国     | LLNL                                                   | 2017 |    C     | https://github.com/LLNL/UnifyFS           |
|  GekkoFS \[6]\[7]  | 德国；西班牙 | （德国）美因茨大学；<br>（西班牙）巴塞罗那超算中心等   | 2018 |   C++    | https://storage.bsc.es/gitlab/hpc/gekkofs |
|     MadFS [8]      |     中国     | 清华大学                                               | 2020 |   Rust   | https://github.com/madsys-dev/MadEngine   |
| Gfarm/BB \[9]\[10] |     日本     | 筑波大学等                                             | 2019 |    C     | https://github.com/oss-tsukuba/gfarm      |
|     CHFS [11]      |     日本     | 筑波大学等                                             | 2020 |    C     | https://github.com/otatebe/chfs           |



### 参考文献

1. BeeGFS Changelog (2015.03 Release Series), https://www.beegfs.io/release/beegfs_2015.03/Changelog.txt
2. BeeOND: BeeGFS On Demand, https://doc.beegfs.io/latest/advanced_topics/beeond.html
3. Teng Wang, et al. BurstFS: A Distributed Burst Buffer File System for Scientific Applications, SC'15. [[Poster](http://sc15.supercomputing.org/sites/all/themes/SC15images/tech_poster/poster_files/post244s2-file2.pdf)] [[PDF](http://sc15.supercomputing.org/sites/all/themes/SC15images/tech_poster/poster_files/post244s2-file3.pdf)]
4. Teng Wang, et al. An Ephemeral Burst-Buffer File System for Scientific Applications, SC'16. 
5. AdamMoody, et al. UnifyFS: A Distributed Burst Buffer File System - 0.1.0, https://doi.org/10.11578/dc.20200519.19
6. Marc-Andre ́ Vef, et al. GekkoFS - A Temporary Distributed File System for HPC Applications, CLUSTER'18
7. Marc-Andre ́ Vef, et al. GekkoFS — A Temporary Burst Buffer File System for HPC Applications, JCST‘20. [[PDF](https://link.springer.com/content/pdf/10.1007/s11390-020-9797-6.pdf)]
8. 陈康, 武永卫, 郑纬民. MadFS：高性能超算缓存文件系统, 大数据'21. [[HTML](http://www.infocomm-journal.com/bdr/article/2021/2096-0271/2096-0271-7-3-00150.shtml)] [[中国Rust开发者大会](https://www.bilibili.com/video/BV1Ni4y1w731/?spm_id_from=333.337.search-card.all.click&vd_source=72cd48a0605765ff6909f8b522dd292b)] [[中国Lustre用户峰会]](https://www.bilibili.com/video/BV1FR4y137hH/)
9. Gfarm 2.7.14 released, http://oss-tsukuba.org/en/news/gfarm-2-7-14
10. Osamu Tatebe, et al. Gfarm/BB — Gfarm File System for Node-Local Burst Buffer, JCST'20. [[PDF](https://link.springer.com/content/pdf/10.1007/s11390-020-9803-z.pdf)]
11. Osamu Tatebe, et al. CHFS: Parallel Consistent Hashing File System for Node-local Persistent Memory, HPC Asia2022. [[PDF](https://dl.acm.org/doi/pdf/10.1145/3492805.3492807)]
12. Andr´e Brinkmann, et al. Ad Hoc File Systems for High-Performance Computing, JCST'20. [[PDF](https://link.springer.com/content/pdf/10.1007/s11390-020-9801-1.pdf)]

