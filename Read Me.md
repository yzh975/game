# Read Me

## NLPCC2024数据集，数据清洗文件结构

* main.py
* txtRevise.py
* viewpicture.py

---

## 函数功能解释

```
normalview(annotation_path,image)  #查看一张图片的标注情况
normalviewAll(txt_root,img_root)   #查看一个文件夹内的图片标注情况
revise(annotation_path,number1,number2,save_path)  #修改一张图片的标注
reviseAll(folder_path,save_root,number1,number2)   #修改一个文件夹内的图片的标注
```

---

## 步骤流程

### step 1

在main函数中补充root地址 

```
txt_row_root  #未修改过的原始txt文件存放的根目录
txt_show_root #修改过的txt文件存放的根目录
image_root    #图像文件存放的根目录
save_root     #原始txt文件修改过后保存的根目录
```

### step 2

可以先查看随便一张原始图像的标注情况

执行main函数

```
if __name__=="__main__":
    viewpicture.normalview(annotation_row_path,image)
```

其中得到参数*annotation_row_path*和*image*仅需要修改参数*name*即可，name为你想要查看的参数序号

![image-20240521144408236](C:\Users\leilei\AppData\Roaming\Typora\typora-user-images\image-20240521144408236.png)
																					图1-1

发现他的标注偏差较大，于是进行修改

### step 3

可以根据我提供的reviseAll函数先将所有图片标注修改一次，修改后的txt文件保存在save_root文件夹中，后续在对每个图片进行大概的检查，若发现太离谱的标注在进行精修，这样可以减小一张一张图片修改的工作量。

执行main函数

```
if __name__=="__main__":
    txtRevise.reviseAll(txt_row_root,save_root,number1,number2)
```

其中*number1*与*number2*可以在全部图片进行大概修改的过程中使用我选择的参数

```
number1=5
number2=10
```

### step 4

查看所有调整后的标注后的图像文件

执行main函数

```
if __name__=="__main__":
    viewpicture.normalviewAll(txt_show_root,image_root)
```

其中图1-1在执行完大致修改后得到图1-2

![image-20240521144620381](C:\Users\leilei\AppData\Roaming\Typora\typora-user-images\image-20240521144620381.png)
															图1-2

但是并非所有图片效果都这么好，如图1-3

![image-20240521145105791](C:\Users\leilei\AppData\Roaming\Typora\typora-user-images\image-20240521145105791.png)
															图1-3

很明显，这个框的宽设置的比较大，因此 这是第二轮精修时需要考虑的

以及图1-4

![image-20240521145337959](C:\Users\leilei\AppData\Roaming\Typora\typora-user-images\image-20240521145337959.png)
																					图1-4

该图明显除了标点符号以外，其他框中的位置比较偏左上，我们需要将框向右下调整

由于一次只会展示一张图片，因此在查看图片的时候如果发现上述类型的标注图片，可以记下他们的序号，后续使用下面的函数修改

```
txtRevise.revise(annotation_row_path,number1,number2,save_path)
```

### step5

具体的修改流程：

若感觉需要对图像标注的框整体移动在main文件中修改number1与number2即可

```
:param number1:向右下调整xy
:param number2:提高宽度和高度
```

若需要对单独某个字符调整

可以对txtRevise文件的revise函数进行修改

例如

![image-20240521150707786](C:\Users\leilei\AppData\Roaming\Typora\typora-user-images\image-20240521150707786.png)
认为该句子中的前两个‘，’没有框到，偏差较大，我们可以向左上移动，同时后续的‘，’在框移动以后不会受到太大影响，因此我们进行改进

在revise函数中，添加或修改需要的elif

例如，原来的函数为：

```
for box in boxes:
    newBox={}
    center_x = box["center_x"]
    center_y = box["center_y"]
    width = box["width"]
    height = box["height"]
    # 计算框的左上角坐标 需要调整的字在这里添加即可
    if word[i]=='，'or word[i]=='。':
        x1 = center_x - width/2
        y1 = center_y - height/2
    elif word[i]=='.':
        x1 = center_x - width-number1*0.5
        y1 = center_y - height/2
    elif word[i]=='！':
        x1 = center_x - width / 2
        y1 = center_y - height / 2 + number1 * 0.5
    elif word[i]=='：':
        x1 = center_x - width / 2
        y1 = center_y - height / 2 + number1 * 0.5
   ....
```

添加 ==elif  word[i]\=='、'==:的这一部分为

```
for box in boxes:
    newBox={}
    center_x = box["center_x"]
    center_y = box["center_y"]
    width = box["width"]
    height = box["height"]
    # 计算框的左上角坐标 需要调整的字在这里添加即可
    if word[i]=='，'or word[i]=='。':
        x1 = center_x - width/2
        y1 = center_y - height/2
    elif word[i]=='、':
        x1 = center_x - width / 2 - number1
        y1 = center_y - height / 2 - number1
    elif word[i]=='.':
        x1 = center_x - width-number1*0.5
        y1 = center_y - height/2
    elif word[i]=='！':
        x1 = center_x - width / 2
        y1 = center_y - height / 2 + number1 * 0.5
    elif word[i]=='：':
        x1 = center_x - width / 2
        y1 = center_y - height / 2 + number1 * 0.5
    elif word[i]=='一':

```

得到的结果

![image-20240521151805269](C:\Users\leilei\AppData\Roaming\Typora\typora-user-images\image-20240521151805269.png)

每当完成显示出标注图片后，程序自动保存该标注的txt文件

==*切记 是在原始的标注文件进行修改，不要再已经修改后的文件上再进行修改*==