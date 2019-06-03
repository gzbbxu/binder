**binder_ref** 

binder_ref 用来描述一个Binder 引用的对象，在android 系统中，每一个 client 组件在Binder 驱动程序都有一个binder 引用对象。

handle 是一个引用，进程a 对进程b提供的服务s 的引用。

**binder_node** 



找到binder_ref 就可以根据 node 找到bInder_node





client 是想向进程b发送数据，用handle 来表示进程b,通过handle 找到binder_ref 。通过binder_ref 找到node .通过binder_node 找到进程B，谁来表示进程B？在binder驱动中，使用binder_proc 来表示进程B。



binder_proc  某个服务从属于某个进程的。





进程a向进程b发送数据，调用ioctrl 传入一个handle ,根据handle 找到binder_ref . binder_ref 里面的desc 的描述等于handle , binder_ref 里面有一个指针。指向binder_node ,binder_node 是表示一个服务，binder_node 里面，有一项是binder_proc ,binder_proc 就是对应进程B，就把数据放到进程B的某个链表里面去了。

真实的应用场景会有多个client 程序，来向这个进程B要求提供服务，进程B就会创建多个线程，来向client 程序提供服务。进程B里面肯定有个结构体来管理线程。

**binder 节点总结：**

binder_proc 中有 **rb_root  threads** ;是一个红黑树，这个树里面挂着多个线程，线程就是使用binder_thread 来表示。每一个线程都有个binder_thread 结构体。在这个引用过程中 源头是谁？

源头是server 的binder_node .

1,首先创建binder_node 别人才能引用你。server 为每个服务创建binder_node。binder_node 里面有proc 选项，binder_node.proc = server 进程。

2,service_manager 会在内核里面创建binder_ref 引用 binder_node .   binder_ref  有一项证数，binder_ref.desc = 1,2,3....

在用户态会创建服务链表，链表中有 服务的名字，hander  。 这个handle binder_ref.desc

3,client 向service_manager 查询服务。传name

4，service_manager 返回handle  给内核态给驱动程序。

5，驱动程序在service_manager 的binder_ref 红黑树中根据handle 找到bineder_ref 在根据binder_ref.node 找到binder_node 。

最后，给client 创建新的binder_ref .指向binder_node . 它的desc 从1 开始。

驱动返回desc 给client 就是handle 。

6，client 发送给handle ,驱动根据handle 找到binder_ref 。每个进程都有一些列binder_ref 根据整数号找到binder_ref 。再根据binder_ref 找到binder_node .根据binder_node 找到 server 进程 . 就可以传数据了





创建binder 是再内核态里面，所有的创建binder_node,binder_ref 都是再内核态驱动程序里面创建的。



