# Tkinter 笔记

GUI是能否做出产品的关键，再简单的产品，运用了GUI就会显得具有较高的完成度。关于tkinter， 网络上比较推荐的教材是John E. Greyson所著的《Python and Tkinter Programming》(PTP)，这本书版本比较旧了，所以主要按照它的结构来学习，具体的细节仍然参考了官方文档。



# 1 Tkinter控件

介绍Tkinter模块和它与Tcl/Tk的关系。



## 1.1 Toplevel窗口类

Toplevel控件为其他控件提供独立的容器，如框架。PTP上把root主窗口和toplevel窗口统称为Toplevel对象了。有的教材上会对两者加以区别，Tk()创建的称为主窗口，是唯一的。Toplevel创建的称为Toplevel窗口，是可以分层次的。简单来讲，对于单窗口的应用程序，当你初始化Tk时创建的对象root，就可以为你提供所需的一切。共有四种toplevel。

### root主窗口

就是主程序的窗口，关闭这个窗口，程序终止。使用root = Tk()初始化tk时，就会自动生成root。

### child子窗口

以root为master创建Toplevel对象，即创建了root的子窗口。事实上，任意一级窗口都可以作为master创建子窗口。子窗口相对独立于其所依赖的master主窗口存在，当主窗口销毁时，child窗口才销毁。<u>子窗口只吃关闭。</u>

### transient暂态窗口

创建方法与子窗口相同，创建好子窗口后，调用handler.transient(root)方法，将子窗口设置为暂态窗口。暂态窗口与主窗口保持高度同步，它本身没有最小化按钮，随主窗口最小化、恢复或关闭。<u>暂态窗口吃一切。</u>

### undecorated无边框窗口

创建方法与子窗口相同，创建好子窗口后，调用handler.overrideredirect(True)方法，将子窗口设置为无边框窗口。<u>无框窗口同子窗口，只吃关闭。</u>

另外，只有toplevel对象可以调用geometry方法，设置窗口大小。

	
	from tkinter import *
	
	root = Tk()
	#root.option_readfile('option_DB')
	root.title('Toplevel')
	Label(root, text='This is the main (default) Toplevel').pack(pady=10)
	
	t1 = Toplevel(root)
	Label(t1, text='This is a child of root').pack(padx=10, pady=10)
	
	t2 = Toplevel(root)
	Label(t2, text='This is a transient window of root').pack(padx=10, pady=10)
	t2.transient(root)
	
	t3 = Toplevel(t1, borderwidth=5, bg='blue')
	Label(t3, text='No wm decorations', bg='blue', fg='white').pack(padx=10, pady=10)
	t3.overrideredirect(True)
	t3.geometry('200x70+150+150')
	
	root.mainloop()



## 1.2 Frame框架

Frame为toplevel窗口中的其他控件提供区域容器。frame除了标准的控件选项之外，几乎没有其他方法可用。frame最常用的功能，就是将一组其他控件打包，并用几何管理工具一并处理。

初始化frame实例时，主要传入master和属性字。master就是所在toplevel的句柄。常用的属性字主要有2个。

一是浮雕效果（relief=‘xxx’），共有六种：平面（flat）、凸面（raised）、凹面（sunken）、凸框（ridge）、凹框（groove）、实线框（solid）。

二是框线宽度（borderwidth=x）。x是像素数，一定要设，否则frame什么都不显示（与flat效果相同）。
	
    import tkinter as tk
    
    root = tk.Tk()
    root.title('Frame Example')
    
    frm = tk.Frame(root, relief='solid', borderwidth=1, width=300, height=150)
    
    frm1 = tk.Frame(frm, relief='groove', borderwidth=2)
    lb1 = tk.Label(frm1, text='You shot him!').pack(pady=10)
    btn1 = tk.Button(frm1, text="He's dead!").pack(side='left', padx=5, pady=8)
    btn2 = tk.Button(frm1, text="He's completely dead!").pack(side='right', padx=5, pady=8)
    frm1.place(anchor='nw', relx=0.01, rely=0.1)
    
    lb2 = tk.Label(frm, text='Self-defence against fruit', bg='blue')
    lb2.place(anchor='w', relx=0.05, rely=0.125)
    frm.pack()
    
    root.mainloop()

这里初步接触了一下几何方法place，可以说这个小例子完全是关于place的用法的，关于几何管理的内容我们等学习后面的内容时再接触。

第一层框架是frm， 它的master是root，它的大小是定值宽300像素，高150像素。实际上可以只设置这两个属性，这样frm框架便不会显示出来。为了明显起见，这里将框线设为实线了。

第一层框架下有一个控件（label控件lb2）和一个框架（frm1）。

### 初识placer

关于place的用法，重点在于理解anchor的用法。在一个大矩形中准确地定义一个小矩形（有面积，不是点）的位置，需要知道三个信息：一是坐标系定义，二是坐标数据，三是要在小矩形上指定一个定位点。
原点和坐标系完全是系统默认的定义方式，即原点在master控件的左上角，向右向下为正。
坐标数据以相对形式给出，取0到1之间的浮点数。如果取0，则参考点横坐标为0（在最左侧），如果取1，则参考点横坐标为master控件的最右侧，纵坐标也是如此。

![anchor说明nw](E:\学习研究\1 编程资料\其他资料\anchor illustration\anchor说明nw.jpg)

如图，定义位置时，以master控件的左上角为原点，以给定的相对坐标（这里都是0.5）指定位置。每个控件都有9个anchor，选一个作为“把手”，将这个“把手”安放在指定位置即可。anchor='nw’的含义是“将控件的左上角安放在指定位置”。再如：

![anchor说明n](E:\学习研究\1 编程资料\其他资料\anchor illustration\anchor说明n.jpg)



anchor='n’的含义是“将控件的上边中点安放在指定位置”。其他的anchor同理。这也就是为什么正规的python代码中，默认先设置指定坐标，最后再选择anchor。



## 1.3 Label标签

Label控件可以用于显示文本或图像。Labels可以包含多行文本，但是只能用一种字体。对于文本，可以指定每行的宽度，或者在文本中适当的位置插入换行符。

例子中的Label使用wraplength=300来指定文本换行宽度，用justify=‘left’指定文本靠左对齐。Label的relief选项用法同frame。

### 在Label中显示图片

例子中涉及bitmap属性，作用是把图像映射到label中。这时，又遇到了老生常谈的问题，如何用相对路径访问文件。应该是很简单，但是我怎么一次也没成功过。用open测试了一下，子目录内的文件用“data/dataset1.csv”即可访问。这里bitmap不好用可能是文件本身有问题（我用的是windows自带的bmp图像）。
	
    
    # 给出文件名和浮雕样式的数组
    images = [('image1', 'sunken'), ('image2', 'sunken')]
    frm2 = tk.Frame(root)
    
    # 从文件新建PhotoImage对象，并将其运用到Label中，共2幅图。
    img1 = tk.PhotoImage(file='images/%s.png' % images[0][0])
    tk.Label(frm2, image=img1, relief='%s' % images[0][1]).pack(side='left', padx=5)
    img2 = tk.PhotoImage(file='images/%s.png' % images[1][0])
    tk.Label(frm2, image=img2, relief='%s' % images[1][1]).pack(side='right', padx=5)
    frm2.pack()

根据网上的示例，用了PhotoImage类来生成图形对象img1和img2。将它们用在Label的image选项中，即可正常显示。

也尝试了BitmapImage方法，总是提示“_tkinter.TclError: format error in bitmap data”，也就是BitmapImage方法的输入文件格式错误。从tkinter文档中看到，BitmapImage类可以从字符串或文本文件中读取X11标准的bitmap，也就是说，给定的文件必须是数据文件，不能随随便便放个什么图片。这样就不如直接用photoimage方便了。

## 1.4 Buttons按钮

严格地讲，按钮就是能够响应鼠标或键盘事件的标签。你可以为按钮绑定call或callback方法，当按钮被激活时调用对应的方法。按钮状态可以被设置为disabled，之后它就不会被用户激活了。按钮控件可以包含文本或图像，文本可以扩展多行。按钮也可以被纳入tab group，这样就可以用TAB键来循环遍历它们。

不是所有的GUI编程者都知道按钮也可以设定浮雕效果。特殊地，浮雕flat和solid可以提供类似于工具栏的效果，而图标icons可以用来提供功能信息。但是运用某些浮雕效果时应当特别注意。例如，如果将一个按钮设置为sunken浮雕，那么激活它时不会有什么外观上的变化。如果是这样，就要采取别的措施来描述“激活”，例如改变背景色、字体或者颜色。

PTP书中举的例子是利用两个序列（边框线宽序列和浮雕效果序列），产生一个按钮阵列，并且点击任何一个按钮可以打印出按钮所用的浮雕和线宽。代码如下：
	
	import tkinter as tk
	
	
	# 将整个GUI封装为一个类
	class GUI:
	    # 在初始化函数中就把需要的控件构建好，留出GUI所在的主窗口或toplevel窗口接口
	    def __init__(self, master=None):
	        # 生成一个None队列，None可以随后被替换为任意实例，这里是frame对象：5层frame。
	        frms = [None]*5
	        # 构建5层frame，每层frame内部依次排列label和6种relief的按钮。
	        for n in range(5):
	            # 构建一层frame
	            frms[n] = tk.Frame(master)
	            # 构建一个标签
	            lb = tk.Label(frms[n], text="bd = %d" % n)
	            lb.pack(side='left')
	            # 构建6个按钮
	            for rlf in ['flat', 'raised', 'sunken', 'ridge', 'groove', 'solid']:
	                # lambda函数的妙用**
	                btn = tk.Button(frms[n], text=rlf, relief=rlf, borderwidth=n,
	                                command=lambda s=self, r=rlf, b=n: s.showborder(r, b),
	                                width=10)
	                btn.pack(side='left', padx=10-n, pady=5-n)
	            frms[n].pack()
	
	    # 方法：显示浮雕效果和边框宽度
	    def showborder(self, rlf, bw):
	        print('%s:%d' % (rlf, bw))
	
	
	# 创建主窗口实例
	root = tk.Tk()
	# 将GUI应用到主窗口
	app = GUI(root)
	# 主循环
	root.mainloop()


值得一提的是lambda函数的使用。观察可知，这是在初始化函数中，调用类本身的方法。实际上，如果不是为了格式上的一致，也可以写为：
	

	lambda r=rlf, b=n: self.showborder(r, b)

为按钮设定激活函数的一般方法是就是command=XXX，其中XXX是函数名，直接使用，不加引号。

## 1.5 Entry文本框

文本框控件是用于收集用户输入的基本控件。它们也可以用于显示信息，或者干脆设为disabled不再响应用户交互。文本框控件中的文本，被限制为单行，而且只能使用同一种字体。如果输入的文本过长，文本框会向后卷动，保持光标始终在显示区域。可以用方向键改变文本框的显示位置。也可以调用文本框的scrolling方法，为其绑定鼠标事件或app上的其他操作。

另外，文本框的width选项，接受的数值不再是像素，而是字符数。

### 文本变量StringVar

每个文本框都需要指定一个变量，用来存储、显示其内容。
	
	e = tk.StringVar()  # 创建StringVar字符串变量
	ety = tk.Entry(root, width=30, textvariable=e)
	ety.pack(side='left', padx=10)
	e.set('This is an entry!')

1.字符串变量是用类tk.StringVar构建。

2.使用Entry的textvariable选项，与Entry对象关联。

3.如果要在脚本中设置其内容，应当调用其set方法。

4.一旦关联，Entry中的输入操作也可以改变其值。

用得还是不太熟练，经查，StringVar是Tcl下的对象，类似的还有StringVar、BooleanVar、DoubleVar、IntVar。StringVar.set接受的是tuple，如果给出的是字符串，也是转化成tuple。由于Tuple对象值不能改变，所以可以先定义一个list，再将list转化为tuple，再使用set（）方法。

其他方法：

StringVar.get()：获取StringVar的值，是个ASCI或Unicode字符串。另外，BooleanVar.get()结果是0或1，DoubleVar.get()获取的是python的浮点类型数据；IntVar.get()获取的是python整型数据。

StringVar.trace(mode， callback)：

比如StringVar.trace('w',callback)是当StringVar



## 1.6 RadioButton单选按钮

单选按钮需要重新命名了，现在的汽车上，已经很少有机械式的按钮了。言归正传，单选按钮的目的是提供互斥的选项，选择一个按钮会取消对其他按钮的选择。

类似于按钮控件，单选按钮可以显示文本或者图像，并且文本也可以扩展到多行，字体当然也必须是统一的。单选按钮通常成组使用，对应于同一个变量。

一组RadioButton的功能实际上是输出一个值。而一个RadioButton选项有：root，text，value，variable。其中，前三个都是单独安排的，variable才是对应的那个Var对象（IntVar、BooleanVar等），只有一个。因为单选指的就是选择这个值。点选某个选项的含义就是将此选项的value赋值给公用的variable。所以使用时应注意两者类型应当相同。书中所用的例子是整型数值，variable用的是IntVar类型。下面的例子中，value和variable用的都是字符串，也是可以的。


	class GUI:
	    def __init__(self, master):
			# 构建标签
	        lb = tk.Label(master, text='Select your favorite language:')
	        lb.pack()
			
			# 创建字符串变量
	        self.svar = tk.StringVar()

			# 生成系列RadioButton
	        for txt, val in [('python', 'python'), ('C++', 'C++'), ('Ruby', 'Ruby')]:
	            rb = tk.Radiobutton(master, text=txt, value=val, variable=self.svar)
	            rb.pack(padx=5, pady=5)
	        self.svar.set('C++')
	        btn = tk.Button(master, text='OK', command=self.printoption)
	        btn.pack(pady=5)
	
	    def printoption(self):
	        print('Your favorite language is ' + self.svar.get())

功能：选择了一个选项以后，点击ok，即可打印出选项内容来。

Radiobutton还有一种形式，就是将选项indicatoron置为1，外观即变成按键形式。

## 1.7 Checkbutton复选按钮

Checkbutton控件用于为多个选项提供开关选择。不像radiobutton控件，选项之间并没有交互关系。可以为checkbutton加载文本或者图片。Checkbutton通常用一个IntVar分配到variable选项，来确定其状态。另外，也可以为Checkbutton分配callback函数，每当按钮被按下时，调用此函数。

	class GUI:
	    def __init__(self, master):
	        # 两个Checkbutton，各有自己的Var对象，这里用一个list存两个Var。
	        self.sv = []
	        self.sv.append(tk.StringVar())
	        self.sv.append(tk.StringVar())
	
	        # 分别设置两个Checkbutton的on值和off值，并且与Var对象做关联
	        cb1 = tk.Checkbutton(master, text='checkbutton1',
	                             onvalue='cb1 on',
	                             offvalue='cb1 off',
	                             variable=self.sv[0])
	        cb1.pack()
	        cb2 = tk.Checkbutton(master, text='checkbutton2',
	                             onvalue='cb2 on',
	                             offvalue='cb2 off',
	                             variable=self.sv[1])
	        cb2.pack()
	        # 初始化Var对象
	        self.sv[0].set('cb1 start')
	        self.sv[1].set('cb2 start')
	        # 搞一个按钮来输出Var对象的值
	        btn = tk.Button(master, text='ok',
	                        width=30,
	                        command=self.sitrep)
	        btn.pack()
	
	    def sitrep(self):
	        for i in self.sv:
	            print(i.get())
	        print('\n')

Checkbutton和Radiobutton的最大区别在于，前者的选择不是互斥的，每个对象的状态对其他对象没有影响。因此，每一个对象都必须有一个独立的Var对象作为后台。依靠选择on或off，改变这个Var的值。实际上是为on和off的两种状态选择值，再将此值传入Var对象，所以类型上要注意。看上去用IntVar或BooleanVar就足够了，但是这里还是用了StringVar。



## 1.8 Menu菜单

菜单控件为用户提供了熟悉的操作方式，用以访问应用程序的各种功能。菜单构建起来比较麻烦，特别是当悬挂层次多的时候更是如此。所以不论要访问何种功能，菜单层次不要超过3层。关于Menu的运用，PTP教材的确已经过时了，从tkinter官方文档中可知，

1. 菜单的核心是Menu类，基于toplevel或root创建Menu对象，即可得到菜单栏，如：menubar = tk.Menu(root)。而基于上一级Menu对象，创建Menu对象，即可得到具有层次关系的多级菜单。

2. 调用Menu对象的add_command方法，可以为本级菜单添加选项。

3. 调用Menu对象的add_cascade方法，可以在本级菜单中添加次级菜单的入口（设置级联关系）
4. 完成了整个菜单的构造之后，要将菜单所在窗口的menu选项中，设置菜单栏的句柄。
        
        import tkinter as tk
        root = tk.Tk()
        def optionclicked():
        print('option clicked!')
        
        # 基于root（或者toplevel对象）建立菜单栏对象
        mbar = tk.Menu(root)
        # 基于菜单栏对象，建立一级菜单
        menu_1 = tk.Menu(mbar, tearoff=0)
        menu_2 = tk.Menu(mbar, tearoff=0)
        menu_3 = tk.Menu(mbar, tearoff=0)
        # 基于一级菜单，建立次级子菜单，这里准备将子菜单入口安排到第二个菜单的第一项。
        menu_2_1 = tk.Menu(menu_2, tearoff=0)
        
        # 开始设置级联关系，第一级菜单是菜单栏的级联，用选项menu设定对应的菜单对象
        mbar.add_cascade(label='M1', menu=menu_1)
        mbar.add_cascade(label='M2', menu=menu_2)
        mbar.add_cascade(label='M3', menu=menu_3)
        
        # 为菜单添加选项，设定命令响应函数
        menu_1.add_command(label='option 1-1', command=optionclicked())
        menu_1.add_command(label='option 1-2', command=optionclicked())
        menu_1.add_command(label='option 1-3', command=optionclicked())
        # 为菜单2添加级联项
        menu_2.add_cascade(label='option 2-1', menu=menu_2_1)
        # 为子菜单添加选项
        menu_2_1.add_command(label='option 2-1-1',
         command=optionclicked())
        menu_2_1.add_command(label='option 2-1-2',
         command=optionclicked())
        menu_2_1.add_command(label='option 2-1-2',
         command=optionclicked())
        # 为菜单2添加普通选项
        menu_2.add_command(label='option 2-2', command=optionclicked())
        menu_2.add_command(label='option 2-3', command=optionclicked())
        
        # 为菜单3添加普通选项
        menu_3.add_command(label='option 3-1', command=optionclicked())
        menu_3.add_command(label='option 3-2', command=optionclicked())
        menu_3.add_command(label='option 3-3', command=optionclicked())
        root['menu'] = mbar
        
root.mainloop()

函数会自动运行一次，不知是何原因。



## 1.9 Message消息

Message控件提供了呈现多行文本的方法。可以用字体、前景色、背景色的组合来设定完整的消息，一般的格式无外乎：
	
    Message(root, text='text', bg='royalblue', fg='ivory', relief='groove').pack()

## 1.10 Text文本框
文本框控件是一种多功能的控件，其主要功能是显示文本，但可以使用多种风格和字体，包含图像和窗口，也可以绑定事件。
文本框控件可以作为简单的编辑器使用，在这种情况下可以定义多个tag（标签）和marking使得文本修改变得简单。文本框控件比较复杂，涉及到的选项和方法也很多，最好还是参阅技术文档吧。我们正要这么做呢。
文本框控件与标签Label相比，是一种通用得多的方法。文本控件基本上可以当做windows里的一个完备的文本编辑器：
可以混合使用不同的字体、颜色和背景色；
可以插入图像，一幅图像可以当做一个单独的字符；
索引index用以描述文本框控件中的两个字符间的确切位置；
文本框可以在字符间包含不可见的mark对象；


