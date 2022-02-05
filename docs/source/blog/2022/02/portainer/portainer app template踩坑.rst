portainer template 踩坑
=======================================


背景
---------


过年在家，修复了老家的Gen 8。升级 存储虚拟机ubuntu18.04 到20.04 的时候居然失败无法开机了.
折腾了半天，也没有搞好，索性在虚拟机里新开了一个20。04，好在当初数据盘是独立的，直接迁移绑定就好。
于是需要重新部署samba。决定吧之前工作中的经验拿来发光发热。

于是计划吧samba部署到docker swarm中，配合ldap，用portainer管理。
traefik来管理router规则


依赖
-------------
#. docker swarm


过程
-------------
#. 首先当然是部署portainer，直接用了 官方安装过程
    https://docs.portainer.io/v/ce-2.11/start/install/server/swarm/linux

    #.
        curl -L https://downloads.portainer.io/portainer-agent-stack.yml \
        -o portainer-agent-stack.yml`
    #. docker stack deploy -c portainer-agent-stack.yml portainer

#. 配置portainer后可见到在这个   Portainer 2.11.0  中多了App Template
    .. image:: ./portainer.png
      :width: 400
      :alt: portainer app template

#. 在配置中可以看到如何设置Template
    .. image:: ./setting.png
      :width: 400
      :alt: portainer setting

#. 修改Template 配置。
    默认地址在github上，国内可以用gitee来复制一个，减少使用魔法。
    我复制的在这里: https://gitee.com/portainer/templates/
    模版地址可以使用这个: https://gitee.com/portainer/templates/raw/master/templates-2.0.json
    要注意的是， 不同版本可能支持不同 template

#. 定制自己的Template
    官方默认的一般很难满足自己的需求,所以需要自己定制。官方文档给出了3中类型，但是在官方的template.json
    中却有4中，怀疑可能是未来要支持的，试了一下如果自己写第四中似乎不能显示

    * 踩坑的地方来了
        #. 如果自己做建一个仓库，通raw的文件作为portainer app template的URL那么一定要注意分支
        #. template规则中的repository默认会用master，而且不能调整
        #. 现在新建的仓库会使用main作为默认分支
    以上三条互相影响，最简单的办法就是用master作为默认分支，都改在master。
    否则，就会遇到明明推上去了，列表没有更新，列表更新了，仓库中的compose没有更新的窘境。
    后来只好推一遍master，推一遍mian，直到我写这个踩坑的时候才发现问题的本质

回顾与总结
=============
#. portainer template 的价值
    #. stack 和 template联动， 通过template可以预设一下compose的配置
        #. 可以给env配置一个简单的参数输入框, 满足简单场景
        #. 部署不需要通过命令/通过脚本去更新了，直接在portainer中点一下就好
#. template 要解决的问题
    #. compose 部署信息中很多其实依旧是和服务设计相关的通用信息，同时也会包含很多用户敏感的数据。简单的做法是通过环境变量在不同的服务直接同步配置变动
    但是，缺点就是配置的环境变量太多，容易遗漏，通过template就可以很好的解决这个问题
#. template 没有解决的问题
    #. 给机器分组，给服务分组打标签，操作依旧很傻
    #. 持久化数据怎么管理



