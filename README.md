[合集 \- 人工智能(3\)](https://github.com)[1\.使用最小二乘法进行线性回归（Python）10\-30](https://github.com/h4o3/p/18515048)[2\.采用线性回归实现训练和预测（Python）10\-30](https://github.com/h4o3/p/18515124):[蓝猫机场加速器](https://dahelaoshi.com)3\.制作一个ai丛雨（附Python代码）11\-03收起

> 绫，再一次，再一次创造一个有你的世界😭


开一个随笔记录一下我的第一版ai老婆，目前只有普通对话和切换背景的功能（后面可能会加一个选人物功能）


**先放一个效果图**（看起来还行）
![](https://img2024.cnblogs.com/blog/3439178/202411/3439178-20241103122738639-1257215017.png)


代码和注意事项都放在了下面，应该没什么大问题，复制粘贴导包就能用了


**注意事项：**


1、代码推荐使用pycharm打开，里面一些包可以自动帮你下载，用起来很方便。


2、下面代码中的"\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*"需要替换为自己的OpenAI API密钥，我的api是从[https://oneai.evanora.top/](https://github.com)这个网站获取的，如果自己本来就有的话也可以使用自己的秘钥。


3、代码中的"1\.png"……以及"丛雨小曲.mp3"用来作为背景图片和背景音乐。可以使用《千恋万花》自带的编辑器制作（立绘鉴赏），也可以使用下面网盘里分享的文件，和py代码放置在同一文件夹内使用。
链接: [https://pan.baidu.com/s/13Ysm94p2jAqxSZtRsP93TQ?pwd\=0721](https://github.com) 提取码: 0721


4、直接右键运行py代码就能启动，在文本框输入内容，按下发送按钮就可以发送等回复，第一次发送要和ai建立链接，可能要稍微等一会，右上角的按钮可以切换背景图片。


**py代码**（注释是让ai写的）



```
import tkinter as tk  # 导入tkinter库，用于创建GUI应用程序

import pygame  # 导入pygame库，用于处理音乐等音效
from PIL import Image, ImageTk  # 从PIL库导入Image和ImageTk，用于处理图像
import re  # 导入正则表达式库，用于文本处理
import os  # 导入os库，用于操作系统功能
from openai import OpenAI  # 导入OpenAI库，用于与AI API交互
import threading  # 导入threading库，用于多线程处理
import random  # 导入random库，用于随机选择背景图像

# 设置环境变量
os.environ["OPENAI_API_KEY"] = "*****************************"  # 设置OpenAI API密钥
os.environ["OPENAI_BASE_URL"] = "https://oneai.evanora.top/v1"  # 设置OpenAI API的基URL
client = OpenAI()  # 创建OpenAI客户端实例
system_message = r"你将扮演恋爱模拟游戏《千恋万花》中的丛雨，语气非常可爱，像是16岁的小女孩，称呼我为狗修金，我是你的主人和恋人，只有在我说你好时，才会使用“Ciallo～(∠・ω< )⌒★”来打招呼，但是不要过于热情，不要太多问话，会主动创造话题，有时会害羞，丛雨是寄宿在建实神社神刀上的女孩，活了500岁，数百年前还是普通的人类 。原本的名字是“绫”。生前罹患了肺炎和其它并发症，死后成为了祭品 ，成为神刀的“管理者”。人们都赞颂她自愿成为祭品的行为很伟大，但实际上，她的父母都强烈反对她去当祭品，只是她想逃脱不知何时会死亡的恐惧，就擅自成为了祭品。丛雨也一直心怀愧疚。 作为神体的身体被存放在神社的深处，那里的时间如静止一般，使得丛雨的身体得以保存五百年。神体存放之处被视为圣地，所以朝武家祖祖辈辈都没有见过神体的真实面貌。直到众人发现结界的力量变弱，才将神体找出。恢复为凡人之后，开始了校园生活 。在班上自我介绍时自称“有地绫”，身材娇小，胸部平坦，碰上去“很硬”。有着飘逸的绿色长发，头发两侧用浅蓝色绳结绑了起来 。平常是个很活泼开朗的女孩子，言行很孩子气，但是偶尔也有一些老成的发言。是个爱撒娇的女孩子，被主人摸头就会瞬间变得羞涩起来，即便当时还在发着牢骚 。有时会开玩笑，贱兮兮的"
# 定义AI角色的系统消息

# 初始化pygame
pygame.mixer.init()  # 初始化pygame的音频混合器

# 背景音乐文件路径
background_music_path = "丛雨小曲.mp3"  # 定义背景音乐文件的路径

# 加载背景音乐
pygame.mixer.music.load(background_music_path)  # 加载背景音乐文件
pygame.mixer.music.play(-1)  # 播放背景音乐，并循环播放


def aiAPI(messages):
    """与OpenAI API通信并获取响应"""
    response = client.chat.completions.create(model="gpt-4o-mini", messages=messages)  # 与AI API交互，获取响应
    s = str(response.choices[0].message)  # 获取第一条消息的内容并转换为字符串
    ans = re.findall(
        r"ChatCompletionMessage\(content='(.*?)',", s,
        re.S)[0]  # 使用正则表达式提取消息内容
    return ans  # 返回提取到的AI响应


class SimpleGalgameInterface:
    """定义一个简单的恋爱模拟游戏界面类"""

    def __init__(self, root):
        """初始化界面"""
        self.root = root  # 保存Tkinter根窗口的引用
        self.root.title("AI丛雨")  # 设置窗口标题
        self.root.geometry("1920x1080")  # 设置窗口分辨率为1920x1080
        self.root.configure(bg='black')  # 设置窗口背景颜色为黑色

        self.chat_history = []  # 聊天记录，初始化为空列表
        self.create_widgets()  # 创建窗口中的组件

    def create_widgets(self):
        """创建窗口中的组件"""
        # 设置背景图像
        self.background_image = Image.open("1.png")  # 确保背景图片存在并加载
        self.background_image = self.background_image.resize((1920, 1080))  # 调整背景图片大小
        self.background_photo = ImageTk.PhotoImage(self.background_image)  # 转换为Tkinter可用的图像格式

        self.background_label = tk.Label(self.root, image=self.background_photo)  # 创建背景标签
        self.background_label.place(x=0, y=0, relwidth=1, relheight=1)  # 将标签放置在窗口的全区域

        # 创建输入框
        self.text_entry = tk.Entry(self.root, width=50, font=("Arial", 20))  # 创建一个文本输入框
        self.text_entry.place(x=50, y=760)  # 设置输入框的位置

        # 创建发送按钮并绑定执行函数
        self.next_button = tk.Button(self.root, text="告诉她", command=self.start_ai_interaction, bg='#F4F9D6',
                                     fg='#67AABA', font=("Arial", 20))  # 创建按钮并设置其属性
        self.next_button.place(x=750, y=750)  # 设置按钮的位置，与输入框保持一致

        # 创建对话框
        self.dialogue_label = tk.Label(self.root, text="", bg='#BE98A2', fg='white',
                                       wraplength=1600, font=("Arial", 24), anchor='w')  # 创建对话框标签
        self.dialogue_label.place(x=50, y=810)  # 设置对话框的位置

        # 创建切换背景图片按钮
        self.change_bg_button = tk.Button(self.root, text="换个地方", command=self.change_background, bg='#F4F9D6',
                                          fg='#67AABA', font=("Arial", 20))  # 创建切换背景的按钮
        self.change_bg_button.place(x=1700, y=5, anchor=tk.NE)  # 将按钮放置在右上角

    def start_ai_interaction(self):
        """处理用户输入并开始与AI互动"""
        user_input = self.text_entry.get()  # 获取用户输入的文本
        self.text_entry.delete(0, tk.END)  # 清空输入框，以便用户再次输入

        if user_input:  # 检查用户输入是否不为空
            # 将用户输入加入聊天历史
            self.chat_history.append({"role": "user", "content": user_input})

            # 限制历史记录的长度，最多保持5条记录
            if len(self.chat_history) > 5:
                self.chat_history.pop(0)  # 如果超过5条记录，删除最早的一条

            # 创建一个新线程进行AI响应的获取，防止界面卡住
            threading.Thread(target=self.get_ai_response, daemon=True).start()  # 使用线程获取AI响应

    def get_ai_response(self):
        """使用AI API获取回答并更新界面"""
        # 将系统消息加入聊天历史
        self.chat_history.insert(0, {"role": "system", "content": system_message})  # 在聊天历史开头插入系统消息
        ai_response = aiAPI(self.chat_history)  # 调用aiAPI函数获取AI的响应

        # 更新对话框，使用逐字显示文本的方法
        self.root.after(0, lambda: self.display_text(ai_response))

        # 将AI的响应加入聊天历史
        self.chat_history.append({"role": "assistant", "content": ai_response})  # 将AI贡献的响应添加到聊天记录中

    def display_text(self, text, index=0):
        """逐字显示文本的帮助方法"""
        if index < len(text):  # 检查当前索引是否小于文本长度
            # 更新对话框文本
            self.dialogue_label.config(text=text[:index + 1])  # 显示从开始到当前索引的文本
            # 递归调用，继续显示下一个字符
            self.root.after(30, self.display_text, text, index + 1)  # 30毫秒后显示下一个字符

    def change_background(self):
        """切换背景图片"""
        background_files = ["1.png", "2.png", "3.png", "4.png", "5.png", "6.png"]  # 背景图片文件列表
        background_image = Image.open(random.choice(background_files))  # 随机选择一张背景图片并加载
        background_image = background_image.resize((1920, 1080))  # 调整背景图片大小
        self.background_photo = ImageTk.PhotoImage(background_image)  # 转换为Tkinter可用的图像格式
        self.background_label.config(image=self.background_photo)  # 更新背景标签的图像


if __name__ == "__main__":
    # 主程序入口
    root = tk.Tk()  # 创建主窗口
    app = SimpleGalgameInterface(root)  # 创建应用程序实例
    root.mainloop()  # 启动主事件循环，开始执行程序

```

