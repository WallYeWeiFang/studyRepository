在食间项目中国呢需要用到比较多的并行处理 如下总结

PHP的curl multi可以使用多线程处理http请求，一定程度上可以提高请求接口的效率。但是，启用多线程也是会消耗资源的事情，那么每次curl multi同时并发多少个请求合适呢？

curl multi并发请求并发数有一个阈值，过高的并发不能提升效率，反而会导致请求不成功，这个阈值与服务端的性能有关。

CURLOPT_TIMEOUT必须跟进实际业务设置合适的值


	<?php
	$max_request = $argv[1];
	$ch_list = array(); 
	$multi_ch = curl_multi_init();
	for ($i = 1;$i <= $max_request;++$i) {
	    $ch_list[$i] = curl_init("http://www.xxx.com/a.php");
	    curl_setopt($ch_list[$i], CURLOPT_RETURNTRANSFER, true);
	    curl_setopt($ch_list[$i], CURLOPT_TIMEOUT, 10);
	    curl_multi_add_handle($multi_ch, $ch_list[$i]); 
	} 
	$active = null; 
	do {
	    $mrc = curl_multi_exec($multi_ch, $active); //处理在栈中的每一个句柄。无论该句柄需要读取或写入数据都可调用此方法。 
	} while ($mrc == CURLM_CALL_MULTI_PERFORM);
	//Note:
	//该函数仅返回关于整个批处理栈相关的错误。即使返回 CURLM_OK 时单个传输仍可能有问题。 
	
	while ($active && $mrc == CURLM_OK) {
	    if (curl_multi_select($multi_ch) != -1) {//阻塞直到cURL批处理连接中有活动连接。 
	        do { 
	        $mrc = curl_multi_exec($multi_ch, $active); 
	        } while ($mrc == CURLM_CALL_MULTI_PERFORM); 
	    } 
	}
	//获取http返回的结果
	$true_request = 0;
	foreach ($ch_list as $k => $ch) {
	    $result = curl_multi_getcontent($ch); 
	    curl_multi_remove_handle($multi_ch,$ch); 
	    curl_close($ch);
	    if ($result == 1) {
	        $true_request += 1;
	    }
	}
	curl_multi_close($multi_ch);
	echo $true_request, PHP_EOL;
	
	
	参数名称	说明
	total_time	      |curl所花费的总时间
	namelookup_time   |	域名解析所花费的时间
	connect_time      |	连接目标服务器所花费的时间
	redirect_time     |	重定向所花费的时间
	starttransfer_time|	数据传输开始时间
	
	
CURLOPT_TIMEOUT 设置为15s 比较合适









