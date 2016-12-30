##c语言多线程学习1
先来看一个例子：
`代码`
<pre><code>
#include'pthread.h'
#include'stdio.h

//迎合pthread_create函数的参数类型
//你也可以不这样定义，只要在调用pthread_create创建线程的时候强制转换一下指针类型就可以了
void* tprocess1(void* args){
         while(1){
                 printf( "tprocess1");
         }
         return NULL;
}

void* tprocess2(void* args){
         while(1){
                 printf("tprocess2");
         }
         return NULL;
}

int main(){
         pthread_t t1;
         pthread_t t2;
         //int pthread_create(pthread_t * thread, pthread_attr_t * attr, void * (*start_routine)(void *), void * arg);
         //pthread_attr_t用来指定线程优先级等属性，一般的情况下，我们没有必要修改，使用默认属性来构造线程，所以这里一般取NULL
		 
	 pthread_create(&t1,NULL,tprocess1,NULL);
         pthread_create(&t2,NULL,tprocess2,NULL);
	 pthread_join方法的功能就是等待线程结束，要等的线程就是第一个参数，程序会在这个地方停下来，直到线程结束，第二个参数用来接受线程函数的返回值
         pthread_join(t1,NULL);
         return 0;
}


</code></pre>


