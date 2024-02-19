# ansible 使用心得

1. ansible 是将系统的运维的繁杂行为 做了一个配置抽象
2. 不同平台同样是安装可能使用不同的配置

## fact/var 
每一个机器/分组可以配置一个树形变量 (有搜索优先级), 对于一个运行的命令来说,可以访问所
运行机器上所有配置的变量,也可以制定host名称来获取其他机器伤的变量

1. generate fact模式每个主机都会进行,可以获取到一些机器信息
    对于一个host来说 都有一个大的全局变量来存储基础相信.这个全局变量是有继承覆盖关系的继承树
   1. 变量来自于:
      1. cmd < role default < group_vars < host_vars < fact < vars < vars_prompt < var_files
      2. inventory < playbook < play < role < block < task < include_vars < registered vars
   2. 具体搜索优先级如下:
      1. command line values (for example, -u my_user, these are not variables)
      2. role defaults (defined in role/defaults/main.yml) 1
      3. inventory file or script group vars 2
      4. inventory group_vars/all 3
      5. playbook group_vars/all 3
      6. inventory group_vars/* 3
      7. playbook group_vars/* 3
      8. inventory file or script host vars 2
      9. inventory host_vars/* 3
      10. playbook host_vars/* 3
      11. host facts / cached set_facts 4
      12. play vars
      13. play vars_prompt
      14. play vars_files
      15. role vars (defined in role/vars/main.yml)
      16. block vars (only for tasks in block)
      17. task vars (only for the task)
      18. include_vars
      19. set_facts / registered vars
      20. role (and include_role) params
      21. include params
      22. extra vars (for example, -e "user=my_user")(always win precedence)

2. 对于共享数据可以创建一个不生产fact的虚拟主机进行共享


## 循环与控制,
1. 与传统编程不同, 对于循环来说不能使用局部变量,都是全局变量
    这就间接导致loop不可以与var联合使用,否则var只有第一次的结果.


