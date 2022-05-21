

# 1.简介

Gui的核心技术： Swing和AWT。

GUI为什么不流行。

1.界面不美观

2.需要jre环境，几百兆内存

那我们为什么要去学习呢？

1. 可以写出自己心中想要的一些小工具。
2. 工作时候，也可能维护到swing界面，但概率极小。
3. 了解MVC架构，了解监听！
4. 强化对java的理解。

# 2.AWT（Swing前身，讲底层的实现）

## 2.1 AWT介绍

1. 包含了很多类和接口！GUI：图像用户界面。 Eeclipse：java环境写的
2. 元素：窗口、按钮、文本框
3. java.awt包里

## 2.2、组件和容器

### 2.2.1、Frame

```java
package com.shuai.lesson1;
import java.awt.*;
//GUI的第一个界面
public class TestFrame {
    public static void main(String[] args) {
        //Frame,JDK  看源码；
        Frame frame = new Frame("我的第一个java图形界面窗口");
        //设置可见性 w h 没有
        frame.setVisible(true);
        //设置窗口大小
        frame.setSize(400, 400);
        //背景颜色 Color
        Color color = new Color(155, 89, 104);
        frame.setBackground(color);
        //弹出的初始位置
        frame.setLocation(200, 200);
        //设置大小固定
        frame.setResizable(false);
    }
}
```

问题：窗口关闭不了，需要退出java运行的进程才能关闭。

回顾一下封装知识：

```java
package com.shuai.lesson1;
import java.awt.*;
public class TestFrame2 {
    public static void main(String[] args) {
        MyFrame myFrame1 = new MyFrame(100, 100, 200, 200, Color.blue);
        MyFrame myFrame2 = new MyFrame(300, 100, 200, 200, Color.yellow);
        MyFrame myFrame3 = new MyFrame(500, 100, 200, 200, Color.RED);
        MyFrame myFrame4 = new MyFrame(700, 100, 200, 200, Color.BLACK);
    }
}
class MyFrame extends Frame{
    //除了Frame方法以外，还有自己的方法
    static int id = 0;//可能存在多个窗口，我们需要一个计数器
    //构造器封装一下
    public MyFrame(int x,int y, int w,int h,Color color){
        super("MyFrame+"+(++id));
        setBackground(color);
        setVisible(true);
        //相当于初始位置x，y和窗口大小w h
        setBounds(x,y,w,h);
        setResizable(false);
    }
}
```

### 2.2.2、面板panel

解决了上方的无法点击关闭窗口的问题，即调用addWindowsListener方法的子方法，并重写其中的WindowsClosing方法，来调用System.exit(0)方法。

```java
package com.shuai.lesson1;
import java.awt.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
import java.awt.event.WindowListener;
//Panel 可以看成一个空间，但是不能单独存在，得放在Frame上
public class TsetPanel {
    public static void main(String[] args) {
       //窗口
        Frame frame = new Frame();
        //布局的概念
        //面板
        Panel panel = new Panel();
        /*设置布局  不设置会默认置顶
        未设置Layout时，java默认为flowLayout布局的，
         设置为null即为清空布局管理器，之后添加组件，常常是设置组件左上角坐标相
         对于容器左上角（0，0）的x,y值来确定组件的位置，即使更改容器大小也不会
         改变位置。这种方式常常用于窗体大小固定的容器里。*/
       	frame.setLayout(null);
        //坐标
        frame.setBounds(300,300,500,500);
        frame.setBackground(new Color(59, 164, 125));
        //panel 设置坐标，相对于Frame的坐标
        panel.setBounds(50,50,200,200);
        panel.setBackground(new Color(90, 46, 30));
        //frame.add(panel)frame添加面板
        frame.add(panel);//Panel经过三层继承，最终继承了Component组件
        frame.setVisible(true);
        //监听时间，监听窗口关闭事件   System.exit(0)
        //适配器模式：接口new WindowsListener()重写的方法太多了,则new WindowAdapter()其子类，该类实现了该接口
        frame.addWindowListener(new WindowAdapter() {
           //窗口点击关闭的时候需要做的事情
            @Override
            public void windowClosing(WindowEvent e) {
                //结束程序
                System.exit(0);
            }
        });
    }
}
```

## 2.3、布局管理器

流式布局。

```java
package com.shuai.lesson1;
import java.awt.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
public class TsetFlowLayout {
    public static void main(String[] args) {
        Frame frame = new Frame();
        //组件-按钮
        Button button1 = new Button("button1");
        Button button2 = new Button("button2");
        Button button3 = new Button("button3");
        //设置为流式布局
        //frame.setLayout(new FlowLayout());默认是中
        //frame.setLayout(new FlowLayout(FlowLayout.LEFT));
        frame.setLayout(new FlowLayout(FlowLayout.RIGHT));
        frame.setSize(200,200);
        frame.setVisible(true);
        frame.add(button1);
        frame.add(button2);
        frame.add(button3);
        frame.addWindowListener(new WindowAdapter() {
            //窗口点击关闭的时候需要做的事情
            @Override
            public void windowClosing(WindowEvent e) {
                //结束程序
                System.exit(0);
            }
        });
    }
}
```

东西南北中布局。

```java
package com.shuai.lesson1;
import java.awt.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
public class TestBorderLayout {
    public static void main(String[] args) {
        Frame frame = new Frame("TestBorderLayout");
        Button east = new Button("East");//
        Button west = new Button("West");
        Button south = new Button("South");
        Button north = new Button("North");
        Button center = new Button("Center");
        frame.add(east,BorderLayout.EAST);
        frame.add(west ,BorderLayout.WEST);
        frame.add(south ,BorderLayout.SOUTH);
        frame.add(north ,BorderLayout.NORTH);
        frame.add(center,BorderLayout.CENTER);
        frame.setSize(300,300);
        frame.setVisible(true);
        frame.addWindowListener(new WindowAdapter() {
            @Override
            public void windowClosing(WindowEvent e) {
               System.exit(0);
            }
        });
    }
}
```

表格布局。

```java
package com.shuai.lesson1;
import java.awt.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
public class TestGridLayout {
    public static void main(String[] args) {
        Frame frame = new Frame("TsetGridLayout");
        Button button1 = new Button("button1");
        Button button2 = new Button("button2");
        Button button3 = new Button("button3");
        Button button4 = new Button("button4");
        Button button5 = new Button("button5");
        Button button6 = new Button("button6");
        frame.setLayout(new GridLayout(3,2));
        frame.add(button1);
        frame.add(button2);
        frame.add(button3);
        frame.add(button4);
        frame.add(button5);
        frame.add(button6);
        frame.pack();//让布局变得好看
        frame.setVisible(true);
        frame.addWindowListener(new WindowAdapter() {
            @Override
            public void windowClosing(WindowEvent e) {
                System.exit(0);
            }
        });
    }
}
```

通过前面的学习做出下面这个界面。

![image-20220413153519044](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220413153519044.png)

```java
package com.shuai.lesson1;
import jdk.nashorn.api.tree.NewTree;
import java.awt.*;
import java.io.FileOutputStream;
public class TestLayoutLianxi {
    public static void main(String[] args) {
        Frame frame = new Frame("TestLayoutLianxi");
        frame.setSize(400,300);
        frame.setLocation(300,300);
        frame.setBackground(Color.BLUE);
        frame.setVisible(true);
        frame.setLayout(new GridLayout(2,1));
        Panel panel1 = new Panel(new BorderLayout());
        Panel panel2 = new Panel(new GridLayout(2,1));
        Panel panel3 = new Panel(new BorderLayout());
        Panel panel4 = new Panel(new GridLayout(2,2));
        Button button1 = new Button("BUTTON1");
        Button button2 = new Button("BUTTON2");
        Button button3 = new Button("BUTTON3");
        Button button4 = new Button("BUTTON4");
        Button button5 = new Button("BUTTON5");
        Button button6 = new Button("BUTTON6");
        Button button7 = new Button("BUTTON7");
        Button button8 = new Button("BUTTON8");
        Button button9 = new Button("BUTTON9");
        Button button10 = new Button("BUTTON10");
        panel1.add(button1,BorderLayout.EAST);
        panel1.add(button2,BorderLayout.WEST);
        panel2.add(button3);
        panel2.add(button4);
        panel1.add(panel2,BorderLayout.CENTER);
        panel3.add(button5,BorderLayout.EAST);
        panel3.add(button6,BorderLayout.WEST);
        panel4.add(button7);
        panel4.add(button8);
        panel4.add(button9);
        panel4.add(button10);
        panel3.add(panel4,BorderLayout.CENTER);
        frame.add(panel1);
        frame.add(panel3);
    }
}
```

知识小总结

1. Frame是一个顶级窗口
2. Panel无法单独显示，必须添加到某个容器中
3. 布局管理器
   1. 流式
   2. 东西南北中
   3. 表格
4. 标题，大小，定位，背景颜色，可见性，监听。

## 2.4、事件监听

事件监听：当某个事件发生的时候，干什么？

```java
package com.shuai.lesson2;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
public class TestActionEvent {
    public static void main(String[] args) {
        //按下按钮，触发一些事件
        Frame frame = new Frame();
        Button button1 = new Button("button1");
        //因为addActionListener需要ActionListener，因此我们需要构造一个ActionListener
        MyActionListener myActionListener = new MyActionListener();
        button1.addActionListener(new MyActionListener());
        frame.add(button1, BorderLayout.CENTER);
        frame.pack();
        frame.setVisible(true);
        windowsClosing(frame);
    }
    //关闭窗体的事件
    private static void windowsClosing(Frame frame){
        frame.addWindowListener(new WindowAdapter() {
            @Override
            public void windowClosing(WindowEvent e) {
                System.exit(0);
            }
        });
    }
//事件监听
    static class MyActionListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            System.out.println("11");
        }
    }
}
```

多个按钮共享一个事件

```java
package com.shuai.lesson2;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.FileOutputStream;
public class TestActionEvent2 {
    public static void main(String[] args) {
        //两个按钮，实现同一个监听
        Frame frame = new Frame("1111");
        Button button1 = new Button("start");
        Button button2 = new Button("stop");
        //可以显示的定义出发会返回的命令，如果不显示定义，则会走默认值
        //可以多个按钮只写一个监听类
        button1.setActionCommand("3333");
        My my = new My();
        button1.addActionListener(my);
        button2.addActionListener(my);
        frame.add(button1, BorderLayout.WEST);
        frame.add(button2, BorderLayout.EAST);
        frame.pack();
        frame.setVisible(true);
    }
    static class My implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            //e.getActionCommand()获得按钮的信息
            System.out.println("按钮被点击了：msg" + e.getActionCommand());
        }
    }
}
```

## 2.5、输入框事件监听TextField

输入框中输入的字，可以打印出来，并将输入的字全部删除。

```java
package com.shuai.lesson2;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
public class TestText01 {
    public static void main(String[] args) {
        //启动！只负责启动
        new MyFrame();
}
}
class MyFrame extends Frame{
    public MyFrame(){
        TextField textField = new TextField();
        add(textField);
        //监听这个文本框输入的文字
        //按下回车键，就会触发这个输入框的事件，在下边的重写方法中重写的语句为  获得输入框的文本并打印
        textField.addActionListener(new My());
        //设置替换编码
        textField.setEchoChar('*');
        setVisible(true);
        pack();
    }
}
class My implements ActionListener{
    @Override
    public void actionPerformed(ActionEvent e) {
        TextField text = (TextField) e.getSource();//获得一些资源,返回的一个对象
        System.out.println(text.getText());//获得输入框的文本
        //每次都设置为空 即每次文本框输入完以后，都会全部删除清零
        text.setText("");
    }
}
```

## 	2.6、简易计算器，组合+内部类回顾复习

oop原则：组合，大于继承

初始版本，面向过程版本

```java
package com.shuai.lesson2;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.TextEvent;
public class TestCALC {
    public static void main(String[] args) {
        new Calculator();
    }
}
//计算器类
class Calculator extends Frame {
    public Calculator() {
        //3个文本框
        TextField textField = new TextField(10);//字符数
        TextField textField1 = new TextField(10);//字符数
        TextField textField2 = new TextField(20);//字符数
        //1 个按钮
        Button button = new Button("=");
        button.addActionListener(new MyAL(textField,textField1,textField2));
        //1个标签
        Label label = new Label("+");
        setLayout(new FlowLayout());
        add(textField);
        add(label);
        add(textField1);
        add(button);
        add(textField2);
        pack();
        setVisible(true);
    }
}
//监听器类
class MyAL implements ActionListener {
    //获取三个变量
    private TextField textField,textField1,textField2;
    public MyAL(TextField textField, TextField textField1, TextField textField2){
              this.textField = textField;
              this.textField1 = textField1;
              this.textField2 = textField2;
    }
    @Override
    public void actionPerformed(ActionEvent e) {
        //1.获得加数和被加数
        int n = Integer.parseInt(textField.getText());
        int n1 = Integer.parseInt(textField1.getText());
        //2.将这个值加法运算后，放到第三个框
        textField2.setText(""+(n+n1));
        //3.清除前两个框
        textField.setText("");
        textField1.setText("");
    }
}
```

面向对象版本

```java
package com.shuai.lesson2;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.TextEvent;
public class TestCALC {
    public static void main(String[] args) {
        new Calculator().loadFrame();
    }
}
//计算器类
//方法
class Calculator extends Frame {
    //属性
    TextField textField;
    TextField textField1;
    TextField textField2;
    //方法
    public void loadFrame() {
        //3个文本框
        textField = new TextField(10);//字符数
        textField1 = new TextField(10);//字符数
        textField2 = new TextField(20);//字符数
        //1 个按钮
        Button button = new Button("=");
        button.addActionListener(new MyAL(this));
        //1个标签
        Label label = new Label("+");
        setLayout(new FlowLayout());
        add(textField);
        add(label);
        add(textField1);
        add(button);
        add(textField2);
        pack();
        setVisible(true);
    }
}
//监听器类
class MyAL implements ActionListener {
    /* //获取三个变量
     private TextField textField,textField1,textField2;
     public MyAL(TextField textField, TextField textField1, TextField textField2){
               this.textField = textField;
               this.textField1 = textField1;
               this.textField2 = textField2;
     }
     @Override
     public void actionPerformed(ActionEvent e) {
         //1.获得加数和被加数
         int n = Integer.parseInt(textField.getText());
         int n1 = Integer.parseInt(textField1.getText());
         //2.将这个值加法运算后，放到第三个框
         textField2.setText(""+(n+n1));
         //3.清除前两个框
         textField.setText("");
         textField1.setText("");
     }
 }
 */
    //改进算法
    //获取计算器这个对象，在一个类中组合另外一个类
    Calculator calculator = null;
    public MyAL(Calculator calculator) {
        this.calculator = calculator;
    }
    @Override
    public void actionPerformed(ActionEvent e) {
        //1.获得加数和被加数
        //2.将这个值加法运算后，放到第三个框
        //3.清除前两个框
        int text = Integer.parseInt(calculator.textField.getText());
        int text1= Integer.parseInt(calculator.textField1.getText());
        calculator.textField2.setText(""+(text1+text));
        calculator.textField.setText("");
        calculator.textField.setText("");
    }
}
```

内部类版本：最大的好处，就是可以畅通无阻的访问外部的属性和方法。

```java
package com.shuai.lesson2;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.TextEvent;
public class TestCALC {
    public static void main(String[] args) {
        new Calculator().loadFrame();
    }
}
//计算器类
//方法
class Calculator extends Frame {
    //属性
    TextField textField;
    TextField textField1;
    TextField textField2;
    //方法
    public void loadFrame() {
        //3个文本框
        textField = new TextField(10);//字符数
        textField1 = new TextField(10);//字符数
        textField2 = new TextField(20);//字符数
        //1 个按钮
        Button button = new Button("=");
        button.addActionListener(new MyAL());
        //1个标签
        Label label = new Label("+");
        setLayout(new FlowLayout());
        add(textField);
        add(label);
        add(textField1);
        add(button);
        add(textField2);
        pack();
        setVisible(true);
    }
      private   class MyAL implements ActionListener {
            @Override
            public void actionPerformed(ActionEvent e) {
                //1.获得加数和被加数
                //2.将这个值加法运算后，放到第三个框
                //3.清除前两个框
                int text = Integer.parseInt(textField.getText());
                int text1 = Integer.parseInt(textField1.getText());
                textField2.setText("" + (text1 + text));
                textField.setText("");
                textField.setText("");
            }
    }
```

## 2.7、画笔

```java
package com.shuai.lesson3;
import java.awt.*;
public class TestPaint {
    public static void main(String[] args) {
        new MYpaint().loadFrame1();
    }
}
class MYpaint extends Frame{
    public void loadFrame1(){
        setBounds(200,200,600,400);
        setVisible(true);
    }
    @Override
    public void paint(Graphics g) {
        //super.paint(g);有些类的父类有一些初始化操作，不能随便干掉
        //画笔，需要颜色，画笔可以画画
        g.setColor(Color.red);
        g.drawOval(100,100,100,100);
        g.fillOval(200,200,100,100);//实心的⚪
        g.setColor(Color.green);
        g.fillRect(300,300,40,20);
        g.drawRect(300,350,40,20);
        //养成习惯 画笔画完，将他还原到最初的颜色
        g.setColor(Color.BLACK);
    }
}
```

## 2.8、鼠标监听

目的，想要实现鼠标画画。

```java
package com.shuai.lesson3;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.util.ArrayList;
import java.util.Iterator;
//鼠标监听事件
public class TestMouseListener {
    public static void main(String[] args) {
        new MyFrame("画图");
    }
}
//自己的类
class MyFrame extends Frame {
    //画画需要画笔，需要监听鼠标当前的位置，需要集合来存储这个点
    ArrayList points;
    public MyFrame(String title) {
        super(title);
        setBounds(100, 100, 500, 400);
        //存鼠标的点
        points = new ArrayList<>();
        //鼠标监听器，针对这个窗口
        setVisible(true);
        this.addMouseListener(new MyML());
    }
    @Override
    public void paint(Graphics g) {
        //画画，监听鼠标的事件
        Iterator iterator = points.iterator();
        while (iterator.hasNext()){
            Point point = (Point) iterator.next();
            g.setColor(Color.BLUE);
            g.fillOval(point.x,point.y,10,10);
        }
    }
//添加一个点到界面上
    public void addPaint(Point point){
        points.add(point);
    }
    //适配器模式
    private class MyML extends MouseAdapter {
        //鼠标 按下，弹起，按住不放
        @Override
        public void mouseClicked(MouseEvent e) {
            MyFrame myframe = (MyFrame) e.getSource();
            //这里我们点击的时候，就会在界面产生一个点
            myframe.addPaint(new Point(e.getX(),e.getY()));
            //每次点击鼠标都需要重新画一遍
            myframe.repaint();//刷新
        }
    }
}
```

## 2.9、窗口监听

```java
package com.shuai.lesson3;
import java.awt.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
public class TestWindow {
    public static void main(String[] args) {
        new WindowF();
    }
}
class WindowF extends Frame {
    public WindowF() {
        setBackground(Color.BLUE);
        setBounds(100, 100, 200, 200);
        setVisible(true);
        this.addWindowListener(
                //匿名内部类
                new WindowAdapter() {
                    @Override
                    public void windowClosing(WindowEvent e) {
                        System.out.println("windowsClosing");
                        System.exit(0);
                    }
                    @Override
                    public void windowActivated(WindowEvent e) {
                        WindowF source = (WindowF) e.getSource();
                        source.setTitle("已激活");
                        System.out.println("windowActivated");
                    }
                });
    }
           /* @Override
            public void windowClosing(WindowEvent e) {
                setVisible(false);// 隐藏窗口
                System.exit(0);//正常退出   1是非正常退出
            };*/
}
```

## 2.10 键盘监听

```java
package com.shuai.lesson3;
import java.awt.*;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.sql.SQLOutput;
//键
public class TestKeyListener {
    public static void main(String[] args) {
        new KeyF();
    }
}
class KeyF extends Frame{
    public KeyF(){
        setBounds(0,0,300,400);
        setVisible(true);
        this.addKeyListener(new KeyAdapter() {
           //键盘按下
           @Override
            public void keyPressed(KeyEvent e) {
               int keyCode = e.getKeyCode();//不需要去记录这个数值，直接使用静态属性VK_xxx
               System.out.println(keyCode);
               if (keyCode == KeyEvent.VK_UP){
                   System.out.println("你按了上键盘");
                   //根据不同的操作，进行不同的结果
               }
           }
        });
    }
}
```

# 3、Swing（做界面）

## 3.1、窗口、面板JFrame

```java
package com.shuai.lesson4;
import javax.swing.*;
import java.awt.*;
public class JFrameDemo {
    //init();初始化
    public void init(){
        //顶级窗口
        JFrame jf = new JFrame("这是一个JFrame窗口");
        jf.setBounds(100,100,400,300);
        //设置文字Label->JLabel
        jf.setBackground(Color.BLUE);
        JLabel jl = new JLabel("JJJJJ");
        jf.add(jl);
        //让文本标签居中
        jl.setHorizontalAlignment(SwingConstants.CENTER);
        //容器实例化
        jf.getContentPane().setBackground(Color.red);
        jf.setVisible(true);
        //关闭事件
        jf.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
    }
    public static void main(String[] args) {
        //建立一个窗口
        new JFrameDemo().init();
    }
}
```

## 3.2、弹窗

```java
package com.shuai.lesson4;
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
public class DialogDemo extends JFrame {
    public DialogDemo() {
        this.setVisible(true);
        this.setBounds(100, 100, 400, 400);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        //Jframe 放东西，容器
        Container contentPane = this.getContentPane();
        //绝对布局
        contentPane.setLayout(null);
        //设置背景
        contentPane.setBackground(Color.BLUE);
        //按钮
        JButton jButton = new JButton("点击弹出一个对话框");
        jButton.setBounds(30, 30, 200, 50);
        //点击按钮弹出弹框
        jButton.addActionListener(new ActionListener() {//监听器
            @Override
            public void actionPerformed(ActionEvent e) {
                //弹窗
                new MyDialog();
            }
        });
        contentPane.add(jButton);
    }
    public static void main(String[] args) {
        new DialogDemo();
    }
}
//弹窗的窗口
class MyDialog extends JDialog {
    public MyDialog() {
        this.setVisible(true);
        this.setBounds(100, 100, 500, 500);
       // this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);   
        this.setDefaultCloseOperation(WindowConstants.DISPOSE_ON_CLOSE);
        //JDialog退出只能是D0_ONTHING,HIDE,DISPOSE这三个中的一种
        //应该是默认就有关闭事件
        this.setTitle("这是一个弹窗");
        Container contentPane = this.getContentPane();
        contentPane.setLayout(null);
        contentPane.setBackground(Color.ORANGE);
        JLabel jjj = new JLabel("学习学习");
        contentPane.add(jjj);
        jjj.setBounds(20,20,50,50);
    }
}
```

## 3.3、标签

JLabel

```java
package com.yuwei.lesson04;

import javax.swing.*;
import java.awt.*;

public class JFrameDemo1 {
    public static void main(String[] args) {
        new JFrameDemo1().init();
    }

    public void init(){
        JFrame jFrame = new JFrame("夏天");
        jFrame.setBounds(100,100,500,500);
        jFrame.setVisible(true);
        JLabel label = new JLabel("夏天夏天");
        jFrame.add(label);
        Container contentPane = jFrame.getContentPane();
        contentPane.setBackground(Color.blue);
        jFrame.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
    }
}
```

图标Icon

```java
package com.shuai.lesson4;
import javax.swing.*;
import java.awt.*;
//图标，需要实现类，Frame继承
public class IconDemo extends JFrame implements Icon {
    private  int width;
    private  int hight;
    public IconDemo(){};//无参构造
    //有参构造
    public IconDemo(int width,int hight){
        this.width = width;
        this.hight = hight;
    };
    public void init(){
        IconDemo iconDemo = new IconDemo(15, 15);
        //图标可以放在标签，也可以放在按钮上！
        JLabel jLabel = new JLabel("标签",iconDemo,SwingConstants.CENTER);
        Container contentPane = getContentPane();
        contentPane.add(jLabel);
        this.setVisible(true);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
    }
    @Override
    public void paintIcon(Component c, Graphics g, int x, int y) {
        g.fillOval(x,y,width,hight);
    }
    @Override
    public int getIconWidth() {
        return this.width;
    }
    @Override
    public int getIconHeight() {
        return this.hight;
    }
    public static void main(String[] args) {
        new IconDemo().init();
    }
}
```

图片

```java
package com.shuai.lesson4;
import javax.swing.*;
import java.awt.*;
import java.net.URL;
public class ImageIconDemo extends JFrame {
    public ImageIconDemo(){
        JLabel jLabel = new JLabel("图片");
        URL resource = ImageIconDemo.class.getResource("4.png");
        ImageIcon imageIcon = new ImageIcon(resource);
        jLabel.setIcon(imageIcon);
        jLabel.setHorizontalAlignment(SwingConstants.CENTER);
        Container contentPane = getContentPane();
        contentPane.add(jLabel);
        this.setVisible(true);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        setBounds(100,100,800,800);
    }
    public static void main(String[] args) {
        new ImageIconDemo();
    }
}
```

## 3.4、面板

JPanel

```java
package com.shuai.lesson5;
import javax.swing.*;
import java.awt.*;
public class JPanelDemo extends JFrame {
    public  JPanelDemo(){
        Container contentPane = this.getContentPane();
        contentPane.setLayout(new GridLayout(2,1,10,10));//后边两个是间距
        JPanel jPanel = new JPanel(new GridLayout(1, 3));
        JPanel jPane2 = new JPanel(new GridLayout(1, 2));
        JPanel jPane3 = new JPanel(new GridLayout(1, 1));
        jPanel.add(new JButton("aaa"));
        jPanel.add(new JButton("bbb"));
        jPanel.add(new JButton("ccc"));
        jPane2.add(new JButton("111"));
        jPane2.add(new JButton("222"));
        jPane3.add(new JButton("---"));
        setBounds(100,100,500,400);
        setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        contentPane.add(jPanel);
        contentPane.add(jPane2);
        contentPane.add(jPane3);
        setVisible(true);
        contentPane.setBackground(Color.YELLOW);
    }
    public static void main(String[] args) {
        new JPanelDemo();
    }
}
```

JScrollPanel

```java
package com.shuai.lesson4;
import javax.swing.*;
import java.awt.*;
public class JScrollPanelDemo extends JFrame {
    public JScrollPanelDemo(){
        Container contentPane = this.getContentPane();
        //文本域
        JTextArea jTextArea = new JTextArea(20, 50);
        jTextArea.setText("学习学习");
        //面板  并添加到contentpane
        contentPane.add(new JScrollPane(jTextArea));
        this.setVisible(true);
        this.setBounds(100,100,400,300);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        contentPane.setBackground(Color.BLUE);
    }
    public static void main(String[] args) {
        new JScrollPanelDemo();
    }
}
```

## 3.5、按钮

图片按钮

```java
package com.shuai.lesson5;
import javax.swing.*;
import java.awt.*;
import java.net.URL;
public class JButtonDemo01 extends JFrame {
    public JButtonDemo01(){
        Container contentPane = this.getContentPane();
        //图片变为图标
        URL resource = JButtonDemo01.class.getResource("4.png");
        Icon icon = new ImageIcon(resource);
        JButton jButton = new JButton();
        jButton.setIcon(icon);
        //悬浮框
        jButton.setToolTipText("这是一个图片按钮");
        contentPane.add(jButton);
        this.setVisible(true);
        this.setBounds(100,100,400,300);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
    }
    public static void main(String[] args) {
        new JButtonDemo01();
    }
}
```

**单选按钮**

```java
package com.shuai.lesson5;
import javax.swing.*;
import java.awt.*;
import java.net.URL;
public class JButtonDemo02 extends JFrame {
    public JButtonDemo02(){
        Container contentPane = this.getContentPane();
        //图片变为图标
        URL resource = JButtonDemo01.class.getResource("4.png");
        Icon icon = new ImageIcon(resource);
       //单选框
        JRadioButton jrb01 = new JRadioButton("jrb01");
        JRadioButton jrb02 = new JRadioButton("jrb02");
        JRadioButton jrb03 = new JRadioButton("jrb03");
        //由于单选框只能选择一个，分组
        ButtonGroup buttonGroup = new ButtonGroup();
        buttonGroup.add(jrb01);
        buttonGroup.add(jrb02);
        buttonGroup.add(jrb03);
        contentPane.add(jrb01,BorderLayout.CENTER);
        contentPane.add(jrb02,BorderLayout.NORTH);
        contentPane.add(jrb03,BorderLayout.SOUTH);
        this.setVisible(true);
        this.setBounds(100,100,400,300);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
    }
    public static void main(String[] args) {
        new JButtonDemo02();
    }
}
```

**复选按钮**

```java
package com.shuai.lesson5;
import javax.swing.*;
import java.awt.*;
import java.net.URL;
public class JButtonDemo03 extends JFrame {
    public JButtonDemo03(){
        Container contentPane = this.getContentPane();
        //图片变为图标
        URL resource = JButtonDemo01.class.getResource("4.png");
        Icon icon = new ImageIcon(resource);
        //多选框
        JCheckBox jcb1 = new JCheckBox("jcb1");
        JCheckBox jcb2 = new JCheckBox("jcb2");
        JCheckBox jcb3 = new JCheckBox("jcb3");
        //流式布局
        contentPane.setLayout(new FlowLayout(FlowLayout.LEFT));
        contentPane.add(jcb1);
        contentPane.add(jcb2);
        contentPane.add(jcb3);
        //东西南北中布局
        /*
         contentPane.add(jcb1,BorderLayout.NORTH);
        contentPane.add(jcb2,BorderLayout.CENTER);
        contentPane.add(jcb3,BorderLayout.SOUTH);
        */
        this.setVisible(true);
        this.setBounds(100,100,400,300);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
    }
    public static void main(String[] args) {
        new JButtonDemo03();
    }
}
```

## 3.6、列表

下拉框

```java
package com.shuai.lesson6;
import javax.swing.*;
import java.awt.*;
public class TestComboboxDemo01 extends JFrame {
    public TestComboboxDemo01(){
        Container container = this.getContentPane();
        JComboBox status = new JComboBox();
        status.addItem("未上映");
        status.addItem("正在热映");
        status.addItem("已下架");
        container.add(status);
        this.setVisible(true);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        this.setBounds(100,100,500,400);
    }
    public static void main(String[] args) {
        new TestComboboxDemo01();
    }
}
```

列表框

```java
package com.shuai.lesson6;
import javax.swing.*;
import java.awt.*;
import java.util.Vector;
public class TestComboboxDemo02 extends JFrame {
    public TestComboboxDemo02(){
        Container container = this.getContentPane();
        //生成列表的内容
       // String[] contents = {"1","2","3"};
        //列表中需要的内容
        Vector contents = new Vector();
        JList jList = new JList(contents);
        JList jList1 = new JList(contents);
        contents.add("2222");
        contents.add("333");
        container.add(jList);
        this.setVisible(true);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        this.setBounds(100,100,500,400);
    }
    public static void main(String[] args) {
        new TestComboboxDemo02();
    }
}
```

**应用场景**

**选择地区，或者一些单个选项**

**列表，展示信息，一般是动态扩展**

## 3.7、文本框

**文本框**

```java
package com.shuai.lesson6;
import javax.swing.*;
import java.awt.*;
public class TestTextDemo01 extends JFrame {
    public TestTextDemo01(){
        Container container = this.getContentPane();
        //不布局只会出现WORLD，且位置不对
        this.setLayout(new FlowLayout(FlowLayout.RIGHT));
        JTextField jTextField1 = new JTextField("HELLO");
        JTextField jTextField2 = new JTextField("WORLD",20);
        container.add(jTextField1);
        container.add(jTextField2);
        this.setVisible(true);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        this.setBounds(100,100,400,300);
    }
    public static void main(String[] args) {
        new TestTextDemo01();
    }
}
```

**密码框**

```java
package com.shuai.lesson6;
import javax.swing.*;
import java.awt.*;
public class TestTextDemo02 extends JFrame {
    public TestTextDemo02(){
        Container container = this.getContentPane();
        JPasswordField jPasswordField = new JPasswordField();//---
        jPasswordField.setEchoChar('-');
        container.add(jPasswordField);
        this.setVisible(true);
        this.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        this.setBounds(100,100,400,300);
    }
    public static void main(String[] args) {
        new TestTextDemo02();
    }
}
```

**文本域**

```java
//文本域
JTextArea jTextArea = new JTextArea(20, 50);
jTextArea.setText("学习学习");
//面板  并添加到contentpane
contentPane.add(new JScrollPane(jTextArea));	
```

# 4、贪吃蛇

1. 首先创建一个框架类，再创建一个面板类和一个数据类，将面板和一些页面添加到框架上。
2. 绘制一个静态的小蛇，默认长度为3，默认方向向右。
3. 让小蛇动起来，需要用到定时器类Timer，蛇头根据方向设置x y坐标，身体则由后一节赋给前一节，这样可以为后面打基础。
4. 通过键盘来控制小蛇移动。
5. 给小蛇设置穿墙术，并且给小蛇添加食物。
6. 给小蛇设置成不可以咬自己尾巴，并且增加一个显示积分和长度面板，可以直接画出来。

## 4.1、GamePanel游戏面板和功能实现类

```java
package com.yuwei.snake;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.util.Random;

//游戏面板
public class GamePanel extends JPanel implements KeyListener, ActionListener {

    //定义蛇的数据结构
    int length;//蛇的长度
    int[] snakeX = new int[600];//蛇的x坐标
    int[] snakeY = new int[500];//蛇的y坐标
    boolean isStart;//游戏状态，默认为不开始游戏
    String str;//蛇的放下
    int score;//分数
    int foodX;//食物的x坐标
    int foodY;//食物的y坐标
    Random random = new Random();//随机数，用来生成食物
    boolean isFail;//用来判断是否失败，默认为成功
    Timer timer = new Timer(100,this);//定时器  100毫秒执行一次

    public GamePanel() {
        init();
        this.setFocusable(true);//获取焦点事件
        this.addKeyListener(this);//获取监听事件
        timer.start();//定时器开始
    }

    //初始化
    public void init(){
        length = 3;//初始化蛇的长度
        snakeX[0] = 100; snakeY[0] = 100;//初始化蛇头坐标
        snakeX[1] = 75; snakeY[1] = 100;//初始化蛇的第一节身体坐标
        snakeX[2] = 50; snakeY[2] = 100;//初始化蛇的第二节身体坐标
        str = "right";//初始化开始蛇的方向
        foodX = 25 + 25*random.nextInt(34);//初始化食物的x坐标
        foodY = 75 + 25*random.nextInt(24);//初始化食物的y坐标
        isStart  = false;//初始化开始游戏标志
        isFail = false;//初始化游戏失败标志
        score = 0;//初始化分数

    }


    //绘制面板,我们游戏中所有的东西，都是用这个画笔来画
    @Override
    protected void paintComponent(Graphics g) {
        //清屏
        super.paintComponent(g);

        //设置面板背景颜色
        this.setBackground(Color.white);

        //绘制头部广告栏
        DateCenter.header.paintIcon(this,g,25,11);

        //绘制游戏界面
        g.fillRect(25,75,850,600);

        //绘制游戏积分和蛇的长度
        g.setColor(Color.black);
        g.setFont(new Font("微软雅黑",Font.BOLD,18));
        g.drawString("分数:" + score,750,35);
        g.drawString("长度:" + length,750,55);

        //画食物
        DateCenter.food.paintIcon(this,g,foodX,foodY);

        //绘制静态小蛇的蛇头方向
        if (str.equals("right")){//蛇头向右
            DateCenter.right.paintIcon(this,g,snakeX[0],snakeY[0]);
        }else if (str.equals("left")){//蛇头向左
            DateCenter.left.paintIcon(this,g,snakeX[0],snakeY[0]);
        }else if (str.equals("up")){//蛇头向上
            DateCenter.up.paintIcon(this,g,snakeX[0],snakeY[0]);
        }else if (str.equals("down")){//蛇头向下
            DateCenter.down.paintIcon(this,g,snakeX[0],snakeY[0]);
        }
        for (int i = 1; i < length; i++) {//蛇的身体
            DateCenter.body.paintIcon(this,g,snakeX[i],snakeY[i]);
        }

        //游戏开始提示
        if(!isStart){
            g.setColor(Color.white);
            g.setFont(new Font("微软雅黑",Font.BOLD,40));
            g.drawString("按下空格开始游戏！",300,300);
        }

        //游戏失败提示
        if(isFail == true){
            g.setColor(Color.red);
            g.setFont(new Font("微软雅黑",Font.BOLD,20));
            g.drawString("你咬到自己尾巴了，游戏失败，按下空格重新开始！",300,300);
        }
    }


    //键盘监听事件
    @Override
    public void keyPressed(KeyEvent e) {
        int keyCode = e.getKeyCode();//读取你按下的键盘
        //如果按下的是空格，则表示开始或结束
        if(keyCode == KeyEvent.VK_SPACE){
            if(!isFail) {
                isStart = !isStart;
            }else {
                init();
            }
            repaint();
        }
        //小蛇移动
        if(keyCode == KeyEvent.VK_UP){
            str = "up";
            repaint();
        }else if(keyCode == KeyEvent.VK_DOWN){
            str = "down";
            repaint();
        }else if(keyCode == KeyEvent.VK_LEFT){
            str = "left";
            repaint();
        }else if(keyCode == KeyEvent.VK_RIGHT){
            str = "right";
            repaint();
        }
    }
    //事件监听----需要通过固定事件来刷新，1s 10次
    @Override
    public void actionPerformed(ActionEvent e) {
        if(isStart && isFail == false){//如果游戏为开始状态,并且不为失败状态，则让小蛇动起来
            //吃食物
            if(snakeX[0] == foodX && snakeY[0] == foodY){
                length++;
                score += 10;
                foodX = 25 + 25*random.nextInt(34);
                foodY = 75 + 25*random.nextInt(24);
            }

            //小蛇身体移动，后一节变成前一节即可
            for (int i = length - 1; i > 0; i--) {
                snakeX[i] = snakeX[i-1];
                snakeY[i] = snakeY[i-1];
            }

            //头向右移动
            if(str.equals("right")){
                snakeX[0] = snakeX[0] + 25;
                //边界判断
                if(snakeX[0] > 850){
                    snakeX[0] = 25;
                }
            }else if(str.equals("left")){//头向左移动
                snakeX[0] = snakeX[0] - 25;
                //边界判断
                if(snakeX[0] < 25){
                    snakeX[0] = 850;
                }
            }else if(str.equals("up")){//头向上移动
                snakeY[0] = snakeY[0] - 25;
                //边界判断
                if(snakeY[0] < 75){
                    snakeY[0] = 650;
                }
            }else if(str.equals("down")){//头向下移动
                snakeY[0] = snakeY[0] + 25;
                //边界判断
                if(snakeY[0] > 650){
                    snakeY[0] = 75;
                }
            }
            
            //判断蛇头是否咬到了蛇身体
            for (int i = 1; i < length; i++) {
                if(snakeX[0] == snakeX[i] && snakeY[0] == snakeY[i]){
                    isFail = true;
                }
            }
            
            //重新绘制页面
            repaint();
        }
        timer.start();
    }
    @Override
    public void keyTyped(KeyEvent e) {}
    @Override
    public void keyReleased(KeyEvent e) {}
}

```

## 4.2、StartGame游戏运行开始类

```java
package com.yuwei.snake;

import javax.swing.*;
//开始游戏
public class StartGame {
    public static void main(String[] args) {
        //创建一个主框架
        JFrame jFrame = new JFrame("夏天用java做的贪吃蛇");

        //初始化主框架
        jFrame.setBounds(10,10,900,720);
        jFrame.setResizable(false);
        jFrame.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);

        //将游戏面板加入到主框架中
        jFrame.add(new GamePanel());

        //将框架设置为可见
        jFrame.setVisible(true);
    }
}

```



## 4.3、DataCenter存放数据的类

```java
package com.yuwei.snake;

import javax.swing.*;
import java.net.URL;

//数据中心，存放素材
public class DateCenter {
    //存放广告图片
    public static URL headerURL = DateCenter.class.getResource("statics/header.png");
    public static ImageIcon header = new ImageIcon(headerURL);

    //存放蛇身体图片
    public static URL bodyURL = DateCenter.class.getResource("statics/body.png");
    public static ImageIcon body = new ImageIcon(bodyURL);

    //存放食物图片
    public static URL foodURL = DateCenter.class.getResource("statics/food.png");
    public static ImageIcon food = new ImageIcon(foodURL);

    //存放蛇头向左图片
    public static URL leftURL = DateCenter.class.getResource("statics/left.png");
    public static ImageIcon left = new ImageIcon(leftURL);

    //存放蛇头向右图片
    public static URL rightURL = DateCenter.class.getResource("statics/right.png");
    public static ImageIcon right = new ImageIcon(rightURL);

    //存放蛇头向上图片
    public static URL upURL = DateCenter.class.getResource("statics/up.png");
    public static ImageIcon up = new ImageIcon(upURL);

    //存放蛇头向下图片
    public static URL downURL = DateCenter.class.getResource("statics/down.png");
    public static ImageIcon down = new ImageIcon(downURL);
}

```