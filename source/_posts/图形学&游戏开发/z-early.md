z-early

原来是在片段着色器之后进行深度测试，但是片段着色器进行光照的计算会有比较大的开销，如果将开销耗费在一个在未来的深度测试中将被抛弃的片元，很不合理

所以z-early就是为了解决这个问题，将深度测试提前到片段着色器之前进行

z-early的不足之处

避免过度绘制的效果不稳定，如果绘制顺序是由远及近的话，z-early也无法避免过度绘制

