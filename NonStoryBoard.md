## 第一步 删除 Scenedelegate.h 和 Scenedelegate.m 文件 

![1284564-5c311138a8d8d757](https://user-images.githubusercontent.com/88173886/128486647-75e16bce-33a1-4d23-831e-4d597495c96d.png)


## 第二步  去TARGETS里删除Main interface 中的main（置空就可以)

![1284564-cf79ae05322bd9b1](https://user-images.githubusercontent.com/88173886/128486682-d9b1170b-dcde-44b4-95f6-3403d501119e.png)


## 第三步 去到info.plist，删掉如下图箭头所示（整行删掉）

![1284564-bca202470c8136fb](https://user-images.githubusercontent.com/88173886/128486715-4278c031-c49f-4edf-af8a-84ce258d08c0.png)


## 第四步 去appdelegate.m文件里删除关于 UISceneSession 所有方法

![1284564-5a575398da4c8f98](https://user-images.githubusercontent.com/88173886/128486743-997c4a26-e580-4ee9-8e54-1b12c3646cac.png)


## 第五步 记得在appdelegate.h添加 window属性。 大功告成可以纯代码写了

![1284564-aac3cc8d1535be0d](https://user-images.githubusercontent.com/88173886/128486777-db3c646a-953d-44db-8f5b-0d623d32b407.png)

![1284564-3b6fb46f71e68493](https://user-images.githubusercontent.com/88173886/128486798-b894c578-300e-48a0-8150-baf3951b4288.png)
