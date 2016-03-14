---
layout: post
title: 如何用C++与QT来写一个简单的程序
tags:  [C++,QT,qmake,mingw]
categories: [QT,C plus plus]
author: yddchsc
excerpt: "简述用C++与QT来写一个简单的程序"
---

##搭建QT开发环境

> 自己去网上可以搜到搭建的方法。也可以点击[这里](http://www.haosou.com/s?ie=utf-8&src=360chrome_toolbar_search&q=markdown%E8%AF%AD%E6%B3%95)。

##编写代码的思路

> 博文中通过注释来表述思路。

> 可以点击[这里](http://download.csdn.net/detail/duanxinhuishou/8809159)下载源代码。（已通过qmake+mingw编译生成可执行文件）。
	

```
//c++
//Window.h 
	#ifndef WINDOW_H
	#define WINDOW_H

	#include <QtGui/QWidget>
	#include <QApplication>  
	#include <QSplitter>  
	#include <QLineEdit>  
    #include <QToolBar>
    #include <QtGui>
    #include <QToolButton>
    #include <QTextCodec>
    #include <iostream>
    #include <string>
    #include <vector>
    #include <QMessageBox>
    #include <QString>
    #include <QVector>
    #include <QDialog>

    using namespace std; 

    class Window : public QWidget
    {  
        Q_OBJECT
        private:
        int num;
        int new_num;
        int sig_num;//定义有效数字位数；
        double x[201];
        double y[201];
        double x_new[201];
        double y_new[201];
        double ave_x;
        double ave_y;//定义数组x与y的平均数；
        double ox;
        double oy;//定义数组x与y的方差；
        double s_x;
        double s_y;//定义数组x与y的标准差；
        double ave_x_dev[201];
        double ave_y_dev[201];//定义方差计算的两个过渡数组；
        double sx;
        double sy;
        double sm;//定义计算回归系数的三个中间变量；
        double a;
        double b;//定义回归系数a和b；
        int jo;
        QString name_x;
        QString name_y;//定义string类型的变量x与y的  
        QString function; 
        QString xo; 
        QString yo;
        QLineEdit *upEdit;
        QLineEdit *bottomEdit;
        QTextEdit *midEdit;
    public:  
        Window(QWidget *parent = 0);
    protected:
        void paintEvent(QPaintEvent *);
    public slots:  //定义QT的槽函数
        void upEditChanged(const QString &);
        void bottomEditChanged(const QString &);
        void doPushBtn();
    };   
    
    #endif   // WINDOW_H

```

	
	

```
//Window.cpp
	#include "Window.h"

	Window::Window(QWidget *parent)
	    : QWidget(parent)
	{   
    //创建主分割窗口 设置方向为竖直分割窗口  
    
	QSplitter *mainSplitter = new
		QSplitter(Qt::Vertical,this);  
	
	//创建一个QToolbar控件 设置其父控件为mainSplitter 
	 QToolBar *qtoolbar = new QToolBar(mainSplitter);

 
    //创建下分割窗口 设置其父控件为mainSplitter 设置方向为垂直分割窗口 最后其分割成上下两个窗口
      QSplitter *downSplitter = new QSplitter(Qt::Horizontal, mainSplitter);
    //开始将其分割成上下两个窗口  
    QSplitter *leftSplitter = new QSplitter(Qt::Vertical,downSplitter);  
    upEdit= new QLineEdit(tr("请输入x的值"), leftSplitter);
    connect(upEdit,SIGNAL(textChanged(QString)),this,SLOT(upEditChanged(QString)));
    bottomEdit = new QLineEdit(tr("请输入y的值"), leftSplitter);
    connect(bottomEdit,SIGNAL(textChanged(QString)),this,SLOT(bottomEditChanged(QString)));
    midEdit= new QTextEdit(tr("请输入时在每个数字后面加一个“,”，  输入时不要按除了数字和“,”“.”之外的键(请输入英文字符‘,’）。  点击文件按钮会出现一个文件浏览器，  选择文件后，右击文件即可选择打开方式打开文件。  编辑框下面的红色句子是计算结果。"), leftSplitter);
    QWidget *qwidget= new QWidget(downSplitter);
    //设定可伸缩控件 第一个参数指定控件设置的控件序号，该控件序号按插入的先后次序依次从0开始编号  
    //第二个参数大于0表示此控件为伸缩控件 此例中设置右部分割窗口为可伸缩控件 
    mainSplitter->setStretchFactor(0, 1); 
    mainSplitter->setStretchFactor(1, 5);  
    downSplitter->setStretchFactor(0, 10);
    downSplitter->setStretchFactor(1, 11);
    leftSplitter->setStretchFactor(0, 4);  
    leftSplitter->setStretchFactor(1, 5);
    leftSplitter->setStretchFactor(2, 6);

    QToolButton *toolButton1 = new QToolButton();
    QToolButton *toolButton2 = new QToolButton();
    QToolButton *toolButton3 = new QToolButton();
    QToolButton *toolButton4 = new QToolButton();
    QToolButton *toolButton5 = new QToolButton();

    toolButton1->setText(QObject::tr(" 文件 "));
    connect(toolButton1,SIGNAL(clicked()),this,SLOT(doPushBtn()));
    toolButton2->setText(QObject::tr("函数一"));
    toolButton3->setText(QObject::tr("函数二"));
    toolButton4->setText(QObject::tr("函数三"));
    toolButton5->setText(QObject::tr("函数四"));    

    qtoolbar->addWidget(toolButton1);
    qtoolbar->addWidget(toolButton2);
    qtoolbar->addWidget(toolButton3);
    qtoolbar->addWidget(toolButton4);
    qtoolbar->addWidget(toolButton5);

    mainSplitter->show();
} 
void Window::upEditChanged(const QString &text)
{
    name_x = text;
}

void Window::bottomEditChanged(const QString &text)
{
    name_y = text;
}
void Window::doPushBtn()
{   
        QString fileName = QFileDialog::getOpenFileName(
                    this,
                    tr("打开文件"),
                    "/usr/local/Trolltech",
                    tr("任何文件(*.*)"
                    ";;文本文件(*.txt)"
                    ";;XML文件(*.xml)"));
}
void Window::paintEvent(QPaintEvent *){
    QPainter painter(this);
    painter.setPen(QPen(Qt::red, height() / 150));
    int sizeofa=name_x.length();
    int sizeofb=name_y.length();
    jo=0;
    int ho=0;
    for (int i = 0; i < sizeofa; ++i)
    {   
            if(name_x[i]!=','){
                xo.append(name_x.mid(i,1));
                if(name_x[i+1]==','){
                    jo=jo+1;
                    x[jo-1]=xo.toDouble();
                    xo="";
                }
            }
    }
    for (int i = 0; i < sizeofb; ++i){
            if(name_y[i]!=','){
                yo.append(name_y.mid(i,1));
                if(name_y[i+1]==','){
                    ho=ho+1;
                    y[ho-1]=yo.toDouble();
                    yo="";
                }
            }
    }
        if (jo>200) 
    {
        painter.drawText(10,330,"Please make sure the number of datas must be less than 200!");//提示若数据数量溢出；
    }    
    double s = 0;
    for (int i = 0; i < jo; i++)   
        s = s + x[i];
    ave_x = s / jo;
    s = 0;
    for (int i = 0; i < jo; i++)  
        s = s + y[i];
    ave_y = s / jo;
    s = 0;
    for (int i = 0; i < jo; i++) { 
        ave_x_dev[i] = x[i] - ave_x;  
        s = s + ave_x_dev[i] * ave_x_dev[i];//考虑方差计算表达式很复杂，对公式进行拆分求和计算；
    }
    ox = s / jo;
    s_x = sqrt(ox);
    s = 0;
    for (int i = 0; i < jo; i++) {
        ave_y_dev[i] = y[i] - ave_y;   
        s = s + ave_y_dev[i] * ave_y_dev[i];
    } 
    oy = s / jo;
    s_y = sqrt(ox);
    sx = 0, sy = 0, sm = 0;
    double r;
    for (int i = 0; i < jo; i++)
    {
        sm = sm + ave_x_dev[i] * ave_y_dev[i];
        sx = sx + ave_x_dev[i] * ave_x_dev[i];
        sy = sy + ave_y_dev[i] * ave_y_dev[i];
    }
    r = sm / (sqrt(sx)*sqrt(sy));//r为相关系数；
    if (r*r >= 0.64)
        painter.drawText(10,270,"The datas are well to be fitted in line.");
    else{
        if (r*r<0.64&&r*r>0.37)
            painter.drawText(10,270,"The datas can not be accurately fitted in line.");
        else
            painter.drawText(10,270,"We don't recommend to fit these datas in line!");
        }

    b = sm / sx;
    a = ave_y - b*ave_x;
    double r2=r*r;
    QString ao=QString::number(a, 'f', 8);
    QString bo=QString::number(b, 'f', 8);
    QString ro=QString::number(r2, 'f', 8);
    
    QString str = QString("%1%2%3%4%5").arg("y =").arg(" x*").arg(bo).arg(" + ").arg(ao);
    QString st = QString("%1%2").arg("r^2 =").arg(ro);
        painter.drawText(10,290,str);
        painter.drawText(10,310,st);

    int side = qMin(width(), height());                                           //创建窗口宽高参数
    painter.setRenderHint(QPainter::Antialiasing,true);                //开启抗锯齿
    painter.translate(width() / 2, height() / 2);                               //坐标系统平移变换，把原点平移到窗口中心
    painter.scale(side / 300.0, side / 300.0);            //坐标系统比例变换，使绘制的图形随窗口的放大而放大
    painter.scale(1, -1);
    painter.setPen(QPen(Qt::black, height() / 600));                                          //Y轴向上翻转，翻转成正常平面直角坐标系
    painter.drawLine(-200,-200*b+a,200,200*b+a);
    painter.setPen(QPen(Qt::blue, height() / 600));
    painter.drawLine(-2000,0,2000,0);
    painter.drawLine(0,1500,0,-1500);
    painter.setPen(QPen(Qt::red, height() / 600));
    for (int i = 0; i <= jo; ++i)
    {   
        painter.drawPoint(x[i],y[i]);
    }

    update();
}   
```

```
//main.cpp

#include "Window.h"  

int main(int argc, char *argv[])
{
       
    QApplication a(argc, argv);
    QTextCodec *codec = QTextCodec::codecForName("utf8");
    QTextCodec::setCodecForLocale(codec);
    QTextCodec::setCodecForCStrings(codec);
    QTextCodec::setCodecForTr(codec);
    Window w;
    w.showMaximized();
    w.show();
    w.repaint();
    return a.exec();
}      
```
##使用qmake+mingw编译QT

> - 从开始菜单进入qt4.8.5command prompt 控制台界面

> ![](http://img.blog.csdn.net/20150616104758664)

> - 点开控制台界面后如图操作

>![这里写图片描述](http://img.blog.csdn.net/20150616105752847)

> - 前两步进入文件所在的文件夹。
> - 第三步生成 a.pro 文件。（a 为该文件夹名称，可替换）
> - 第四步因为C++代码中使用了信号和槽。"发现就跟Q_OBJECT这个宏有关。我用的是QT Designer，一般只有用到信号和槽时才会用到MOC，因为采用信号和槽是Qt的特性，而C++没有，所以采用了MOC(元对象编译器)把信号和槽部分编译成C++语言。好，我们就用
> 
         moc mainwindow.h  –o moc_mainwindow.cpp

>生成 moc_mainwindow.cpp 文件并加入工程。再次编译通过。
>
由于用的是 QT Designer , 一般只有用到信号signals和槽slots时才会用到 MOC , 因为采用信号 signals 和槽 slots 是QT的特性,而C++没有,所以采用了MOC(元对象编译器)把信号 signals 和槽 slots 部分编译成 C++ 语言。
用信号 signals 和槽 slots 需注意的基本问题是：
（1）、在类 class 声明中必须加入 Q_OBJECT；
（2）、在 CPP 文件中要把信号 signals 和槽 slots 联系起来,即使用connect , 例 connect( iv, SIGNAL(clicked (QIconViewItem *)), this, SLOT( draw()))；

>再次强调：只要按上述方式就行了，因为MOC文件的生成和继承都是自动的!!!"[上述引用出自此处](http://www.cnblogs.com/newthing/archive/2011/06/28/2157485.html)
>
> [Qt 出现“undefined reference to `vtable for”原因总结](http://blog.csdn.net/baymoon/article/details/1409167)
>  - 第五步是错误的，第六七步正确，这里不做多讲。