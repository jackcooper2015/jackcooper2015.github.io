# 利用hexo github搭建的个人博客

主要有两个分支master、hexo，master分支主要放博客文章，hexo分支主要放hexo工程文件。master分支类容需要执行一下命令推送

 * hexo clean
 * hexo g
 * hexo d
 
 
# 命令解释
	* hexo init [folder] # 初始化一个网站。如果没有设置 folder ，Hexo 默认在目前的文件夹建立网站
	* hexo new [layout] <title> # 新建一篇文章。如果没有设置 layout 的话，默认使用 _config.yml 中的 default_layout 参数代替。如果标题包含空格的话，请使用引号括起来
	* hexo version # 查看版本
	* hexo clean # 清除缓存文件 (db.json) 和已生成的静态文件 (public)
	* hexo g # 等于hexo generate # 生成静态文件
	* hexo s # 等于hexo server # 本地预览
	* hexo d # 等于hexo deploy # 部署，可与hexo g合并为 hexo d -g
	
# 搭建参考文章
 https://www.jianshu.com/p/674c58cef707