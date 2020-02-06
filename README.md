# Study-Notes
C++学习笔记
大多来自小甲鱼的鱼C论坛
1、多态实现原理；重载、覆盖、隐藏的区别
C++类的多态通过抽象方法实现，函数多态通过重载实现。而对于区分重载、覆盖和隐藏：
1、重载是指同一类中函数重载，函数名相同参数不同；
2、覆盖指子类对基类中函数的覆盖，子与父类中函数名相同，参数相同（若参数不同则为隐藏），基类中声明时必须加virtual关键字；
3、隐藏指子类对父类的函数的隐藏，若子类与基类函数名相同，但参数不同；或者函数名相同，参数相同，但并未声明为虚方法。基类函数隐藏本质是失误，即在基类和子类中误用了不同的参数，或者在基类中漏掉了virtual关键字。基类函数隐藏会导致不可预知的错误发生。
以下进行举例说明：
(```C++
#include <iostream>
using namespace std;

class Base
{
public:
    void human(int age)
    {
        cout<<"Class Base::human(int age) "<<endl;
    }
    void man(int age)
    {
        cout<<"Class Base::man(int age) "<<endl;
    }
    //重载
    void man(int age, float weight)
    {
        cout<<"Class Base::man(int age, float weight) "<<endl;
    }
    virtual void woman(int age);
}

class Derived
{
public:
    void human(float age)
    {
        cout<<"Class Derived::human(float age)"<<endl;
    }
    void women(int age)
    {
        cout<<"class Derived::woman(int age)"<<endl;
    }   
}
```)

(```C++
int main
{
    
    Derived d;
    Base* pb = &d;//将Derived对象地址赋给Base对象时，编译器自动进行类型转换，
    编译器此时认为保存的就是Base对象地址
    b->human(23.5);//基类human被隐藏
    b->woman(21);//基类woman被覆盖
    b->man(23);
    b->man(23,60.0);//重载函数正常使用
     
    return 0;
}
```)
最简单的是重载，同一个类中为了省去对新声明函数重新命名的麻烦，可以使用同一函数名，只要参数表不同即可。但是注意返回值类型也必须一致，否则会变成重新定义，是不允许的。
对于虚方法的覆盖，程序一般运行时，先找到找到类，如果它有基类，再找它的基类，最后运行的是基类中的函数，这时，它在基类中找到的是virtual标识的函数，它就会再回到子类中找同名函数。

2、0xC0000005: 读取位置 0x00000000 时发生访问冲突
来自：https://blog.csdn.net/heathyhuhu/article/details/18039359
遇见这种问题一般都是空指针，即：指针里没有赋值~ 
如果你对null 进行操作就会产生空指针异常

Object obj = new Object();
你要知道 obj是一个Object指针变量,指向Object类的一个实例
我们说obj是一个对象 实质是它指向一个对象的首地址 
如果这个指针变量obj 没有指向任何空间 你调用它的方法和属性就会出错

例如
(```C++
Object obj = new Object();
obj.equals("123");
```)
这个是没错的

但换下面语句 
(```C++
Object obj = null;
obj.equals("123");
```)
就会出现空指针异常
未处理的异常: 0xC0000005: 读取位置 0x00000000 时发生访问冲突

     在使用的过程中，出现了标题中的错误，首先在网上搜了一些方法，费了好大的劲，终于解决了，
关于0xC0000005问题：
0xC0000005: Access Violation错误调试- -
1》数据越界或是定义的指针未释放.
2》空的指针的可能性最大。使用指针前最好能显式的赋值！ 
应该是指针的问题
3》内存访问错误，检查指针，是否为空，是否越界等
可能性 3 种 
1: 
char *p; 
p = new char[number]; 
delete [] p; 
.... 
// always using p.... 
p = xxx; // access violation 

2: 
char *p; 
memcpy(p, xxx, number); // access violation 

3: 
char *p; 
p = new char[number]; 
delete [] p; 
......... 
delete [] p; // access violation
3、容器QVector、QList
遍历一
QList<QVariant>* tagNameList = new QList<QVariant>;
QStringList *columnlabels = new QStringList;
	foreach (Sep_LoopTag* looptag,loops)
	{
		tagNameList->insert(tagNameList->end(), looptag->tagName);
		columnlabels->insert(columnlabels->end(), looptag->tagName);
	}

遍历二
QStringList p_list = lstRelation[nRow].strPrimaryLoop.split(',');
QList<QString>::iterator i; 
for(i=p_list.begin();i<p_list.end();++i)
	{
		if(columnlabels->contains(*i),QString::SkipEmptyParts)
			p_chkList->setChecked(columnlabels->indexOf(*i,1),true);
	}	
4、Qt自定义下拉框和多选框
//继承自QWidget的下拉框
ComboBoxBase::ComboBoxBase(QWidget *parent)
: QComboBox(parent)
{
}
ComboBoxBase::~ComboBoxBase()
{
}
void ComboBoxBase::wheelEvent( QWheelEvent * event )
{
	return;
}
//使用自定义下拉框类
ComboBoxBase* cmbPlant = new ComboBoxBase;
foreach (SEP_PLANT plant,plantList)
{
	cmbPlant->addItem(plant.name,plant.id);
	if (lstRelation[nRow].nPlant == plant.id)
	{
		cmbPlant->setCurrentText(plant.name);
	}
}
ui->tableWidget->setCellWidget(nRow,0,cmbPlant);
	
//多选框
//头文件
#ifndef CHECKLISTVIEW_H
#define CHECKLISTVIEW_H

#include <QListWidget>
#include <QCheckBox>
#include <QComboBox>

class CheckListView : public QListWidget
{
    Q_OBJECT
public:
    CheckListView(QWidget* parent=0, QStringList *columnlabels=NULL, QList<QVariant> *data=NULL, QComboBox* cmb=NULL);
    ~CheckListView();
    void getSelectData(QList<QVariant> &data);

    void setChecked(int index,bool checked);

private slots:
    //QCheckBox复选消息处理
    void slot_setSelectItem(int state);


private:
    QComboBox         *m_cmbox;
    QList<bool>       m_bchecked;
    QList<QVariant>   m_data;
    QList<QCheckBox*> m_checklist;
    //QList<QString>    m_fslist;
};

#endif // CHECKLISTVIEW_H
//cpp文件
#include "checklistview.h"

CheckListView::CheckListView(QWidget* parent,QStringList *columnlabels,QList<QVariant> *data,QComboBox* cmb)
    :QListWidget(parent)
{
    setViewMode(QListWidget::ListMode);
    //setSelectionMode(QAbstractItemView::SingleSelection);
    m_cmbox = cmb;

    for( int i=0; i<columnlabels->size(); i++ )
    {
        m_bchecked.append(false);
        m_data.append(data->at(i));

        QListWidgetItem *item = new QListWidgetItem();
        //item->setFlags(Qt::ItemIsSelectable | Qt::ItemIsUserCheckable |Qt::ItemIsEnabled);
		item->setFlags(Qt::ItemIsUserCheckable);
        item->setData(Qt::UserRole+1, data->at(i));
        insertItem(model()->rowCount(), item);

        QCheckBox* box = new QCheckBox(columnlabels->at(i));
        setItemWidget(item,  box);
        m_checklist.append(box);
        //m_fslist.append(columnlabels->at(i));
        //链接复选状态改变信号槽

        connect(box, SIGNAL(stateChanged(int)), this, SLOT(slot_setSelectItem(int)));
    }
}

CheckListView::~CheckListView()
{
}
void CheckListView::getSelectData(QList<QVariant> &data)
{
    for( int i=0; i<m_bchecked.size(); i++)
    {
        if( m_bchecked.at(i)== true)
            data.append( m_data.at(i) );
    }
}

void CheckListView::slot_setSelectItem(int state)
{
	Q_UNUSED(state);
    QCheckBox*  box = qobject_cast<QCheckBox*>(sender());
    for( int off = 0;off<m_checklist.size(); off++)
    {
        if( box == m_checklist.at(off) )
        {
            m_bchecked[off] = (box->checkState() ==  Qt::Checked) ? true : false;
            break;
        }
    }

//    QString strfs;
	QStringList lstfs;
    for( int i=0; i<m_bchecked.size(); i++)
    {
        if( m_bchecked.at(i)== true)
        {
//             if(!strfs.isEmpty())
//             {
//                 strfs += ",";
//             }
//             strfs += m_fslist.at(i);
			lstfs << m_checklist[i]->text();
        }
    }

    //m_cmbox->setEditText( strfs );
	m_cmbox->setEditText(lstfs.join(","));
}

void CheckListView::setChecked(int index,bool checked)
{
    if(index >= 0 && index < m_checklist.size())
    {
        m_checklist[index]->setChecked(checked);
    }
}
//使用checklist类
ComboBoxBase* cmbPrimaryLoop = new ComboBoxBase;
cmbPrimaryLoop->setEditable(true);
CheckListView* p_chkList = new CheckListView(this,columnlabels,tagNameList,cmbPrimaryLoop);
cmbPrimaryLoop->setModel(p_chkList->model());
cmbPrimaryLoop->setView(p_chkList);
//读取数据库中多项拆开，而后将对应复选框选中
QStringList p_list = lstRelation[nRow].strPrimaryLoop.split(',',QString::SkipEmptyParts);
QList<QString>::iterator i; 
for(i=p_list.begin();i<p_list.end();++i)
{
	if(columnlabels->contains(*i))
		p_chkList->setChecked(columnlabels->indexOf(*i,1),true);
}
ui->tableWidget->setCellWidget(nRow,2,cmbPrimaryLoop);
	
2020.2.6
  今天改SEPSIM的需求，深感小白的煎熬。在一个地方卡了整整一天，具体情况如下：需求是将菜单栏中某菜单上的动作添加到右键，如果在同一类中较为简单，只需将改功能地址传过来就可以。
  例：
  
    //菜单栏
    QMenuBar *mBar = menuBar();

    //添加菜单
    QMenu *pFile = mBar->addMenu("文件");

    //添加菜单项，添加动作
    QAction *pNew = pFile->addAction("新建");
    
    //工具栏，菜单项的快捷方式
    QToolBar *toolBar = addToolBar("toolBar");

    //工具栏添加快捷键
    toolBar->addAction(pNew);
这样就相当于把菜单栏中的动作复制一份到工具栏中，将其加到右键也是如此。但是我碰到的情况是：菜单栏和触发右键按钮属于不同对象。右键在画面上触发，而菜单栏在主界面上，在项目中分属两个类。但是，但是，画面是建立在主界面上的：
    itemScene* pic = new itemScene(this);
而如何使pic上的触发信号传到主界面上呢，获取到主界面地址就ok啦。就是在这里，我像傻子一样苦苦追寻。然而只需要一个简单的函数就可以搞定。
    myWidget* par = (myWidget*)parent();
接下来就豁然开朗啦，搜答案看到一句话特别棒：写代码是一种艺术，甚于蒙娜丽莎的微笑。
