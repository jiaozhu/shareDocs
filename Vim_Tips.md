# Vim Tips



## 常用操作 

###	文件切换 

`ls` Show all buffers.		
`:bn` next buffer in the buffer list  	
`:bp` previous buffer in the buffer list	 
`:b#` previous buffer you was in	

###	插入模式

`a` 在光标后插入  
`o` 在当前行后插入一个新行		
`O` 在当前行前插入一个新行		
`cw` 替换从光标所在位置后到一个单词结尾的字符		

###	移动光标

`0` 数字零，到行头	
`^` 到本行第一个不是blank字符的位置（所谓blank字符就是空格，tab，换行，回车等）    
`$` 到本行行尾     
`g_` 到本行最后一个不是blank字符的位置。    
`/pattern` 搜索 pattern 的字符串（如果搜索出多个匹配，可按n键到下一个）    
`nG` 到第 N 行 （注意命令中的G是大写的）         

### 内容排版

`=` 自动缩进        
`<` 或 `>` 左右缩进	    	


### 拷贝/粘贴

`P` 粘贴   
`yy` 拷贝当前行当行于 `ddP`   


## 插件相关

### Undotree

撤销指令的树状list。		

快捷键 ： `,u` 	

它有两个窗口。上面的窗口：当前标记的下一步会被标记{seq}。[seq]表示该程序的最后一次改变(不包括执行undotree撤销操作的改变) 。下面的窗口：当前所处步骤是把什么改变成了什么。   

### NERDTree


文件系统导航。		

快捷键 ：`<Leader>e`		

自定义动作		

	Use <C-E> to toggle NERDTree		
	Use <leader>e or <leader>nt to load NERDTreeFind which opens NERDTree where the current file is located     
	Treat NERDTree more like a panel than a split.     
	
### NERDCommenter

快速添加注释。

快捷键 ： `<Leader>c<space>`				

### Tagbar

标签导航。			

快捷键：`,tt`		

使用这个插件需要要安装ctags插件,spf13并没有该插件。	
	
	brew install ctags
	


	
	









	





