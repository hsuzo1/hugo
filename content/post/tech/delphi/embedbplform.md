---
title: "Delphi实现BPL窗体嵌入主程序内"
date: 2021-03-17T19:59:31+08:00
draft: false
categories: ["tech"]
Tags : ["delphi", "bpl", "download"]
---

Delphi中的BPL文件类似于windows中DLL文件，可以封装完整的VCL和其他函数。如在开发使用中采用“主程序”+“bpl”模式，不仅可以大大减少主程序的体积，还能将各功能模块
分布开发，提高同步开发的速度。另外，如后期需要更改个别功能时仅要修改对应的bpl文件即可，而不必重新编译发布整个程序文件。

### 测试用的VCL程序主要属性
- 测试使用的是Delphi 10.2和windows10系统
- 添加一个Mainmenu和Panel，Panel设置为align=alClient，Mainmenu添加一个菜单“Open BPL Form”用于激活子窗口。
- 新建一个Package包，命名为“bplForm.bpl”；
- bplForm.bpl包中添加一个Form，主要属性如下：align=alClient，BorderSytle=bsNone，WIndowState=wsMaximized。界面上放置一个**Button**，用于实现关闭功能，其他组件随便添加。

{{< image src="/img/tech/Snipaste_2021-03-17_19-app.gif"  position="center" style="width:80%">}}

### 各主要功能代码
**bplForm.bpl**中的Form2如下：
```pascal
procedure TForm2.Button1Click(Sender: TObject);
begin
  close;
end;

procedure TForm2.FormClose(Sender: TObject; var Action: TCloseAction);
begin
  Action := caFree;
end;

initialization
  RegisterClass(TForm2);

finalization
  UnRegisterClass(TForm2);
  FreeAndNil(Form2);
```

主程序中代码如下：
1.private中定义动态装载包的过程：
```pascal
procedure LoadMDIForm(BplFile, TFrmName : String);
```
2.其他如下
```pascal
var
  Form1: TForm1;

implementation

{$R *.dfm}

//声明一些全局变量
var
  BPLHandle : Cardinal;//HModule;
  AForm : TForm;
  LoadedBplFileName : String;


{ TForm1 }
procedure TForm1.BplForm1Click(Sender: TObject);
begin
    // 动态装载包
  LoadMDIForm('bplForm.bpl' , 'TForm2');
end;

procedure TForm1.FormClose(Sender: TObject; var Action: TCloseAction);
begin
    // 关闭主程序时，清理包
  if BPLHandle <> 0 then
  begin
    UnRegisterModuleClasses(BPLHandle);
    UnloadPackage(BPLHandle);     //卸载包
  end;
end;

procedure TForm1.FormCreate(Sender: TObject);
begin
  LoadedBplFileName := '';
end;

procedure TForm1.LoadMDIForm(BplFile, TFrmName: String);
var
  AClass : TClass;
begin
  if Not FileExists(ExtractFilePath(paramstr(0)) + BplFile) then
  begin
    Application.MessageBox(PChar('找不到文件 ' + BplFile) , 'MSG', MB_OK);
    Exit;
  end;

  Try
    if (AForm <> Nil) And (AForm.Showing) then FreeAndNil(AForm);
    if LoadedBplFileName <> BplFile then
    begin
      if BPLHandle <> 0 then
      begin
        UnloadPackage(BPLHandle);
        BPLHandle := 0;
      end;
      BPLHandle := LoadPackage(BplFile);
    end;
    AClass := GetClass(TFrmName);
    if AClass <> nil then
    begin
      Application.CreateForm(TComponentClass(AClass) , AForm);
      // 将bpl中的Form嵌入到主程序中的Panel中，实现与主程序同步缩放
      AForm.parent := Form1.Panel1;
      AForm.bringToFront;
      AForm.Show;
    end;
  Except
    on E: Exception do
    begin
      Application.MessageBox(PChar('窗口嵌入失败！错误信息如下：'+#13+ E.Message ) , '公文管理', MB_OK);
      Exit;
    end;
  End;
  LoadedBplFileName := BplFile;
end;
```

### 最关键的一处设置
要实现动态加载package包还需要在IDE进行一点设置，否则**GetClass(TFrmName)**将始终返回**nil**，从而无法达到预期。
- 勾选“link with runtime package”为 **True**
- 在下方的“Runtime packages”中手动添加要加载的包名称，同时删除下方默认的系统包。最终如下图：
{{< image src="/img/tech/Snipaste_2021-03-17_19-setting.jpg"  position="center">}}

>其他参考文章[https://www.cnblogs.com/dingjie/articles/195195.html](https://www.cnblogs.com/dingjie/articles/195195.html)

**点击按钮下载本文源代码：**
{{< link "/files/BPLFormSimpleDemo.zip" >}}