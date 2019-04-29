# QG移动组最终考核第一周

### Gsonfomt插件

设置-->插件plugins的Browse repositories搜索 GsonFormat  然后安装,重启AS后，就已经安装好了

#### 使用GsonFormat

在一个实体Bean类里，右键打开Generate，选择GsonFormat后，弹出一个代码框，需要我们往里面放很丑很难看的JSON数据，把数据放进去后，点击OK，这个bean类就被创建好了



### AS中快速提交到GitHub保存

1. 打开Android Studio配置界面，找到File——>settings——>Version Control——>Git，输入正确的Git安装目录
2. 再找到Version Control节点下的GitHub，然后输入你自己的GitHub账号和密码，点击Test看一下是否能够成功连接。然后Apply——>OK
3. 从GitHub上检出项目，VCS——＞Checkout from Version Control——>GitHub
4. 更新代码到GitHub，右键项目——>Git——>Add——>Commit。或者是VCS——>Git——>Add——>Commit
5. 提交代码，在Commit的时候提交说明是必须填写的。然后点击Commit and push，代码就上传到GitHub上了；也可以只Commit，然后在Push。