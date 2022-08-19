# Fragment

Fragment 代表了 Activity 的子模块，可以理解为 Activity 的片段，其必须嵌入 Activity 中使用，且其生命周期受到它所在的 Activity 的影响。

关于 Fragement，有如下特征：

1. Fragment 总是作为 Activity 的组成部分。Fragment 可调用 getActivity() 获取它所在的 Activity，Activity 可以调用 FragmentManager 的 findFragmentById() 或 findFragmentByTag() 来获取 Fragment；
2. 在 Activity 运行过程中，可以调用 Fragment 的 add()、remove()、replace() 动态地添加、删除、
3.  Fragment；
4. 一个 Activity 可以同时组合多个 Fregment，一个 Fregment 一个被多个 Activity 复用；
5. Fragment 可以响应自己的输入事件，并拥有自己的生命周期，但它们的生命周期由 Activity 控制。



## 创建Fragment                                                                                                                                                                                                                             

开发 Fragment 与开发 Activity 非常相似，区别在于开发 Activity 需要继承 Activity 或其子类，而开发 Fragment 需继承 Fragment 或其子类。

通常来说，创建 Fragment 需要实现如下三种方法：

```java
//创建Fregment对象回调该方法，该在实现代码中值初始化想要初始化的Fragment中保持的必要组件
protected void onCreate(Bundle savedInstanceState);
//当Fragment绘制界面组件时会回调该方法，方法必须返回一个View，即Fragment显示的View
public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState);
//当用户离开该Fragment时回调该方法
protected void onPause();
```



## Fragment与Activity通信

将 Fragment 添加到 Activity 中有如下两种方式：

1. 在布局文件中使用 <fragment.../> 元素添加 Fragment，其 android:name 属性指定 Fragment 的实现类。

```xml
<!-- 添加一个Fragment -->
<fragment
	android:id="@+id/book_list"
	android:name="com.example.fragmentdemo.BookListFragment"
	android:layout_width="0dp"
	android:layout_height="match_parent"
	android:layout_weight="1" />
```

2. 在代码中通过 FragmentTrasaction 对象的 add() 方法添加 Fragment。

```java
// 向Fragment传入参数
fragment.setArguments(arguments);
// 使用fragment替换book_detail_container容器当前显示的Fragment
getSupportFragmentManager().
    beginTransaction().
    replace(R.id.book_detail_container, fragment).
    commit();
```



## Fragment管理与事务

Activity 管理 Fragment 主要依靠 FragmentManger，Fragment 可以完成以下方面功能：

1. 使用 findFragmentById() 或 findFragmentByTag() 获取指定 Fragment；
2. 调用 popBackStack() 将 Fragment 从后台栈弹出（模拟 Back 键）；
3. 调用 addOnBackStackChangeListener 注册一个监听器，用于监听后台栈的变化。

每个 FragmentTransaction 可以包含多个 Fragment 的修改，如包含多个 add()、remove()、replace()  操作，最后调用 commit() 方法提交。在调用 commit() 之前，开发者调用 addToBackStack() 将事务添加到 Back 栈中，该栈由 Activity 管理，这样允许用户返回到前一个 Fragment 的状态。

```java
//创建一个新的Fragment并打开事务
ExampleFragment newFragment = new ExampleFragment();
FragmentTrasaction transaction  = getSupportFragmentManager().beginTransaction();
//替换该界面中的fragment_container内的Fragment
transaction.replace(R.id.fragment_container,newFragment);
//将事务添加到Back栈中,允许用户返回到替换Fragment前的状态
transaction.addBackStack(null);
//提交事务
transaction.commit();
```



## Fragment的生命周期

```java
//当该Fragment被添加到它所在的Context时被回调,只被调用一次
onAttach();
//创建Frgment时被回调，该方法只会被调用一次
onCreate();
//每次创建、绘制该Fragment的View组件时回调该方法
onCreateView();
//当Fragment所在的Activity被启动完成后回调该方法
onActivityCreated();
//启动Fragment时被回调
onStart();
//恢复Fragment时被回调
onResume();
//暂停Fragment时被回调
onPause();
//停止Fragment时被回调
onStop();
//销毁Fragment时被回调，该方法只被调用一次
onDestory();
//将该Fragment从它所在的Context中删除、替换完成时回调该方法
onDetach();
```



## Fragment导航

### 结合ViewPager实现分页导航

ViewPager 组件与 AdapterView 有些类似，ViewPager 只是一个容器，该组件显示的内容由 Adapter 提供，因此必须为其设置一个 Adapter。

```xml
<androidx.viewpager.widget.ViewPager
        android:id="@+id/container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
```

```java
public static class DummyFragment extends Fragment{
        private static final String ARG_SECTION_NUMBER = "section_number";
        //该方法用户返回一个DummyFragment实例
        public static DummyFragment newInstance(int sectionNumber){
            DummyFragment fragment = new DummyFragment();
            //定义Bundle用于向DummyFragment传入参数
            Bundle args = new Bundle();
            args.putInt(ARG_SECTION_NUMBER,sectionNumber);
            fragment.setArguments(args);
            return fragment;
        }

        //重写方法用于生成该Fragment所显示的控件
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            //加载/res/layout/目录下的fragment_main.xml文件
            View view = inflater.inflate(R.layout.fragment_main,container,false);
            TextView textView = view.findViewById(R.id.setion_lable);
            textView.setText("Fragment页面"+getArguments().getInt(ARG_SECTION_NUMBER,0));
            return view;
        }
    }

    //定义FragmentPagerAdapter的子类，用于作为ViewPager的Adapter对象
    public class SectionPagerAapter extends FragmentPagerAdapter{

        public SectionPagerAapter(@NonNull FragmentManager fm) {
            super(fm);
        }

        //根据指定位置返回指定的Fragment
        @NonNull
        @Override
        public Fragment getItem(int position) {
            return DummyFragment.newInstance(position);
        }

        @Override
        public int getCount() {
            return 3;
        }
    }
```



### 结合TabLayout实现Tab导航

```xml
<com.google.android.material.tabs.TabLayout
        android:id="@+id/tabs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <com.google.android.material.tabs.TabItem
            android:id="@+id/tabItem1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="标签一"/>
        <com.google.android.material.tabs.TabItem
            android:id="@+id/tabItem2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="标签二"/>
        <com.google.android.material.tabs.TabItem
            android:id="@+id/tabItem3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="标签三"/>

    </com.google.android.material.tabs.TabLayout>
```

关联 TabLayout 组件与 ViewPager

```java
//获取界面上的TabLayout组件
TabLayout tabLayout = findViewById(R.id.tabs);
//设置从ViewPager到TabLayout的关联
viewPager.addOnPageChangeListener(new TabLayout.TabLayoutOnPageChangeListener(tabLayout));
//设置从TabLayout到ViewPager关联
tabLayout.addOnTabSelectedListener(new TabLayout.ViewPagerOnTabSelectedListener(viewPager));
```

