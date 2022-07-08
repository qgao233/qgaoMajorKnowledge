# springCache

@Cacheable({"gao"})，保存在gao的缓存位置，key为方法参数，value为返回值

@CacheEvict(value="andCache",allEntries=true),清除保存在andCache缓存位置的所有缓存

@CachePut也可以声明一个方法支持缓存功能。与@Cacheable不同的是使用@CachePut标注的方法在执行前不会去检查缓存中是否存在之前执行过的结果，而是每次都会执行该方法，并将执行结果以键值对的形式存入指定的缓存中。
