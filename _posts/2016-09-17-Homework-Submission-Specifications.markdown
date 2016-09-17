---
layout:     post
title:      "Homework Submission Specifications"
date:       2016-09-17 1:00:00
author:     "Java Course Staff"
header-img: "img/post1.jpg"
header-mask: 0.3
catalog:    true
---

## How to submit your homework via Git
___

/*This instruction is designed to give a clear instruction on how to hand in your homework.*/

#### Step 1:

* register your own account [Here](http://git.oschina.net/)
* **Attention**: when you register your account, please make sure that your url is 'iss'+your student number
![](/img/Submission-instruction/instruction_1.png)  

---

#### Step 2:

* create your own private project

![](/img/Submission-instruction/instruction_3.png) 

* select the private option 

![](/img/Submission-instruction/instruction_2.png)  

---

#### Step 3:

* Add SSH key [(Reference)](http://git.mydoc.io/?t=83157)

---

#### Step 4:

* After finishing your project, please commit and push your labour to your own repository.
* Note that the repository should be named as `homework_x` where x is the homework number with no extra zeros.

---

Here I will show you how to use command line to upload.

*1) Create an empty repo named `homework_x` and enter the repo's root directory.*

```
$ git init homework_x
$ cd homework_x
```

*2) Add a remote host named `origin` with your remote repo's **SSH address**.*

```
$ git remote add origin 'SSH Address'
```

The SSH address of your repo may look like this one:
![](/img/Submission-instruction/instruction_4.png)  

*3) If your OSChina git repo is not empty, you need to pull it first.*

```
$ git pull origin master
```

*4) Then Happy Coding in your local repo!*

*5) After coding , perform an Add -> Commit -> Push workflow.*

```
$ git add file1.java
$ git add file2.java
$ ......
```

You can also add all files in a single `add` command.

```
$ git add .
```

Then commit a new version with a commit message.

```
$ git commit -m "Hello" 
```

*6) Finally don't forget to upload your labour to the remote repo.*

If you are making a first push, add `-u origin master` option to establish an upstream relationship from localhost:master to origin:master.

```
$ git push -u origin master
```

If you have made a push before, you can just omit that option.

```
$ git push
```

For detailed Git tutorial, please go to [廖雪峰的Git博客](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000).

#### Step 5:

* Add our TA as a project member
![](/img/Submission-instruction/instruction_5.png)
* We will grab all the project right after the deadline. 

## [What's More](http://git.mydoc.io/?t=84110)
---
* Author: Xiaoyu Zheng
* CopyRight: TA group of ISS-Java course
* Any question please ask on the Piazza.
* Any kind of cheating will be punished, but you are allowed to ask your classmates for help.
