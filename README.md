#WebSocket-Engine
###基于[Workerman-chat](https://github.com/walkor/workerman-chat)开源项目二次开发的WebSocket引擎  
  

---

* 在原有聊天室功能基础上集成了后台API调用机制
主要更改了[event](https://github.com/SUTFutureCoder/WebSocket-Engine/blob/master/applications/Chat/Event.php)文件

---

* 使用方法：`sudo  workerman/bin/workermand start/stop/restart/reload/kill/status`

---

#具体流程
1.页面加载前生成user_key并添加到js代码段中  

2.在数组中填写本页地址以便收到后台发送来的数据    
最简单的例子  `Rewind['src'] = location.href.slice((location.href.lastIndexOf("/")));  `  

3.如果是群发就加上  `Rewind['group'] = 'WSPDM2'; `  

4.填写后台api地址
如果是CI框架可以这样写`Rewind['api'] = location.href.slice(0, location.href.lastIndexOf("/")) + '/TableInfo/B_RewindSnapShot';`   

5.填写传输的数据
`Rewind['data'] = '{"user_key" : "<?= $user_key ?>", "user_name" : "<?= $user_name ?>", "snap_type" : "' + data[4]['type'] + '", "database" : "' + data[4]['database'] + '"}';`
> 为了安全起见，请务必设置用户密钥  

6.发送数据  
普通发送到API使用`parent.IframeSend(data);`  
如果需要群发则`parent.IframeSend(Rewind, 'group'); `

母页函数  

			function IframeSend(data, type) { 
				type = arguments[1] ? arguments[1] : "iframe";
				if (!data['group']){
						ws.send('{"type":"' + type + '","api":"' + data['api'] + '","src":"' + data['src'] + '","data":' + data['data'] + '}');
   			 	} else {        
       				 	ws.send('{"type":"' + type + '","api":"' + data['api'] + '","src":"' + data['src'] + '","data":' + data['data'] + ',"group":"' + data['group'] + '"}');
  			 	}
			}

7.由[event](https://github.com/SUTFutureCoder/WebSocket-Engine/blob/master/applications/Chat/Event.php)文件接收，通过cURL模拟POST到对应API接口，同时接收接口返回的数据。

iframe内外式：[例1](https://github.com/SUTFutureCoder/intelligence_server/tree/master/WSPDM2/application/views)

普通发送式：[例二](https://github.com/SUTFutureCoder/intelligence_server/tree/master/VirtualShell)
