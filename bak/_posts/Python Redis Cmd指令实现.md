title: Python Redis Cmd指令实现
date: 2019-02-13T03:47:52.901Z
tags: [python]
categories: [开发]
---
> 开发自动化平台的时候，想实现录入一行cmd指令就去执行的效果，网上找了一堆材料都没有对应的帮助，跑去翻了redis的源码才找到解决方案


- 先来看redis的一条get方法是怎么实现的
```
    def get(self, name):
        """
        Return the value at key ``name``, or None if the key doesn't exist
        """
        return self.execute_command('GET', name)

可以看到调用了execute_command方法
```

<!--more-->

- 在client.py代码里找到了封装的execute_command方法
```
    # COMMAND EXECUTION AND PROTOCOL PARSING
    def execute_command(self, *args, **options):
        "Execute a command and return a parsed response"
        pool = self.connection_pool
        command_name = args[0]
        connection = pool.get_connection(command_name, **options)
        try:
            connection.send_command(*args)
            return self.parse_response(connection, command_name, **options)
        except (ConnectionError, TimeoutError) as e:
            connection.disconnect()
            if not connection.retry_on_timeout and isinstance(e, TimeoutError):
                raise
            connection.send_command(*args)
            return self.parse_response(connection, command_name, **options)
        finally:
            pool.release(connection)
```
- 我们比较一下三种代码
```
cmd指令：get key
redis对外提供的方法：redis.get("key")
真正执行的方法：execute_command('GET', "key")
```
- 解决方案：先上代码
```
    def runCmd(self,cmd):
        try:
            cmdlis = cmd.split(" ")
            return self.redis.execute_command(cmdlis[0].upper(), *cmdlis[1:len(cmdlis)])
        except:
            logger.error("Redis Cmd执行失败，Cmd:%s"%cmd)
            return False

对cmd指令进行分割，第一个字段作为指令，后续的字段通过*的方式进行传参
```