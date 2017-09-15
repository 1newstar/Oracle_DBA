#  What-If Command Evaluation

 Oracle集群提供了一系列的评估命令来判断一个特定操作对系统的影响。有了这个特性，我们可以在实际操作之前默认此行为，进而减少对生产环境的影响。
    crsctl eval activate policy
    crsctl eval add resource
    crsctl eval add server
    crsctl eval add serverpool
    crsctl eval delete server
    crsctl eval delete serverpool
    crsctl eval fail resource
    crsctl eval modify resource
    crsctl eval modify serverpool
    crsctl eval relocate resource
    crsctl eval relocate server
    crsctl eval start resource
    crsctl eval stop resource
    srvctl relocate server 
