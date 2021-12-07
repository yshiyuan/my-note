# pthread笔记

POSIX线程数据类型

| 类型                | 描述                  |
| ------------------- | --------------------- |
| pthread_t           | 线程标识符            |
| pthread_mutex_t     | 互斥量                |
| pthread_code_t      | 条件变量              |
| pthread_key_t       | 线程私有权握访问键    |
| pthread_attr_t      | 线程属性对象          |
| pthread_mutexattr_t | 互斥量属性对象        |
| pthread_condattr_t  | 条件变量属性对象      |
| pthread_once_t      | 一次性初始化 控制变量 |

* 初始线程与普通线程的不同之处:
  * main函数返回时，即初始线程结束时，不会等待其他线程结束。如果希望初始线程终止时，进程中的其他线程继续执行，则需要在初始线程中调用pthread_exit，而不是从main函数中返回。
  * 初始线程的启动函数（即main函数）是从程序外部被调用的，例如很多UNIX系统中将程序连接到一个crt0.o文件上，该文件负责初始化进程然后调用main函数。
* 线程如果没有被**分离**，终止时会保留其虚拟内存，包括他们的栈空间以及其他系统资源。为确保已终止线程的资源能被进程回收重用（不是销毁，大部分系统可以重用资源来建立新线程），应该在每个线程结束时分离他们。
* 可以使用属性来建立可分离的线程。pthread_detach可以通过线程ID随时分离自己或者其他线程。pthread_join阻塞调用者直到指定线程终止，并且自动分离指定线程。
* 互斥量pthread_mutex_t
  * 初始化
    * 静态初始化：

      ```cpp
        pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
      ```

      使用PTHREAD_MUTEX_INITIALIZER宏静态初始化的互斥量不需要调用pthread_mutex_destory来释放它。
    * 动态初始化：

      ```cpp
        int status;
        pthread_mutex_t mutex;
        status = pthread_mutex_init(&mutex, NULL);
        if (status != 0) {

        } else {
            status = pthread_mutex_destory(&mutex);
        }
      ```

      动态初始化的互斥量必须调用pthread_mutex_destory释放。
