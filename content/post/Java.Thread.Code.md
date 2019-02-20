



Thread

    static{
        static native void registerNatives()
    }

    long tid
    volatile char name[]
    static long threadSeqNumber
    volatile int threadStatus
    int priority
    Runnable target

    volatile Object parkBlocker
    volatile Interruptible bocker
    final Object blockerLock

    ThreadGroup group
    ThreadLocal.ThreadLocalMap threadLocals, inheritableThreadLocals

    boolean single_step
    boolean daemon
    boolean stillborn
    long stackSize
    long nativeParkEventPointer

    final static int [MIN| NORM| MAX]_PRIORITY= 1| 5| 10

    
    
    public Thread(...)
        init(ThreadGroup, Runnable, ThreadName, stackSize, accessControlContext)


    static native Thread currentThread()
    static native void yield()
    static native void sleep(long mills[, int nanos])
    synchronized void start()
        native void start0()
    native void run()
    void interrupt()
    static boolean interrupted()
        native boolean isInterrupted(bClearInterrupted)
    final native boolean isAlive()
    final void setPriority(priority)
    static int activeCount()
    static int enumerate(Thread ar[])
    final synchronized void join([mills= 0[, nanos]])
    final void setDaemon(on)
    static native boolean holdsLock(obj)
    StatckTraceElement[] getStackTrace()


    native static StackTraceElement[][] dumpThreads(Thread[])
    native static Thread[] getThrads()
    


    enum State{NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED;}


    
.....// TODO