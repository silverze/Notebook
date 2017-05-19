# Home页面点击Widget启动Panel

---

### 一、启动流程分析

![](/assets/Home页面点击Widget启动Panel.png)

### 二、点击Widget启动实现

* 1、按照在《Home中添加一个新的Widget》中步骤①添加两个文件至项目中。

* 2、在GUI\_Fantasy.cpp的OpenPanels\(\)函数中添加代码：

```
#if CVTE_EN_ISDB
case UI_CLASS_ISDB:
{
    CPanelISDB *pPanel = new CPanelISDB;

    pPanel->Initialize(MAKE_PANEL_ID(UI_CLASS_ISDB,PAGE_ISDB_WRAPPER), 
    NULL, m_pSkinManager, m_pTextGL, m_hMainWnd, m_entry);
    pPanel->LinkCommander(m_pCommander);
    pPanel->LinkStatusBar(m_pStatusBar);
    m_listPanels.Add(pPanel,MAKE_PANEL_ID(UI_CLASS_ISDB,PAGE_ISDB_WRAPPER));
}
break;
#endif
```

* 3、在PanelWidget.cpp的`void CPanelWidget::HandleControlMSG(DWORD idControl,DWORD wMsg,DWORD wPara,LPVOID lpPara)`函数中添加代码：

```
case IDC_WIDGET_ISDB:
    SwitchPage(UI_CLASS_ISDB, PAGE_ISDB_WRAPPER,PAGE_SWITCH_ANI_ZOOMOUT,0,
    (LPVOID)m_listWidgets.GetItemByID(idControl),TRUE);
    break;
```

### 三、 Panel内容实现

* 界面显示主要编辑 `Initialize()`函数：

```
BOOL CPanelISDB::Initialize(UINT id, CPanelGL *pParent, CSkinManagerGL *pSkinManager,
 CTextGL *pTextGL, HWND hWnd, MODULEENTRY entry)
{
	CPanelGL::Initialize(id,pParent,pSkinManager,pTextGL,hWnd,entry);

	const int cx=LIST_BOX_ITEM_WIDTH;
	const int cy=LIST_BOX_ITEM_HEIGHT;

	m_bShowStatusBar = TRUE;
	m_bEnableSmartBar = TRUE;

	m_labelSignal.Initialize(0,NULL,m_pSkinManager->GetTexture(TEXID_029));
	m_labelSignal.SetScale(1.0f, 1.0f);
	m_labelSignal.SetPos(-g_iClientWidth/2 + 40, g_iClientHeight/2 -40);

	m_btnHome.Initialize(IDC_ISDB_BTN_HOME,this,m_pSkinManager->GetTexture(TEXID_015),
		NULL,m_pSkinManager->GetTexture(TEXID_016),NULL,
		NULL,NULL,
		NULL,NULL);
	m_btnHome.SetNotifyFunc(&CPanelISDB::OnNotify, this);
	m_btnHome.SetPos(-g_iClientWidth/2+80, -g_iClientHeight/2+40);
	m_btnHome.SetTouchRate(1.6f, 1.6f);
	m_btnHome.SetShowScale(0.8f);
	m_btnHome.EnableClickAni(TRUE);
	m_btnHome.EnableTouchGlow(TRUE, m_pSkinManager->GetTexture(TEXID_020));
	
	GLfloat texCoord_s01000[]={
		4.0/512.0, 172/512.0,
		4.0/512.0, 340.0/512.0,
		328.0/512.0, 172.0/512.0,
		328.0/512.0, 340.0/512.0};

	m_mainIcon.Initialize(0, NULL, m_pSkinManager->GetTexture(TEXID_150),texCoord_s01000);
	m_mainIcon.SetScale(1.0f, 1.0f);
	m_mainIcon.SetPos(-cx + 80, 20);


	SIZE si = {400,32};
	COLORGL cr = {1,1,1,0.5};
	m_tagTxt.Initialize(0,this,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
	m_tagTxt.SetSize(&si);
	m_tagTxt.SetCaptionColor(&cr);
	m_tagTxt.SetCaption(TEXT("Current Station:"));
	m_tagTxt.LinkTextGL(pTextGL);
	m_tagTxt.SetPos(-cx/2, -cy);
	m_tagTxt.SetCaptionHeight(16);
	m_tagTxt.EnableDrawBack(FALSE);
	m_tagTxt.SetCaptionPos(BTN_CAPTION_CENTER);


	//ListBox 相关初始化
	LISTBOXSTRUCTURE box;

	memset(&box,0,sizeof(box));
	box.siClient.cx=cx;
	box.siClient.cy=g_iClientHeight- 2*cy;
	box.siBorder.cx=cx;
	box.siBorder.cy=g_iClientHeight- 2*cy;
	box.iSpacing=(g_iClientHeight- 2*cy)/8;

	box.idTexIndexBase=TEXID_603;
	box.idTexIndexFocus=TEXID_068;
	box.idTexBase=TEXID_066;
	box.idTexSliThumb=TEXID_063;
	box.pxSlider=-15;
	box.bNoSlider=FALSE;
	box.bNoBorder=TRUE;
	box.bGlow=FALSE;
	box.bNoHorSeperator=TRUE;
	box.idAlignType=LIST_ALIGN_LEFT;
	box.bQuickSearchBoard=FALSE;
	box.fIndexBaseOffset=60; 

	COLORGL cr_normal = {0.7,0.7,0.7,1.0};
	COLORGL cr_focus = {1.0,1.0,1.0,1.0};
	m_listBoxStation.Initialize(IDC_ISDB_LISTBOX_STATION, this, &box,pSkinManager);
	m_listBoxStation.SetNotifyFunc(&CPanelISDB::OnNotify, this);
	m_listBoxStation.SetPos(cx + 50, 0);
	m_listBoxStation.LinkTextGL(pTextGL);
	m_listBoxStation.SetAlpha(1.0f);
	m_listBoxStation.SetCaptioniHeight(20);
	m_listBoxStation.SetCaptionColor(&cr_normal,&cr_focus);
	m_listBoxStation.SetSortType(ITEM_SORT_BY_ID);

	CListBoxItemGL *pItem;
	pItem = new CListBoxItemGL(TEXT("Station Lists"));
	m_listBoxStation.AddItem(pItem, 1);
	pItem = new CListBoxItemGL(TEXT(""));
	m_listBoxStation.AddItem(pItem, 2);
	pItem = new CListBoxItemGL(TEXT("001.GNTV_SD"));
	m_listBoxStation.AddItem(pItem, 3);
	pItem = new CListBoxItemGL(TEXT("002.GMA 1Seg"));
	m_listBoxStation.AddItem(pItem, 4);
	pItem = new CListBoxItemGL(TEXT("003.BEAM_SD1"));
	m_listBoxStation.AddItem(pItem, 5);
	pItem = new CListBoxItemGL(TEXT("004.CCTV-5"));
	m_listBoxStation.AddItem(pItem, 6);
	pItem = new CListBoxItemGL(TEXT("005.UNTV-1"));
	m_listBoxStation.AddItem(pItem, 7);
	pItem = new CListBoxItemGL(TEXT("006.SPORTS"));
	m_listBoxStation.AddItem(pItem, 8);
	pItem = new CListBoxItemGL(TEXT("007.ABS-CBN"));
	m_listBoxStation.AddItem(pItem, 9);
	pItem = new CListBoxItemGL(TEXT("008.CINEMOI"));
	m_listBoxStation.AddItem(pItem, 10);
	
	return TRUE;
}
```

* Panel经过Initialize后，需要在Render中刷新：

```
void CPanelISDB::Render()
{
	static CTexture *pTexBar=m_pSkinManager?m_pSkinManager->GetTexture(TEXID_055):NULL;

	if(!BeginRender())return;

	//bar:
	COLORGL cr = {1.0,1.0,1.0,1.0};
	DrawRect(pTexBar, 0, (-g_iClientHeight/2+40), g_iClientWidth-4, 720, 2, &cr, TRUE);

	m_labelSignal.SetAlpha(m_fCurAlpha);
	m_labelSignal.Render();

	m_btnHome.SetAlpha(m_fCurAlpha);
	m_btnHome.Render();

	m_listBoxStation.SetAlpha(m_fCurAlpha);
	m_listBoxStation.Render();
	
	m_mainIcon.SetAlpha(m_fCurAlpha);
	m_mainIcon.Render();

	m_tagTxt.SetAlpha(m_fCurAlpha);
	m_tagTxt.Render();

	//bar:
	DrawRect(pTexBar, 0, (-g_iClientHeight/2+40), g_iClientWidth-4, 80, 2, &cr, TRUE);

	if(m_pStatusBar && IsStatusBarVisible())
	{
		m_pStatusBar->SetAlpha(m_fCurAlpha);
		m_pStatusBar->Render();
	}

	EndRender();
}
```

* 最后对Panel中的控件进行动作处理：

```
void CPanelISDB::HandleControlMSG(DWORD idControl, DWORD wMsg, DWORD wPara, LPVOID lpPara)
{
	switch (idControl)
	{
	case IDC_ISDB_BTN_HOME:
		SwitchPage(UI_CLASS_HOME,PAGE_HOME_WRAPPER);
		PlaySoundEffect(0);
		break;
	case IDC_ISDB_LISTBOX_STATION:
		{
			int index=wPara;	
			SelectStation(index);//动作的具体实现函数
		}
		break;
	 }
}
```

### 四、效果图

### ![](/assets/Home页面点击Widget启动Panel效果图.png)



