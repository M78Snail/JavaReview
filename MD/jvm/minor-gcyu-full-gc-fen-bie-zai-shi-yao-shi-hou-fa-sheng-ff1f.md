Minor GC也叫Young GC，当年轻代内存满的时候会触发，会对年轻代进行GC

Full GC也叫Major GC，当年老代满的时候会触发，当我们调用System.gc时也可能会触发，会对年轻代和年老代进行GC

