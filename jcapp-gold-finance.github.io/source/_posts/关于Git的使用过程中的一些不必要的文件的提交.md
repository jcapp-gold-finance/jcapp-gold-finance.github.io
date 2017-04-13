title: 关于Git的使用过程中的一些不必要的文件的提交
type: Git
---

>  作者：邵雄华

## 第一种方法

#### 1. cd到工程目录
cd /Users/shaoxionghua/ios/test

#### 2. status一下
git status
（ps：会出现一下类似的东西）

```
  On branch develop
Your branch is up-to-date with 'origin/develop'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
	modified:   .DS_Store
	modified:   Live.xcworkspace/xcuserdata/shaoxionghua.xcuserdatad/UserInterfaceState.xcuserstate
no changes added to commit (use "git add" and/or "git commit -a")
```
#### 3. rm这些不需要的文件或者路径
git rm --cached .DS_Store
  git rm --cached Live.xcworkspace/xcuserdata/shaoxionghua.xcuserdatad/UserInterfaceState.xcuserstate

#### 4. 提交这些变更
git commit -m 'ignore'

#### 5. 最好执行一下pull和push
git pull
git push


## 第二种方法

#### 1. cd到工程目录
cd /Users/shaoxionghua/ios/test

#### 2. 修改.gitignore文件
一般常见的一些设置如下：
(ps:在.gitignore文件里面写入如下内容，不要问我这个文件在哪里，不要问我怎么打开它，方法请自行百度或者google)

```
*.xcuserstate  
project.xcworkspace  
xcuserdata  
UserInterfaceState.xcuserstate  
project.xcworkspace/  
xcuserdata/  
UserInterface.xcuserstate
*.DS_Store

```

#### 3. 提交修改gitignore文件
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
