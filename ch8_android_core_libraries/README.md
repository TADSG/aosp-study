# Android 核心Libraries

[上一章：設定 Android Studio](/ch7_android_studio_setup)

Android是一個很龐大的系統，而為了撐起這個系統，Android本身有許多自身專用的Library。如果能先對這些Library有一定程度的認識，在研究Android原始碼時也會比較順利。本章節介紹幾個Android內非常常見的Library及class。

# libutils

## Refbase, sp<?>, 與wp<?>

在Android開發的時候，還沒有所謂的smart pointer，因此Android自己打造了一個自己的pointer系統。所有支援Android pointer的class都必需繼承Refbase，在這之後該class就能以sp和wp的型式存在。
其中sp是StrongPointer的縮寫，而wp是WeakPointer的縮寫。

RefBase裡面有以下function

```C++
class RefBase {
    void incStrong(const void *id) const;
    void desStrong(const void *id) const;
    ...
    class weakref_type {
        RefBase* refBase() const;
        void incWeak(const void *id) const;
        void decWeak(const void *id) const;
        ...
    }
}
```

大致上來說，RefBase會去記住現在被多少人強引用以及弱引用，當沒有強引用時則會回收自己。

sp和wp利用了template來實現泛型，並且override了許多operation，藉此讓用的人在用sp和wp時就像在用原本的class instance。

```C++
template<typename T>
class sp {
public:
    inline sp() : m_ptr(0) { }

    sp(T* other);
    sp(const sp<T>& other);
    sp(sp<T>&& other);
    template<typename U> sp(U* other);
    template<typename U> sp(const sp<U>& other);
    template<typename U> sp(sp<U>&& other);

    ~sp();

    // Assignment

    sp& operator = (T* other);
    sp& operator = (const sp<T>& other);
    sp& operator = (sp<T>&& other);

    template<typename U> sp& operator = (const sp<U>& other);
    template<typename U> sp& operator = (sp<U>&& other);
    template<typename U> sp& operator = (U* other);

    //! Special optimization for use by ProcessState (and nobody else).
    void force_set(T* other);

    // Reset

    void clear();

    // Accessors

    inline  T&      operator* () const  { return *m_ptr; }
    inline  T*      operator-> () const { return m_ptr;  }
    inline  T*      get() const         { return m_ptr; }

    // Operators

    COMPARE(==)
    COMPARE(!=)
    COMPARE(>)
    COMPARE(<)
    COMPARE(<=)
    COMPARE(>=)

private:    
    template<typename Y> friend class sp;
    template<typename Y> friend class wp;
    void set_pointer(T* ptr);
    T* m_ptr;
};
```

```C++
template <typename T>
class wp
{
public:
    typedef typename RefBase::weakref_type weakref_type;
    
    inline wp() : m_ptr(0) { }

    wp(T* other);
    wp(const wp<T>& other);
    wp(const sp<T>& other);
    template<typename U> wp(U* other);
    template<typename U> wp(const sp<U>& other);
    template<typename U> wp(const wp<U>& other);

    ~wp();
    
    // Assignment

    wp& operator = (T* other);
    wp& operator = (const wp<T>& other);
    wp& operator = (const sp<T>& other);
    
    template<typename U> wp& operator = (U* other);
    template<typename U> wp& operator = (const wp<U>& other);
    template<typename U> wp& operator = (const sp<U>& other);
    
    void set_object_and_refs(T* other, weakref_type* refs);

    // promotion to sp
    
    sp<T> promote() const;

    // Reset
    
    void clear();
    // Accessors
    
    inline  weakref_type* get_refs() const { return m_refs; }
    
    inline  T* unsafe_get() const { return m_ptr; }

    // Operators

    COMPARE_WEAK(==)
    COMPARE_WEAK(!=)
    COMPARE_WEAK(>)
    COMPARE_WEAK(<)
    COMPARE_WEAK(<=)
    COMPARE_WEAK(>=)

    inline bool operator == (const wp<T>& o) const {
        return (m_ptr == o.m_ptr) && (m_refs == o.m_refs);
    }
    template<typename U>
    inline bool operator == (const wp<U>& o) const {
        return m_ptr == o.m_ptr;
    }

    inline bool operator > (const wp<T>& o) const {
        return (m_ptr == o.m_ptr) ? (m_refs > o.m_refs) : (m_ptr > o.m_ptr);
    }
    template<typename U>
    inline bool operator > (const wp<U>& o) const {
        return (m_ptr == o.m_ptr) ? (m_refs > o.m_refs) : (m_ptr > o.m_ptr);
    }

    inline bool operator < (const wp<T>& o) const {
        return (m_ptr == o.m_ptr) ? (m_refs < o.m_refs) : (m_ptr < o.m_ptr);
    }
    template<typename U>
    inline bool operator < (const wp<U>& o) const {
        return (m_ptr == o.m_ptr) ? (m_refs < o.m_refs) : (m_ptr < o.m_ptr);
    }
                             inline bool operator != (const wp<T>& o) const { return m_refs != o.m_refs; }
    template<typename U> inline bool operator != (const wp<U>& o) const { return !operator == (o); }
                         inline bool operator <= (const wp<T>& o) const { return !operator > (o); }
    template<typename U> inline bool operator <= (const wp<U>& o) const { return !operator > (o); }
                         inline bool operator >= (const wp<T>& o) const { return !operator < (o); }
    template<typename U> inline bool operator >= (const wp<U>& o) const { return !operator < (o); }

private:
    template<typename Y> friend class sp;
    template<typename Y> friend class wp;

    T*              m_ptr;
    weakref_type*   m_refs;
};
```

## Singleton

Android也做了獨身模式，主要用在系統服務相關的類別。

```C++
template <typename TYPE>
class ANDROID_API Singleton
{
public:
    static TYPE& getInstance() {
        Mutex::Autolock _l(sLock);
        TYPE* instance = sInstance;
        if (instance == 0) {
            instance = new TYPE();
            sInstance = instance;
        }   
        return *instance;
    }   

    static bool hasInstance() {
        Mutex::Autolock _l(sLock);
        return sInstance != 0;
    }   
    
protected:
    ~Singleton() { };
    Singleton() { };

private:
    Singleton(const Singleton&);
    Singleton& operator = (const Singleton&);
    static Mutex sLock;
    static TYPE* sInstance;
};
```

### 參考檔案
* $TOP/system/core/include/utils/RefBase.h
* $TOP/system/core/libutils/RefBase.cpp
* $TOP/system/core/include/utils/StrongPointer.h
* $TOP/system/core/include/utils/Singleton.h


# libcutils

## log.h

給C/C++用的logcat API，大致上有以下這些：

```C++
#ifndef LOG_TAG
#define LOG_TAG NULL
#endif

ALOGV(...)
ALOGV_IF([true|false], ...)
ALOGD(...)
ALOGD_IF([true|false], ...)
ALOGI(...)
ALOGI_IF([true|false], ...)
ALOGW(...)
ALOGW_IF([true|false], ...)
ALOGE(...)
ALOGE_IF([true|false], ...)

```

有`ALOGX_IF`的版本只是使用上比較方便，和普通的並法有差別。使用時記得自己在`Android.mk`或程式碼內`#define LOG_TAG XXX`，否則Logcat打出來會沒Tag。

另外還有`SLOGX`的版本，這會將log打在別的buffer裡面，可以用如`adb logcat -b system`來叫出來。

值得一提的還有`LOG_ALWAYS_FATAL_IF`，用起來就像`assert`，但不同的是可以留下一些log再結束程式。

還想知道有哪些log功能建議直接看原始碼。

## klog.h

用來打kernel log的工具

### 參考資源
* $TOP/system/core/include/cutils/log.h
* $TOP/system/core/include/log/log.h

# 完成！

知道這幾個library和function後，我們就算具備閱讀Android framework的預備知識了！

[下一章：Android 的 `Makefile` -- `Android.mk`](/ch9_android_makefile)

