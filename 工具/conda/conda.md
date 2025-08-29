# conda 常用命令
    1. activate // 切换到base环境
       
    2. activate learn // 切换到learn环境
       
    3. conda create -n learn python=3 // 创建一个名为learn的环境并指定python版本为3(的最新版本)
       
    4. conda env list // 列出conda管理的所有环境
       
    5. conda list // 列出当前环境的所有包
       
    6. conda install requests 安装requests包
    
    7. conda remove requests 卸载requets包
    
    8. conda remove -n learn --all // 删除learn环境及下属所有包
    
    9.  conda update requests 更新requests包
    
    10. conda env export > environment.yaml // 导出当前环境的包信息
    
    11. conda env create -f environment.yaml // 用配置文件创建新的虚拟环境
    
    12. conda info –-envs 检查目前安装了哪些环境变量







