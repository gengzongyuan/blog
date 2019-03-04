title: LoadRunner应用之批量获取邮编
date: 2018-09-20 17:42:10
tags: [loadrunner,性能测]
categories: [测试]
---


### 前言

> 工作中遇到要统计一堆人邮编信息的情况，网络目前提供的批量邮编查询功能又太弱，一个一个的把他们的地址粘到百度上查询再录入太过麻烦，所以写一个小脚本完成这个单调的工作。

#### 1.接口选择
- 本文选择百度的邮编查询页面进行查询
- 分析发现邮箱查询的请求是通过Get方式实现的，链接为
http://opendata.baidu.com/post/s?wd=%BD%AD%CB%D5%CA%A1%D1%CE%B3%C7%CA%D0&rn=20
- 而我们的关键字就是wd这个参数的值了,后续只要对其参数化便可实现不同值的查询

<!--more-->

#### 2.脚本编写
- 下一步就可以开始编写我们的脚本，通过web_url构造一个简单的get请求，并对wd的值进行参数化
```
	web_url("s", 
		"URL=http://opendata.baidu.com/post/s?wd={chengshi}&rn=20", 
		"TargetFrame=", 
		"Resource=0", 
		"RecContentType=text/html", 
		"Referer=", 
		"Snapshot=t1.inf", 
		"Mode=HTML", 
		EXTRARES,LAST);、
```

---------
- 参数化

#### 3.关联并获取邮编
- 通过关联去获取查找的邮编,关联左右边界如下,关联技巧不做详细介绍
```
	web_reg_save_param("youbian",
		"LB=</em> ",
		"RB=</a></li>",
		"NotFound=ERROR",
		"Search=Body",
		LAST);
```

#### 4.简单的打印查看结果
- 到此为止，加入简单的输出函数，我们就可以实验有没有成功了
```
	lr_error_message("city is:%s  youbian is:%s",lr_eval_string("{chengshi}"),lr_eval_string("{youbian}"));
```
- 运行结果
```
Action.c(20): Error: city is:江苏省睢宁县  youbian is:221200
```

#### 5.打印到本地txt文本中
- 为了方便录入，我们将找到的邮编统一放置到txt文本中,函数如下

```
在输出语句后加入代码
	writeStringToFile(lr_eval_string("{youbian}\n"));

在Action下定义函数
int writeStringToFile(char *string){
    char *filename = "F:\\脚本文件\\wenjianshuchu\\fileOutTest.txt";//参数文件保存路径，根据需要修改
    long file;//指向文件的指针
//	extern char * strtok(char * string, const char * delimiters ); // 扩展声明
	//用于打开文件
	 if ((file = fopen(filename, "a+" )) == NULL){
//      if ((file = fopen(filename, "w+" )) == NULL)   {
		 lr_output_message("###################################");
		 lr_output_message("Unable to create %s", filename);
		 return -1;
	 }
	 //fwrite(string,strlen(string),1,file );
     fprintf(file, string);
	 fclose(file);//关闭文件
	 return 1;
}
```

#### 全部代码
```
Action()
{
	web_reg_save_param("youbian",
		"LB=</em> ",
		"RB=</a></li>",
		"NotFound=ERROR",
		"Search=Body",
		LAST);

	web_url("s", 
		"URL=http://opendata.baidu.com/post/s?wd={chengshi}&rn=20", 
		"TargetFrame=", 
		"Resource=0", 
		"RecContentType=text/html", 
		"Referer=", 
		"Snapshot=t1.inf", 
		"Mode=HTML", 
		EXTRARES,LAST);

	lr_error_message("city is:%s  youbian is:%s",lr_eval_string("{chengshi}"),lr_eval_string("{youbian}"));

	writeStringToFile(lr_eval_string("{youbian}\n"));

	return 0;
}


int writeStringToFile(char *string){
    char *filename = "F:\\脚本文件\\wenjianshuchu\\fileOutTest.txt";//参数文件保存路径，根据需要修改
    long file;//指向文件的指针
//	extern char * strtok(char * string, const char * delimiters ); // 扩展声明
	//用于打开文件
	 if ((file = fopen(filename, "a+" )) == NULL){
//      if ((file = fopen(filename, "w+" )) == NULL)   {
		 lr_output_message("###################################");
		 lr_output_message("Unable to create %s", filename);
		 return -1;
	 }
	 //fwrite(string,strlen(string),1,file );
     fprintf(file, string);
	 fclose(file);//关闭文件
	 return 1;
}

```




















