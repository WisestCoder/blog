### node下的几种文件路径处理
 - __dirname: 总是返回被执行的 js 所在文件夹的绝对路径
 - __filename: 总是返回被执行的 js 的绝对路径
 - process.cwd(): 总是返回运行 node 命令时所在的文件夹的绝对路径
 - path.resolve(xx路径): 同__dirname