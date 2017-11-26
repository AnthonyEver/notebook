---
layout: data
title: Data binding 入坑笔记四列表适配器用法
date: 2017-11-24
categories:
- Android Data Binding
tags:
- Data Binding
comments: true
---


![iceland](http://upload-images.jianshu.io/upload_images/2953846-b599da134cb1943f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Data binding 入坑笔记一入门篇](http://www.jianshu.com/p/3e4cd4d951e0)
[Data binding 入坑笔记二进阶篇之双向绑定](http://www.jianshu.com/p/2424306f9c76)
[Data binding 入坑笔记三layout表达式详解](http://www.jianshu.com/p/3dfcaa0b126e)

> 这一篇咱们来讲讲如何在`RecyclerView` 中的`adapter` 来使用Data binding，这个比较重要，因为毕竟各种样式的列表占据了app的半壁江山，我们一会儿先来说说原生怎么使用，然后再实战运用到一个比较火的开源项目[BaseRecyclerViewAdapterHelper](https://github.com/CymChad/BaseRecyclerViewAdapterHelper) 上。

<!-- more -->

## 加载布局方式

在讲适配器之前我们先来说一下`DataBindingUtil`加载布局的两种方式

### 普通加载
```
DataBindingUtil.setContentView(this, R.layout.activity_layout);
```
### 动态加载
```
View itemView = LayoutInflater.from(context)
     .inflate(layoutId, viewGroup, false);
DataBindingUtil.bind(itemView);
```

或直接调用inflate方法加载布局

```
DataBindingUtil.inflate(LayoutInflater, layoutId,
    parent, attachToParent);
```

我们加载列表时在`adapter`中就用到了动态加载

## 原生使用方法

先来看`ViewHolder`我们在构造中用`DataBindingUtil`的`bind`方法将`view`绑定，然后提供一个`bind`方法可以让每个`item`绑定实体

```
    public static class UserHolder extends RecyclerView.ViewHolder {
        private UserItemBinding mBinding;

        public UserHolder(View itemView) {
            super(itemView);
            mBinding = DataBindingUtil.bind(itemView);
        }

        public void bind(@NonNull User user) {
            mBinding.setUser(user);
        }
    }
```

再来看`adapter`的实现，我们现在`onCreateViewHolder`中获取`view`，然后在`onBindViewHolder` 中绑定数据，搞定

```
public class UserAdapter extends RecyclerView.Adapter<UserAdapter.UserHolder> {

    @NonNull
    private List<User> mUsers;

    @Override
    public UserHolder onCreateViewHolder(ViewGroup viewGroup, int i) {
        View itemView = LayoutInflater.from(viewGroup.getContext())
                .inflate(R.layout.user_item, viewGroup, false);
        return new UserHolder(itemView);
    }

    @Override
    public void onBindViewHolder(UserHolder holder, int position) {
        holder.bind(mUsers.get(position));
    }

    @Override
    public int getItemCount() {
        return mUsers.size();
    }
}
```

[查看源码](https://github.com/LaxusJie/DataBindingSimple/blob/master/app/src/main/java/com/jie/databindingsimple/UserAdapter.java)

## 在开源框架中用法

我们以`BaseRecyclerViewAdapterHelper`为例，在不改底层框架的情况下我们可以这么用，当然如果嫌每次绑定很麻烦我们可以将开源库引进来改源码，把绑定动作放到底层去

```
public class DiscussAdapter extends BaseQuickAdapter<DiscussEntity, BaseViewHolder> {
    FragmentDiscussItemBinding binding;

    public DiscussAdapter() {
        super(R.layout.fragment_discuss_item, null);
    }

    @Override
    protected void convert(BaseViewHolder helper, DiscussEntity item) {
        binding =  DataBindingUtil.bind(helper.itemView);
        binding.setDiscuss(item);
    }

}
```