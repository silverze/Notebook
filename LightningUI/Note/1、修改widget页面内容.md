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

### 二、修改AUX页面为如下图：

![](/assets/修改widget页面内容02.png)

  修改AUX页面为如上图所示，主要修改PanelAUX.cpp与PanelAUX.h两个文件。其中在PanelAUX.h文件中主要是添加或删减一些CButtonGL、CLabelGL的成员变量；而在PanelAUX.cpp文件中主要通过修改 BOOL CPanelAUX::Initialize\(UINT id,CPanelGL \*pParent,CSkinManagerGL \*pSkinManager,CTextGL \*pTextGL,HWND hWnd,MODULEENTRY entry\) 、void CPanelAUX::Render\(\) 两个函数中的内容。



例如：

1、添加Disk这个图标：

①在PanelAUX.h文件中，声明一个成员变量：CLabelGL	m\_iconDisk;

②在PanelAUX.cpp文件中的Initialize\(\)函数中，添加代码：

```
	m_iconDisk.Initialize(0,NULL,
m_pSkinManager->GetTexture(TEXID_202));
	m_iconDisk.SetPos(-g_iClientWidth/2 + 160, -10);
	m_iconDisk.SetShowScale(0.6f);
```

③在PanelAUX.cpp文件中的Render\(\)函数中，添加代码：

```
	m_iconDisk.SetAlpha(m_fCurAlpha);
	m_iconDisk.Render();
```

2、修改显示字符串：

①在PanelAUX.h文件中，新添加声明一个成员变量：CButtonGL	m\_tagTxt;

②在PanelAUX.cpp文件中的Initialize\(\)函数中，修改\添加代码：

```
SIZE si1={800,64};
	COLORGL cr = {0.0,0.733,0.914,0.0};
	m_tagTitle.Initialize(0,this,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
	m_tagTitle.SetSize(&si1);
	m_tagTitle.SetCaptionColor(&cr);
	m_tagTitle.SetCaption(GetResourceString(IDS_AUX_PLAYING)); 
	m_tagTitle.LinkTextGL(pTextGL);
	m_tagTitle.SetPos(80, 15);
	m_tagTitle.SetCaptionHeight(32);
	m_tagTitle.EnableDrawBack(FALSE);
	m_tagTitle.SetCaptionPos(BTN_CAPTION_CENTER);

 SIZE si2 = {400,32};
	COLORGL cr2 = {1,1,1,0.5};
	m_tagTxt.Initialize(0,this,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
	m_tagTxt.SetSize(&si2);
	m_tagTxt.SetCaptionColor(&cr2);
	m_tagTxt.SetCaption(GetResourceString(IDS_AUX_TIP2));
	m_tagTxt.LinkTextGL(pTextGL);
	m_tagTxt.SetPos(80, -25);
	m_tagTxt.SetCaptionHeight(16);
	m_tagTxt.EnableDrawBack(FALSE);
	m_tagTxt.SetCaptionPos(BTN_CAPTION_CENTER);
```

③在PanelAUX.cpp文件中的Render\(\)函数中，修改\添加代码：

```
	m_tagTitle.SetAlpha(m_fCurAlpha);
	m_tagTitle.Render();
	m_tagTxt.SetAlpha(m_fCurAlpha);
	m_tagTxt.Render();
```

④修改资源文件中的字符串常量:

在Resource View中打开String Table\[English\(U.S.\)\]，并将上述使用到的两个资源ID的Caption修改为对应的字符串。![](/assets/修改widget页面内容03.png)

### 三、在AUX页面顶部增加音源按键、呼出按键:

![](/assets/修改widget页面内容04.png)

AUX顶部增加音源、呼出按键，主要修改的文件有：PanelAUX.cpp、PanelAUX.h、LightningUI.rc\(String Tabel\[English\(U.S.\)\]\)、Control\_res.h。根据要求，按钮是需要添加行为动作的；添加的一个按钮以及其动作处理的过程：

1、在类头文件中，添加一个按钮的声明：CButtonGL m\_btnXXX;

2、在类的定义文件Inilialize\(\)函数中，对其进行初始化设置；

3、在Render\(\)函数中进行，刷新显示；

4、如果该按钮需要处理消息事件，则需要在类的定义文件HandleControlMSG\(\)函数中对其进行消息处理。

例如：

在AUX顶部添加Bluetooth音源输入按钮，以及呼出按钮

①、在PanelAUX.h中添加按钮的声明：

```
CButtonGL m_btnBluetooth;
CButtonGL m_btnArrow;
```

②、在PanelAUX.cpp的Inilialize\(\)函数中，进行初始化设置：

```
m_btnBluetooth.Initialize(IDC_AUX_BTN_BLUETOOTH,this,
                m_pSkinManager->GetTexture(TEXID_121),NULL,
		m_pSkinManager->GetTexture(TEXID_121),NULL,
		NULL,NULL,
		NULL,NULL);//注意 IDC_AUX_BTN_BLUETOOTH 宏
 //将按钮的触发消息绑定在OnNotify()函数
	m_btnBluetooth.SetNotifyFunc(&CPanelAUX::OnNotify,this);
	m_btnBluetooth.SetPos(-g_iScreenWidth/2 + 40 + INTERVAL,   
                              g_iClientHeight/2 - 40);
	m_btnBluetooth.SetTouchRate(1.0f ,1.0f);
	m_btnBluetooth.SetShowScale(0.4f);
	m_btnBluetooth.EnableClickAni(TRUE);
	m_btnBluetooth.EnableTouchGlow(TRUE,
                           m_pSkinManager->GetTexture(TEXID_020));
                           
//通过四个Label来做呼出按钮的状态切换!
	m_iconArrowLeftUp.Initialize(0,NULL,m_pSkinManager->GetTexture(TEXID_082));
	m_iconArrowLeftDown.Initialize(0,NULL,m_pSkinManager->GetTexture(TEXID_082));	
	m_iconArrowRightUp.Initialize(0,NULL,m_pSkinManager->GetTexture(TEXID_083));
	m_iconArrowRightDown.Initialize(0,NULL,m_pSkinManager->GetTexture(TEXID_083)); 

	m_btnArrow.Initialize(IDC_AUX_BTN_ARROW,this,NULL,NULL,
		NULL,NULL,
		NULL,NULL,
		NULL,NULL);
	m_btnArrow.SetIcon(&m_iconArrowLeftUp,&m_iconArrowLeftUp);
	m_btnArrow.EnableDrawBack(FALSE);
	m_btnArrow.SetNotifyFunc(&CPanelAUX::OnNotify,this);
	m_btnArrow.SetPos(-g_iScreenWidth/2 + 40 + 4*INTERVAL, g_iClientHeight/2 - 40);
	m_btnArrow.SetTouchRate(1.0f ,1.0f);
	m_btnArrow.SetShowScale(1.0f);
	m_btnArrow.EnableClickAni(TRUE);
	m_btnArrow.EnableTouchGlow(TRUE,m_pSkinManager->GetTexture(TEXID_020));
```

③、在PanelAUX.cpp的Render\(\)函数中进行刷新显示：

```
m_btnBluetooth.Render();
m_btnArrow.Render();
```

④、处理按钮的消息事件，在初始化函数中，已经将m\_btnBluetoothy与OnNotify\(\)函数进行了绑定；但是OnNotify\(\)函数最终是通过调用该面板的HandleControlMSG\(\)函数来具体的进行消息处理。

```
void CPanelAUX::HandleControlMSG(DWORD idControl,DWORD wMsg,DWORD wPara,LPVOID lpPara)
{
	switch (idControl)
	{
    		 ……
	case IDC_AUX_BTN_BLUETOOTH:
		m_strId = IDS_AUX_PLAYING_BLUETOOTH; //改变现实字符串
		PlaySoundEffect(0);
		break;
		……
	case IDC_AUX_BTN_ARROW:
		ChangeTopButtonList();
		PlaySoundEffect(0);
		break;
		……
	}
}
```

其中 IDC\_AUX\_BTN\_BLUETOOTH、IDC\_AUX\_BTN\_ARROW 宏在 Controls\_res.h 头文件中定义。IDS\_AUX\_PLAYING\_BLUETOOTH是字符串资源，可在LightningUI.rc\(String Tabel\[English\(U.S.\)\]\)中进行编辑；编辑完成后Visual Studio会自动在Resource.h文件中生成对应的宏。

ChangeTopButtonList\(\)函数是定义在PanelAUX.cpp文件中的m\_btnArrow按钮的事件处理执行函数：

```
void CPanelAUX::ChangeTopButtonList()
{
	if(m_isExtend)
	{
		m_isExtend = FALSE;
				   m_btnArrow.SetIcon(&m_iconArrowRightUp,&m_iconArrowRightDown);

		switch(m_strId)
		{
			case IDS_AUX_PLAYING :
				m_btnAUX.Show(TRUE);
				m_btnBluetooth.Show(FALSE);
				m_btnDVD.Show(FALSE);
				m_btnUSB.Show(FALSE);
				m_btnAUX.SetPos(-g_iClientWidth/2 + 40, g_iClientHeight/2 - 40);
				break;
			
			case IDS_AUX_PLAYING_BLUETOOTH:
				m_btnAUX.Show(FALSE);
				m_btnBluetooth.Show(TRUE);
				m_btnDVD.Show(FALSE);
				m_btnUSB.Show(FALSE);
				m_btnBluetooth.SetPos(-g_iClientWidth/2 + 40, g_iClientHeight/2 - 40);
				break;

			case IDS_AUX_PLAYING_DVD:
				m_btnAUX.Show(FALSE);
				m_btnBluetooth.Show(FALSE);
				m_btnDVD.Show(TRUE);
				m_btnUSB.Show(FALSE);
				m_btnDVD.SetPos(-g_iClientWidth/2 + 40, g_iClientHeight/2 - 40);
				break;

			case IDS_AUX_PLAYING_USB:
				m_btnAUX.Show(FALSE);
				m_btnBluetooth.Show(FALSE);
				m_btnDVD.Show(FALSE);
				m_btnUSB.Show(TRUE);
				m_btnUSB.SetPos(-g_iClientWidth/2 + 40, g_iClientHeight/2 - 40);
				break;

		}

		m_btnArrow.SetPos(-g_iClientWidth/2 + 40 + INTERVAL, g_iClientHeight/2 - 40);
	}
	else
	{
	    m_isExtend = TRUE;
		m_btnArrow.SetIcon(&m_iconArrowLeftUp,&m_iconArrowLeftDown);

		m_btnAUX.Show(TRUE);
		m_btnAUX.SetPos(-g_iClientWidth/2 + 40, g_iClientHeight/2 - 40);
		m_btnBluetooth.Show(TRUE);
		m_btnBluetooth.SetPos(-g_iClientWidth/2 + 40 + INTERVAL, g_iClientHeight/2 - 40);
		m_btnDVD.Show(TRUE);
		m_btnDVD.SetPos(-g_iClientWidth/2 + 40 + 2*INTERVAL, g_iClientHeight/2 - 40);
		m_btnUSB.Show(TRUE);
		m_btnUSB.SetPos(-g_iClientWidth/2 + 40 + 3*INTERVAL, g_iClientHeight/2 - 40);

		m_btnArrow.SetPos(-g_iClientWidth/2 + 40 + 4*INTERVAL, g_iClientHeight/2 - 40);
	}
}
```

注意：

1、尽可能的只在Initialize\(\)函数中对控件进行Initialize操作；之前在ChangeTopButton\(\)函数中反复调用m\_btnArrow的Initialize\(\)函数，这种方式不好。

2、通过四个CLabelGL,结合CButtonGL类的SetIcon\(\)函数来实现按钮的状态切换。

3、CButtonGL类的EnableDrawBack\(\)函数可控制是否绘制按钮背景。

4、在Render\(\)函数中，可通过

	COLORGL cr\_b = {0.380,0.361,0.361,m\_fCurAlpha}; 

DrawRect\(0, 0, g\_iClientWidth, g\_iClientHeight, &cr\_b\);

绘制一个背景色。

5、注意DrawRect\(\)函数的\(0,0\)坐标在屏幕的左上角，而控件的SetPos\(\)函数的\(0,0\)坐标在屏幕正中心。



