---
title: Generate a SetUp.exe file in WinForms application
cover: https://img.hendryshi.com/i/2023/04/6431ba655a543.webp
tags:
  - .NET
categories:
  - Tech
abbrlink: 962e50b3
date: 2020-05-17 12:19:00
---


## Create a Setup Project for your application

When you are sure that have finished your development for your application (WinForms application in my case). You can right click your Solution in `Solution Explorer`, then click `Add new project`. In the windows came out, search `Installer`. then choose `Setup Project` And create it.

<!-- more -->

![SetUpProject](https://img.hendryshi.com/i/2023/04/6431ba7ac9836.webp)

## Create Primary output

After you create your Installer project, you will find three folders in the left of screen:
    - The folder `Application Folder` stocks all your project files and resources(documents, images).
    - The folder `User's Desktop` can create shortcut in desktop
    - The folder `User's Programs Menu` can create shortcut in Program menu.

Now right click the folder `Application Folder`  and choose `Add project output`. In the windows came out, choose your primary project in dropdown list `Project`. And click `OK`.

![PrimaryOutput](https://img.hendryshi.com/i/2023/04/6431ba823a2ce.webp)

In my case, I have some image resource, so I also created a sub folder named `Img` to stock my images.

![ImgFolder](https://img.hendryshi.com/i/2023/04/6431ba8987047.webp)

After you have added your primary output, you can now set some properties for your SetUp application. Right click your Installer project, and select property. In this property windows, you can set your application's author, your application's description, and some other properties.

![Property](https://img.hendryshi.com/i/2023/04/6431ba943195d.webp)

## Create Desktop & Program Menu shortcut

In the other two folder, you can set up your shortcuts in desktop or program menu. Right click the folder and choose `Create shortcut to user's desktop`. Then you can name your shortcut and you can also set a icon for your shortcut. (Note: for the shortcut's icon, only the format `.ico` is accepted).

## Add Prerequisites to your installer project

In some cases, your application need some other dependencies to run it like .Net Framework, then you should include these components when installing it. Right click your installer's project, select `Property` => `Prerequisites`. In this windows, you can select which prerequisites to install. 

![Prerequisites](https://img.hendryshi.com/i/2023/04/6431ba9c7ee5f.webp)