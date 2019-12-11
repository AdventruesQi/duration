# node的卸载以及写入权限的问题：

### 1.卸载node：

```javascript
sudo npm uninstall npm -g
sudo rm -rf /usr/local/lib/node /usr/local/lib/node_modules /var/db/receipts/org.nodejs.*
sudo rm -rf /usr/local/include/node /Users/$USER/.npm
sudo rm /usr/local/bin/node
sudo rm /usr/local/share/man/man1/node.1
sudo rm /usr/local/lib/dtrace/node.d
```





### 2.在已经安装好node之后想使用npm在全局下安装react的脚手架工具，在安装过程中报错：**Missing write access to /usr/local/lib/node_modules npm ERR! path /usr/local/lib/node_modules**



### 解决办法：

```
修改npm包所安装目录的权限：sudo chown -R $USER /usr/local 然后输入密码就可以了
```

