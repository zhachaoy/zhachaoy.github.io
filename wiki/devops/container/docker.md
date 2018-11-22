# Docker

1. **OverlayFS**
   1. 说明
      > 是一种堆叠文件系统, 于2014年正式合并到Linux-3.18主线内核, 它依赖并建立在其它的文件系统之上(例如ext4fs和xfs等等), 并不直接参与磁盘空间结构的划分, 仅仅将原来底层文件系统中不同的目录进行"合并", 然后向用户呈现.
   
   2. 配置github token(Minishift会去github上验证，并调用其api，不配置token将没有api权限)
      ```bash
      export MINISHIFT_GITHUB_API_TOKEN=<token_ID>
      ``