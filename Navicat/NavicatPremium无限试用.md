# 申明:

本项目只做个人学习研究之用，不得用于商业用途！
若资金允许，请点击[链接](https://www.navicat.com/en/)购买正版，谢谢合作！



NavicatPremium 14天无限试用, 上代码

```
:: 申明:
:: 本项目只做个人学习研究之用，不得用于商业用途！
:: 若资金允许，请点击[链接](https://www.navicat.com/en/)购买正版，谢谢合作

:: 除了注册机可以使用以下方式来达到无限试用, 只需要找到两个注册目录就可以了, 非常方便简单

:: 重置NavicatPremium注册列表, 14天无限试用
:: NavicatPremium14天试用到期后, 在不运行Navicat的情况下运行此脚本

:: NavicatPremium在每台电脑注册列表目录不同请自行查找要删除的目录,查找到后将下面脚本替换成你的目录:
:: 按win+R然后输入: regedit, 打开注册表, 找到 HKEY_CURRENT_USER\SOFTWARE 目录;
:: 1.Classes\CLSID\目录下查找仅包含Info目录的目录;说明:可在装NavicatPremium前先查看HKEY_CURRENT_USER\SOFTWARE\Classes\CLSID\目录下面所有所有的目录, 安装完NavicatPremium后并运行,查看新增的那个目录就是NavicatPremium注册列表目录(目录下只有一个info目录的目录);  如果是已安装NavicatPremium, 找不到注册列表目录, 最简单的做法卸载重装(有sql连接记得导出备份哦,下次可以直接导入连接),NavicatPremium卸载完成后删除Classes\CLSID\目录下仅包含Info目录的所有注册列表目录(不会很多,经验告诉我删除不会有啥问题,不放心可以删除前先备份, 如果只有一个, 就不用卸载了哦, 这个就是NavicatPremium注册列表目录)
:: 2. PremiumSoft\NavicatPremium\目录下查找Registration开头的目录;

@echo ================================
@echo == 清除NavicatPremium注册列表 ==
@echo ================================
@echo.
@pause

:: 最关键的两步

:: 1. 删除 CLSID目录下的注册列表({6EFA4EF0-2301-58F4-41BF-7B8945C8E752}就是要替换的删除目录)
reg delete "HKEY_CURRENT_USER\SOFTWARE\Classes\CLSID\{6EFA4EF0-2301-58F4-41BF-7B8945C8E752}\Info" /va /f
@echo.
:: 2. 删除 PremiumSoft\NavicatPremium 目录下的注册列表(Registration15XEN 就是要替换的删除目录)
reg delete "HKEY_CURRENT_USER\SOFTWARE\PremiumSoft\NavicatPremium\Registration15XEN" /va /f

:: 清除结束

@echo.
@echo ================================
@echo NavicatPremium清除完毕, 又可以免费试用14天了哦!!!
@echo.
@pause
```

