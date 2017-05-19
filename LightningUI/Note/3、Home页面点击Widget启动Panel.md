# Home页面点击Widget启动Panel

---

### 一、启动流程分析

### 

---

### 二、实例说明

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



