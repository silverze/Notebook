## 修改widget页面内容

### 一、更换主页图标：

* 更换主页Widget图标：在对应的CWidgetxxx类中的Initialize\(\)函数中修改

* 更换的图标资源ID对应工程目录\Release\Customer Files\LightningUI 1.4.10 - DEFAULT\下的.tif文件标号。



例如：修改主页AUX的图标为:

![](/assets/修改widget页面内容01.png)

该icon可以在Release\Customer Files\CUSTOMER\_DEFAULT\LightningUI 1.4.10 - DEFAULT\100\_Home目录下找到在CWidgetCore类的定义文件PanelWidget.cpp的初始化函数Initialize\(\)中，修改代码如下：

```
	m_tagAUX.Initialize(0,this,m_pSkinManager->GetTexture(TEXID_153),
NULL,NULL,NULL,
NULL,NULL,NULL,NULL);
	m_tagAUX.SetPos(0,10);
	m_tagAUX.SetShowScale(0.7f);
```





