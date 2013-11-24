---
layout: post
title: nginx下php使用flush
date: 2013-11-24 21:08
comments: true
categories: PHP
---

对于PHP, 希望可以在页面上不间断地输出内容(类似tail -f filename), 而不是等待请求处理完一次性返回, 可以使用ob([output buffer](http://www.php.net/manual/en/book.outcontrol.php))相关的函数.

这需要禁用服务端的gzip压缩, 以及输出缓冲(nginx+fastcgi模式下), 相应设置如下:
	php.ini:
		output_buffering = Off
		zlib.output_compression = Off

	php-fpm.conf:
		request_terminate_timeout = 0

	nginx.conf:
		gzip off;
		proxy_buffering off;

PHP也可以在执行脚本中进行运行时设置:
	ini_set('zlib.output_compression', 0);
	ini_set('implicit_flush', 1);
	@ob_end_clean();
	set_time_limit(0);

然后在PHP代码中如下使用:
	ob_start('ob_gzhandler');
	ob_flush();
	ob_implicit_flush(1);

	$i = 100;
	while ($i--) {
		echo $i;
		sleep(1);
	}
